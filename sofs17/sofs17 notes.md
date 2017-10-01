# **sofs2017**

---

> *The sofs17 is a simple and limited file system, based on the ext2 file system, which was designed for purely educational purposes and is intended to be developed in the practical classes of the Operating Systems course in academic year of 2017/2018. The physical support is a regular file from any other file system.*

---


- Sistema simples e limitado
- Baseado no ext2 
- Suporte físico: um ficheiro regular de outro sistema operativo
	- Este ficheiro será formatado para imitar uma unidade física formatada no formato sofs17

# Organização das aulas durante o sofs17
2 horas: \
- 1h30 : interagir relativamente ao trabalho pendente \
- 0h30 : falar da próxima camada de software \


#1. Introduction
- Durante a execução de um programa, ele manipula informação (produz, acede e/ou modifica).
- Esta informação tem de ser guardada exteriormente (**mass storage**)
	- discos magnéticos
	- discos ópticos
	- SSD
	- ...

- **mass storage** (armazenamento de massa): dispositivos organizados em arrays de blocos
	- 256 bytes até 8 Kbytes por bloco
	- os blocos são numerados sequencialmente (LBA model)
	- o acesso para R/W é efetuado através de um ID (identification number) \

| Block 0 \| | Block 1 \| | Block 2 \| | Block 3 \| | ... | Block NTBK-1 |
|:-----:|:-------:|:-----:|:---:|:-------:|:----:|

Cada bloco tem BKZS bytes de informação
- O acesso ao disco é feito bloco a bloco:
	- **Não é possível modificar um único byte**

---

> _Direct access to the contents of the device **should not be allowed to the application programmer.** _

---

Porque:

- Um sistema de ficheiros é complexo
- A sua estrutura interna precisa de *enforce quality criteria* para garantir:
	- eficiência
	- integridade
	- partilha de acessos
- O utilizador não sabe o conteúdo de cada bloco de dados nem em que blocos a informação do ficheiro x está.

Daí a necessidade/exigência da existência de um *uniform interaction model* (Nível de abstração). \


**ficheiro**: 

- unidade lógica de armazenamento de massa
- *abstract data type*, sobre o ponto de vista do programador
	- composto por um conjunto de atributos e operações
- tipos:
	- NTFS
	- ext3
	- FAT*
	- UDF
	- APFS
	- ...

---

> *Is the operating system’s responsability to provide a set of from the file system point of view: system calls that implement such abstract data type. These system calls should be a simple and safe interface with the mass storage device. The component of the operating system dedicated size — the size in bytes of the file’s data to this task is the file system*

---

Ou seja, operações de leitura e escrita **são sempre efetuadas no contexto de ficheiros**, através de syscall disponibilizadas pelo OS.

A interface de comunicação com o OS é a mesma, mas diferentes sistemas de ficheiros obrigam a diferentes técnicas e manipulação do filesystem, que são transparentes para o programador.

##1.1  File as an abstract data type

Os atributos de um ficheiros dependem da implementação do sistema de ficheiros.

Os mais comuns:

- **name**:
- **internal identifier**: ID númerico (e interno - o user desconhece) que é usado pelo OS para aceder ao ficheiro
- **size**: tamanho do ficheiro em bytes
- **ownership**: Identificação de quêm o ficheiro pertence (usado para controlo de acessos)
- **permissions**: Atributos que em conjunto com a ownership (des)autorizam o acesso ao ficheiro
	- Possíveis permissões:
		- r: read
		- w: write
		- x: execute
		- d: directory
	- Nos diretórios, execução `x` significa que eu tenho permissões para atravessar o diretório (posso não ter permissões nem para ler nem para escrever, mas posso seguir no diretório para chegar a outro path)
- **acess monitoring**: data  do último acesso e última modificação
- **localization of the data**: identificação dos clusters onde os dados do ficheiros estão guardados
- **type**: tipo dos ficheiros: 
	1. ordinary or regular: qualquer ficheiro \"normal\" para o utilizador [ID= - ]
		- .txt
		- .doc
		- .png
		- .avi
		- .mp3
		- .pdf
		- .c
		- .exe
		- ...
	2. directory: um tipo de **ficheiro** interno, com um formato pre-definido, usado para localizar outros ficheiros ou diretórios, permitindo visualizar o sistemas de ficheiros como uma árvore de diretórios e fichieros [ID= d]
	3. shortcut (symbolic link): ficheiro interno, com um formato predefinido, que contém uma referência para outro ficheiro/diretório [ID= s]
		- ref pode ser absoluta ou relativa
	4. character device(**special file**): *represents a device handles in bytes* [ID= c]
	5. block device(**special file**): *rep	esents a device handles in block* [ID= b]
	6. socket(**special file**): *represents a file used for inter-process communication* [ID= s]
	7. named pipe: _**another special file** used for inter-process communication_ [ID = p]

ID (`ls -ll`) | meaning 
:-----------:|:-------:
- | ordinary/regular file
d | directory
s | symbolic link
c | character device
b | block device
s | socket
p | named pipe

No sofs17 só serão considerados os três primeiros tipos de ficheiros.

### Operações em ficheiros
- Dependem do OS
- Todas as operações estão disponíveis **apenas** através de syscalls (funções que funcionam como *entry-points* para o OS
- Syscalls em Linux para os tipos de ficheiros a usar no sofs17:
```c
/*[TODO] Inserir descrição das operações para o teste*/
```
	- Comun aos três:
		- open
		- close
		- chmod
		- chown
		- utime
		- stat
		- rename
	- Comun para **ficheiros regulares** e **shortcuts**:
		- link
		- unlink
 	- Só para **ficheiros regulares**:
		- mknod
		- read
		- write
		- truncate
		- lseek
	- Só para **diretórios**
		- mkdir
		- rmdir
		- getdents
	- Só para **shortcuts**
		- symlink
		- readlink


A descrição destas syscalls pode ser obtida executando num terminal o comando:
```bash
man 2 <syscall>
```

##1.2 FUSE
Inserir um novo filesystem num OS requer:
1. Integração do software que implementa o novo filesystem no kernel
2. Instanciação de um ou mais dispositivos que usam o formato do novo filesystem

> *In monolitic kernels, the integration task involves the recompilation of the kernel, including the sofware that implements the new file system. In modular kernels, the new software should be compiled and linked separalely and attached to the kernel at run time.* 

Tarefa morosa e difícil, que requer _deep knowledge of the hosting system_ - **OUT OF THE SCOPE OF SO**

> FUSE (File system in User SpacE) is a canny solution that **allows for the implementation of file systems in user space** (memory where normal user programs run). Thus, **any effect of flaws of the suporting software are restricted to the user space, keeping the kernel imune to them.**

O novo filesystem é executado em cima do FUSE com permissões de user e não de root. Assim, certas operações que poderiam danificar fisicamente os dispositivos estão interditas e erros no código não geram kernel-panics.

Isola-se a execução deste novo filesystem do kernel.

### Infrastrutura
- **Interface com o filesystem nativo**: funciona como mediador entre as syscalls do sistema nativo e as implementadas em user sapce
- **Implementation library**: 
	- Estruturas de dados
	- Protótipos de funções (que devem ser desenvolvidas pelo user para criar o filesystem específico)
	- Métodos para instanciar e integrar o novo filesystem com o kernel

![FUSE diagram with sofs17](fuse.jpeg)

# 2 SOFS17 Architecture
- Um disco é um conjunto de blocos numerados
	- No sofs17 cada bloco tem 512 bytes
- Os elementos principais na definição da arquitectura do sofs2017 são:
	- **superblock**: estrutura de dados guardada no bloco 0. Contém atributos globais para
		- o disco como um todo
		- outras estruturas de dados 
	- **inode**: estrutura de dados que contém **todos os atributos de um ficheiro, excepto o nome**
		- Existe um região contínua no disco reservada para guardar todos os inodes (inode table)
		- A identificação de um inode é feita com um indíce que representa a sua posição relativa na inode table
	- **directory**: _special file_ que permite a implementação de uma hierarquia (árvore) para acesso aos ficheiros
		- É composto por um conjunto de entradas (*directory entries*) em que cada uma associa um nome a um inode
		- Assume-se que o diretório de raiz (*root*) está associado ao inode 0
	- **disk blocks**: usados para guardar os dados
		- Estão organizados em grupos de 4 blocos contínuos -> **clusters**
		- A identificação de um cluster é dada através de um indíce que identifica a posição relativa do cluster na cluster zone
	- **cluster**: Para cada cluster existe um bit correspondente que representa o seu estado (vazio/preenchido)
		- Estes bits estão guardados no sistema de ficheiros numa área chamada *reference bitmpa table*


De forma geral, os N blocks de um disco formatado em sof17 organizam-se em 4 áreas:

![Organização de um disco formatado em sofs17](sofs17_disk.png)

## 2.1 List of free inodes
- O número de inodes num disco sofs17 é **fixo após a formatação**.
- Quando um novo ficheiro é criado, deve-lhe ser atribuido um inode. Para isso é preciso:
	- Definir uma política (conjunto de regras) para decidir que free inode será usado
	- Definir e guardar no disco uma estrutura de dados adequada à implementação desta política


> _In sofs17 a FIFO policy is used, meaning that the first free inode to be used is the oldest one. The
implementation is based in a double linked list of free inodes, built using the inodes themselves._

- Na estrutura de inodes, existem dois campos que guardam os indíces do próximo inode e do inode anterior vazios.
- Estas listas de inodes são circulares, ou seja:
	- O _previous_ inode livre do primeiro free inode é o último free inode
	- o _next_ free inode do último inode é o primeiro free inode
- No _superblock_, dois dois campos guardam o número total de free inodes e um indíce para o primeiro free inode

## 2.2 List of free clusters
- Tal como os inodes, o número de clusters num disco é fixo após a formatação.
- Para manipular a estrutura de clusters é necessário:
	- Definir uma maneira de representar o estado (livre/usado) de todos os clusters no disco
	- Definir uma política para decidir que cluster (que estea livre) deve ser usado quanto é necessário um cluster
	- Definir e guardar no disco uma estrutura de dados adequada para representar o sistema de clusters e permitir a implementação dos pontos acima


Concretamente no sofs17: \
- Existe uma estrutura de _bit map_ unívoca que mapeia o estado de um cluster 
	- Esta estrutura é formada por um blocos contínuos no disco (logo após a _inode table_)
	- Cada bit funciona como uma variável booleana que classifica o cluster que referencia como vazio ou ocupado
- Existem duas caches guardadas no superblock que são usadas para guardar referências diretas para os clusters. São:
	- **retrieval cache**: 
	- **insertion cache**: 
- Considera-se que um cluster está livre nas seguintes condições 
	- A sua referência está em qualquer uma das caches 
	- Ou o seu bit correspondente no bit map indica que está vazio

- As duas caches têm como função melhorar a eficiência de operações de **alocação**(atribiuir um cluster livre a um novo ficheiro a ser guardado em disco) e **libertação**(remover as referências para um dado cluster).
	- Na maioria das vezes as duas operações só precisam de aceder ao superblock e não fazem mais di que um acesso ao disco

![Caches and Reference bitmap blocks](cache.png)

### Allocation
1. Uma referência para um cluster livre é obtida da retrieval cache
	1. Se a cache tiver vazia, são transferidas várias referências do bit map para a cache antes de se obter a referência para o cluster livre
	2. As referências transferidas para os clusters são transferidas de forma sequencial
2. Um byte iglobal (guardado no superblock) indica a localização no bit map de onde a transferência deve começar 
	1. Assim cria-se rotatividade no uso dos clusters
3. Os clusters não funcionam estritamente como uma FIFO.
	1. Se a _retrieval cache_ e o bit map estão vazios, as referências presentes na inserion cache são transferidas da insertion cache para o disco
	2. Se se efetua uma release operation a referência para o novo cluster livre é inserida na inserion cache
	3. Se esta cache está cheia, então as referências para a cache são transferidas para o bit map, antes de se proceder como anteriormente


## 2.3 List of clusters used by a file (inode)

---

> _**Clusters are not shared among files, thus, an in-use cluster belongs to a single file**_

---

O número de clusters usado por um ficheiro é  $N_c = roundup(\frac{size}{ClusterSize})$, onde: \ 

- **size**: tamanho em bytes de um ficheiro
- **ClusterSize**: tamanho de um cluster em bytes

### Considerações:
- $N_c$ pode ser muito elevado.
	- Um disco com um block size de 512 bytes e com um _cluster size_ de 4 blocos. Se o ficheiro a guardar tiver 2 GByte são necessários 1 milhão de clusters
- $N_c$ pode ser nulo (0):
	- Se o ficheiro tiver 0 bytes, $N_c$ = 0

---

> _Thus, it is impractical that all the clusters used by a file are contiguous in disk. The data structure used to represent the sequence of clusters used by a file must be flexible, growing as necessary._

---

A **escrita** e a **leitura** no disco **não são sequenciais**, mas sim aleatórias.

Exemplo: pretendemos aceder ao indice _j_ de um ficheiro. Para obter o cluster que contém esse ficheiro precisamos de saber o indice do cluster do ponto de vista de um ficheiro, $ClusterIndex = \frac{j}{ClusterSize}$ 

Para obter a localização do ficheiro no disco, temos de obter o número do cluster usando a  estrutura do filesystem.

No sofs17: \
- a _data strucuture_ definida é dinâmica e permite uam identificação rápida de qualquer data cluster.
- Cada inode permite o acesso a um array dinâmico, _**d**_, que identifica a sequencia de clusters usados para guardar os dados associados com um ficheiro.
- Sendo _**ClusterSize**_ o tamanho em bytes de um cluster, temos:
 
|Cluster|Descrição|
|:---:|:---|
|d[0]| Número do cluster que contem os primeiros ClusterSize bytes |
|d[1]| Número do cluster que contem os segundos ClusterSize bytes |
| . | ... |
| . | ... |
|d[$N_c -1$]| Número do cluster que contém os últimos ClusterSize bytes|

O array _**d**_ não é guardado num único local: \
- Os **primeiros 6 elementos** são diretamente guardados no inode, no campo d (referência direta)
- Os **próximos elementos, se existirem, são referenciados através dos campos:
	- **$i_1$**: referência indireta
	- **$i_2$**: referªencia indireta dupla

### Campo $i_1$
- É usado para estender indiretamente o array _**d**_
- O primeiro elemento, $i_1[0]$ é usado para referenciar um cluster onde cada bloco é um endereço para uma posição no disco (cluster) onde estão guardados os dados do ficheiro
- Permite extender o array d de d[6] para d[RPC+6-1]=d[RPC+5]
	- **RPC** é o número de referências para clusters que podem ser guardadas num cluster

### Campo $i_2$
- Se mesmo assim não for possível guardar os dados do ficheiro usando referência indireta simples, pode ser usada referência indireta dupla. 
- O campo $i_2$ do inode é usado para referenciar um cluster em que cada bloco do cluster referenciad um cluster de dados
- É usado para extender o array de referências indiretas $i_1$ usando as referências indiretas do cluster. Assim temos um array de referências indiretas de $i_1[1]$ até $i_1[RPC]$.
- O primeiro cluster do array de referências indiretas duplas é $i_1[1]$ ($i_1[0]$ corresponde às referências diretas).
	- Traduzindo para o array de **_d_** corresponde aos segmentos do array entre d[RPC + 6] e $d[2*RPC + 5]$

### NullReference
- É usada para representar uma referência que não existe
- Exemplos:
	- se d[1] for uma NullReference, o ficheiro não contém o index de cluster 1
	- se $i_1$, representando $i_1[0]$ é equal to NullReference, significa que entre d[6] até d[RPC+5] todos os indices são NullReference e o o ficheiro não contém estes indices
	- se $i_2$ for uma NullReference, significa que entre $i_1[1]$ to $i_1[RPC]$ são NullReferences e portanto d[RPC+6] até d[RPC^2 + RPC+ 5] são NullReferences e o ficheiro não contém esses indices

## 2.4 Directories
- Um diretório pode ser visto como um array de entrada para diretórios.
- No sofs17:
	- Um diretório é uma estrutura de dados composta por um array de bytes com tamanho fixo. Usados para guardar:
		- nome
		- referência que associa o diretório a um inode
	- A estrutura de dados foi definida para que um cluster suporte apenas um número inteiro de diretórios
		- As primeiras duas entradas `"."` e `".."` representam o diretório atual e o diretório pai
		- Um diretório pode tomar um de três estados:
			- **in-use**: contém o nome e o inode number de um ficheiro que existe (seja ele um regular file, diretório ou atalho)
			- **deleted**: o nome contém o 1 e último caracter trocado, passando a ser `\0 name[0:end-1]` (ou seja, uma null string, mas com o nome recuperável). Mantém o slot no diretório.
			- **clean**: Todos os caracteres do nome são `\0` e o reference field é uma NullReference
	- Quando um cluster é adicionado a um diretório, primeiro deve formatado como uma sequência de diretórios de entrada limpas.
		- O tamanho do diretório é sempre um múltiplo do tamanho de um cluster e nunca pode "encolher" (devido à forma como o delete está implementado
		

# 3. Formatting
A operação de formatação deve preencher todos os blocos do disco para criar um disco sofs17 vazio.

Um disco formatado contém: \
 
- root directory \
- duas entradas, `"."` e `".."`, que apontam para o inode 0 (root directory)

A operação de formatação deve: \ 

- escolher o valor apropriado para o número de inodes, o número de clusters e o número de blocos usados pelo bit map
	- tem de ter em consideração o número de inodes especificado pelo utilizador e número total de blocos no disco.
	- todos os blocos no disco devem ser usados. Se não forem usados para outros propósitos devem ser adicionados à inode table
- Preencher a tabela de inodes:
	- inode number 0 é o root directory
	- todos os outros inodes estão livres
	- A lista de inodes livres começa no inode número 1 e termina no último inode
- Preencher o bit map, sabendo que:
	- O cluster 0 está a ser usado pelo diretório root
	- Todos os outros clusters estão livres
- Preencher o root directory, ocupando o cluster número 0
- Preencher com zeros todos os clusters livres, se especificado pela ferramenta de formatação


# 4. Code Structure
A estrutura do código é apresentada abaixo:

![Code Strucuture to be developed](structure.png)

## Rawdisk
Implementa o acesso físico ao disco

## Dealers
Implementam o acesso ao superblock, inodes, bit map e clusters

### sbdealer
Acesso ao superblock

### itdealer
Acesso à inode table e aos inodes

### bmdealer
Acesso às referências da bit map table

### czdealer
Acesso à cluster zone, usando as cluster references

### ocdelaer
Open/close the dealers


## ilayers
Funções intermédias

### inodeattr
Manipular os campos dos inodes

### freelists
Manipular a lista dos inodes livres e a lista de clusters livres

### filecluster
Lidar com os clusters de um inode

### direntries
Lidar com entradas de diretórios


## syscalls
versão das syscalls de sistema adaptadas ao _sofs17_ 

## fusecallbacks
Interface com FUSE

## probing
Biblioteca para debug

## exception
o tipo de exceções lançadas em caso de erro



[AQUI]

## Generic
- ficheiro showsizes é usado para mostrar os valores dos campos de dados

## createDisk
```bash
createDisk <dir> <num_blocks>
```

- createDisk usa o dd

# showblock
Usado para mostrar o conteudo dos blocos de acordo com a indicação
Não sabe a estrutura interna. Vai formatar o que eu lhe der da forma que eu lhe peço

## inodes
- Cada inode tem dois campos que são reservados para fazer uma lista ligada
- A lista é circular. 
- Cada numero da lista paonta sempre para o seguinte. 
- O previous aponta sempre para o elemento aterior.
- Só preciso de saber a tail porque a previous do head é a tail
- O número de inodes por default é [NUM_BLOCKS]/8


```table
in | name
.. | ..
```

Correspondência univoca entre o inode e o nome do ficheiro

- stat <filename> : mostra a estatísticas do ficheiro (filesize, blocks, ID Block, device, inode, links e datas de aceso, modificação e change)
	- Ficheiro `.` : diretório atual
	- Ficheiro `..` : diretório atual

Na raiz o `..` aponta para a raiz (não existem entradas anteriores

## Comandos para executar
./showblock /tmp/zzz -i 1
./createBlock
./testtools

## Retrieval Chache
Se o disco tiver vazio, a referência deve ser max e não 0.
o 0 significa que está cheio

## Insertion cach
Se o disco tiver vazio, a referência deve ser 0.
o 0 significa que a insertion cahce esta vazia

## Testtools
Permite testar o sistema de ficheiros

## Notes
função alloc cluster tem de verificar se ficou tudo bem no disco
função replentish transfer da reference bitmao block para a retrieval cache


---

26/09/17

## Camada obrigadória:
- ilayers :
- freelist : lida com as listas livres 
- fileclsuters : lida com os files clsters associado a um ficheiro
- inodeatri : lida com a atribuição de atributos de forma especial
- 

## Camada opcional
- dealers (caso pretenda ter notas mais altas)
24 syscalls - cada grupo faz 6 (1 por cada um dos elementos)

## Tree view
### bin

### doc
- não fornece a documentação
- fornece meios para gerar a documentação

### lib
- Bibliotecas binárias para todos os ficherios

### src 
### dataypes

### dealers

### freelayers

### mksofst (formatador)
- CS :
- FCT :
- IT :

- RC _ reset clusters

### probing

### rawdisk

### sofsmount

### syscalls
(fazer 6 das 12)

### test
- showblock
- testtool

## test
- Qualquer script de apoio ao teste das funções que se faça deve ser incluído nesta pasta **não na bin**
- **Vai ser avaliado**
- os scripts de teste podem ser usados para a apresentação do trabalho

## Gerar documentação
```bash
doxygen #correr os ficheiros .h e gerar a documentação
```

Abrir o ficheiro index.html

## Makefile
```bash
make <args>
```

target: depencies
	actions

Um objetivo : de quem é que ele depende
	O que faz

---

O nosso trabalho vai ser alterar os ficheiros
Todos eles funcionam porque chamam o ficheiro binário
O processo de compilação já está todo feito

---

## NOTES
- Nada funciona antes haver formatador
- Posso trabalar ao nível do bloco -> debugging
	- usar showblock
	- fill in superblock
- ferramenta testool permite verificar tudo
	- alocar inodes
	- invocar individualmente cada uma das funções
	- invocar o showblock e verificar se aquilo que fiz está bem fieto
	
## Dois níveis de trabalho
- Tenho sempre de criar um disco formatado em sofs17
1. Usar o testtool e trabalhar diretamente dentro das ferramentas
2. Criar uma pasta e montar o disco nessa pasta
	- O diretório de montagem passa a ser o entry do meu disco (formatado em sofs17)
	- o numéro de inodes muda:
		- de 4096 (ext4) para 2048 (sofs17)
	- Posso navegar com um gestor de ficheiros normal no diretório montado
	- posso efetuar operações de có
```bash
# criar um ficheiro demasiado grande
for i in #seq(1 10)
do
cat /etc/passwd >> /tmp/mnt/zzz
done
```
	- Já tenho um ficheiro que obriga a usar referênciação indireta
	- A lista ligada está por ordem porque foi formatado
	- Após uso os inodes não estão necessariamente por ordem
	- Se o inode for apagado vai para o fim da lista e só após o sistema percorrer tudo é que o inode volta a ser usado
	- 
	```bash
		showblock /tmp/zzz -r 64-67
		# -h print help
		# -r show block as cluster references
	```
	- Posso consultar em ascii (não formatado por linhas (ignora o `\n`)
## Montar o disco
```bash
./sofsmount <disk_file> <mounting_point> 
```

QUando monto o disco, o <mounting_point> é alterado para o tipo de filesystem e altera o número de inodes e clusters da pasta <mounting_point> para os que estõa no filesystem
## probing
- printf que posso configurar ou não para não aparecer
- as syscalls estão em camadas
```bash

```
- Por uma questão de consistência do disco é importante que não tenha nenhuma cópia de inodes e superblocks no disco
	- Para isso é qe existe o dealer
		- devolve um pointer para a parte física do disco
		- todos as funçṍes operam na mesma parte do disco (R/W) direto
32768 - identificação de um ficheiro regular pelo OS

|:Color code:|:
azul - código nosso que foi executado
vermelho - foi chamado um binário do professor (não pode pertencer na versão final)
amarelo - código opcional
verde - código open-source que o professor dá


	
## Dismount
para desmonatr o disco
```bash
# dismount 
fusermount -u </mounting/point/>

# mount
./sofsmount /tmp/zzz /tmp/mnt

# No ato de montagem posso ativar o probing 
./sofsmount /tmp/zzz /tmp/mnt -d -l<start,stop>

# Quando monto o disco o OS dá disown ao terminal e portanto o software não imprimir para o terminal
```
---

# 29 Sep 2017

## Typical Computational system
- Processing unit
- terminal controller
	- graphic card
	- keyboard
	- mouse
- main memory

## Simplified view
- SO : sistema base instalado no computador
	- usável e que "fale" de forma abstrata

Podem existir dois tipos de sistemas operativos:
1. **gráficos** : com janelas, maioritariamente manipuladas recorrendo a um rato
	- elementos principais de interação são os icones e menus
	- main input tool : mouse
2. **textual** : comandos introduzida através di teclado
	- uso de linguagens de comandos para criar comandos complexos

Não são mutuamente exclusivo

Windows :  grafico e pode lançar aplicação para ambiente textual
Linux   : textual e pode lançar ambiente gráfico

## Sistema Operativo:
- Aplicações
- System programs
- operating system
- hardware

Duas perspetivas:
	- extended machine
	- resource manager

### Extended machine
Fornece níveis de abstração (API) para aceder a partes físicas do disco \
Cria uma máquina virtual : visão virtual do meu computador real \

Acesso através de system calls \
- Executa o core da sua função em root (com permissões de super user)
- Existem funções que só podem correr em super user
- Todas as chamadas ao sistema são interrupções

|:USER:|
|KERNEL|
|HARDWARE|

O sistema operativo controla o espaço de endereçamento fisico criando uma camada de abstração (memória virtual)

### Tipos de funções da extended machine
- Criar um ambiente de interface para o utilizador
- Disponibilizar mecanismos para desenvolver, testar e validar programas
- Disponibilizar mecanismos que controlem e monitorizem a execução de programas, incluindo a sua intercomunicação e e sincronização

## Operation system as a resource manager
Um sistema computacional é composto por um set de recursos (processador(es), memória principal, memória secundária, I/O device controllers) 


## Evolution of operational systems
Primórdios : Sistema Electromecânco

## 1. Geração
1945 - 1955

##$ Technology
- Vacuum tubes
-electromechanical relays

###2. Notes
-No operating system
-progra,med in system


###3. Program has fukk control of the machine

### 4. Cartões perfurada (ENIAC)

#### Transisteosres individuais


|:----:|:-----:|
| Technology | kernal |
| ... | ... |


## 4th Generation (1980 - present)
|:----:|:-----:|
|Technology|Notes|
| LSI/VLSI | Standard Operation systems (MS-DOS, Macintosh, Windows, Unix) |
| personal computers (microcomputers) | Network operation systems |
| network | |


```C
// Inserir slides
```

## 5th Generation (1990 - present)
|:----:|:---:|
|Technology|Notes|
|Broadband, wireless | mobile operation systems (Symbian, iOS, Android)|
|system on chip|cloud computing|
|smartphone|ubiquitous computing|

```C
// Inserir Slides
```



# so1718 - Aula prática 29 Sep
# Gerar documentação
```bash
cd ./doc
doxygen
```

A documentação fica na pasta `./doc/html/`
O make compila sempre tudo e não somente o conteudo da pasta

# Make
```bash
make
make -C <path_to_start> 	% indica o caminho onde o make começar
```

Na linkagem necessita da biblioteca fuse.h. Está contida na biblioteca libfuse-dev que pode ser instalada com:
```bash
sudo apt-get install libfuse-dev
```

[TODO] mksofs
- msksofs : formatador para o sistema de ficheiros sofs17

Para já as funções


- compute structure:
	- nao altera os dados no disco.
	- Apenas calcula os blocos de inodes, clusters, etc.
- cada função vai preencher a àrea do disco respetiva
	- **fillInSuperBlock** : computes the structural division of the disk
	- **fillInInodeTable** :


## Criar um disco
```bash
./createDisk /tmp/so # a pasta tmp é dada reset a cada reboot
```

```bash
# permite me olhar para uma sequência ou range de um discod da forma que eu quiser
./showblock

# conteudo interno do bloco 0 em hexadecimal formatado considerando que é um valor hexadecimal
./showblock /tmp/so -x 0
```

|:----:|:-----:|
| -h   | help |
|  -x  | hex |
| -s   | superblock |
| -b   | bits

## formatar o disco
./mksofs.bin /tmp/zzz

|:----:|:-----:|
| -h   | help |
| no args | default values |
| -z   | clusters não alterados são colocados a zero |

## Compute struxture
Recebe como argumento o número de inodes e o número de blocos


Tenho o disco com 1000 blocos
Um dos blocos é usado para super bloco
Quero 125 inodes    


Variável em bytes
Referências armazenadas dos clusters em uso: 1 bit


Pela `./showsizes` tenho: \
- InodesPerBlock: 8
- BlockPerCluster: 

# Inodes ?
QUero gardar um array qyue seja binario
Preciso de uma estrutura dinâmica que escreva sobre a inode

Cada grupo tem 4 inode dentro de um cluster.
NO inode, caso o tamanho do ficheiro ultrapssa os  
bytes

/ Cada referência sãp 4 b?

Existe 6 bits no cluster para referencia ireta e 1 para apontar para um cluster com referênica indireta

inode has 6 positions to save references d[0 to 5] : referência direta
inode [i1] identifica o cluster C1; Extende o espaço de armazentamentom tempo 512 referências diretas para clusters. d[6, 517)
inode [i2] identifica o cluster C2;
inode [i3] extende o cluster C2 onde os elementos são referências diretas para clusters

Capacidade: (6+ 2^9 + (2^9)^2)x 2^11

NO mapa de bits posso ter 4064 bits para identificar clusters
4064 bits posso identificar 4*4096 clusters, logo é mais do que suficiente (só tenho 999 clusters)

# Number of blocks for 125 inodes
125 / 8 
45    15
  1

125 / 8 = 15.62 = 16

1000 inodes - 16 inodes - 1 (superblock) = 983

Número de clusters: 
882 / 4 
18    245
 22   
   2

No block tem 2 campos de dois bytes. 


Cada ficheiro tem um inode. O número máximo de inode é o número máximo de ficheiros

(ReferencePerBitmapBlock: 500

Magic number representa se o sistema é big endian ou little endian

Sofs17 é little endiam - bit menos significativo à esquerda

## Gradim
disk with 1000 blocks
1 superblock
number of inodes = 125

size per iNode = 64 bytes = 8 inodes per block
how many blocks for 125 inodes -> 1254/8 = 15.62... = 16
since 16 blocks can allocate 128 inodes
Total block used so far = 1 for sb + 16 for inodeTable = 17

clusters_per_bitmpa = 4064
1 block for bit map allows 4064 clusters
numberOfBlocks/4 =_ number_of_cluster REM (U_8) unused-blocks -> allocate has inodes
128 inodes * U_8 * iNodesPerBlock = 128+16=144

TOdos os nomes dos ficheiros são null terminated `('\0')`


Na formatação não vale apenas só guardar ponto. Também é preciso guardar 


