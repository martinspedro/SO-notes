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


# Introduction
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

## File as an abstract data type

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

## FUSE
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

![FUSE diagram with sofs17](Pictures/fuse.jpeg)

# SOFS17 Architecture
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

![Organização de um disco formatado em sofs17](Pictures/sofs17_disk.png)

## List of free inodes
- O número de inodes num disco sofs17 é **fixo após a formatação**.
- Quando um novo ficheiro é criado, deve-lhe ser atribuido um inode. Para isso é preciso:
	- Definir uma política (conjunto de regras) para decidir que free inode será usado
	- Definir e guardar no disco uma estrutura de dados adequada à implementação desta política

---

> _In sofs17 a FIFO policy is used, meaning that the first free inode to be used is the oldest one. The
implementation is based in a double linked list of free inodes, built using the inodes themselves._

---

- Na estrutura de inodes, existem dois campos que guardam os indíces do próximo inode e do inode anterior vazios (criam uma lista ligada)
- Estas listas ligadas de inodes são circulares, ou seja:
	- O _previous_ inode livre do primeiro free inode é o último free inode
	- o _next_ free inode do último inode é o primeiro free inode
	- Assim:
	- Cada numero da lista paonta sempre para o seguinte. 
	- O previous aponta sempre para o elemento aterior.
	- Só preciso de saber a tail porque a previous do head é a tail
- No _superblock_, dois campos guardam o número total de free inodes e um indíce para o primeiro free inode
- O número de inodes por default é [NUM_BLOCKS]/8


Correspondência univoca entre o inode e o nome do ficheiro

- stat <filename> : mostra a estatísticas do ficheiro (filesize, blocks, ID Block, device, inode, links e datas de aceso, modificação e change)
	- Ficheiro `.` : diretório atual
	- Ficheiro `..` : diretório atual


## List of free clusters
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


### Retrieval Chache
Serve para guardar as referências após eliminar um ficheiro. Se o disco tiver vazio, a referência deve ser max e não 0. O valor 0 significa que está cheio a retrieval cach está cheia.

### Insertion cache
Serve para guardar as referências de ficheiros a inserir. Se o cache estiver vazia, a referência deve ser 0. O valor 0 significa que a insertion cache está cheia.


![Caches and Reference bitmap blocks](Pictures/cache.png)

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


## List of clusters used by a file (inode)

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
e
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

## Directories
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
		

# Formatting
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


# Code Structure
A estrutura do código é apresentada abaixo:

![Code Strucuture to be developed](Pictures/structure.png)

## Rawdisk
Implementa o acesso físico ao disco

## Dealers
- Implementam o acesso ao superblock, inodes, bit map e clusters
- São opcionais (só são feitas se os alunos desejarem ter notas mais altas)

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
Obrigatórias

### inodeattr
Lida com a manipulação dos campos especiais dos inodes

### freelists
Manipular a lista dos inodes livres e a lista de clusters livres

### filecluster
Lidar com os clusters de um inode (file clusters associados a um ficheiro)

### direntries
Lidar com entradas de diretórios


## syscalls
versão das syscalls de sistema adaptadas ao _sofs17_ 
Cada grupo Só irá implementar 6 das 24 utilizadas.

## fusecallbacks
Interface com FUSE

## probing
Biblioteca para debug

## exception
o tipo de exceções lançadas em caso de erro




