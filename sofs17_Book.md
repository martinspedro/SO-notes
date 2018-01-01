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




- **datatypes**: um conjunto de constantes que podem ser usadas para aceder aos ficheiros
	- InodesPerBlock
	- ReferencesPerBlock
	- ReferencesPerCluster
	- ReferencesPerBitmapBlock
	- BlocksPerCluster
	- CLusterSize
	- DirentriesPerCluster
	- NullReference


# createDisk

- Cria um disco **não formatado** que serve de suporte a um sistema de ficheiros.
- Na prática, um disco é um ficheiro que possui uma estrutura de blocos fixa.

- Apenas é garantida que a estrutura do disco possui:
	- o número desejado de clusters
	- o número desejado de bytes por cluster

- Para o disco ser um sistema de ficheiros válido é necessário formatá-lo com ferramentas adequadas para o tipo de sistemas de ficheiros pretendido 

## Exemplo de utilização
```bash
./createDisk <diskfile> <numblocks>
```

O _output_ após a execução do script para um disco com 1000 blocos é:

```bash
./createDisk <diskfle> 1000
1000+0 records in                               
1000+0 records out
512000 bytes (512 kB) copied, 0.05734 s, 8.9 MB/s
``` 

## Implementação
O createDisk usa o comando [_dd_](https://en.wikipedia.org/wiki/Dd_(Unix) "Wikipedia page") para escrever para o disco/ficheiro e preenche-o com valores aleatórios obtidos do _/dev/urandom_.

```bash
#!/bin/bash                
           
if [ $# != 2 ]; then
        echo "$0 diskfile numblocks"
        exit 1                     
fi
 
dd if=/dev/urandom of=$1 bs=512 count=$2
```


---
geometry: margin=1.5cm
fontsize: 10pt 
---
# showblock
- Permite visualizar a informação contida numa sequência de blocos do disco:
	- Os dados dos blocos podem ser formatados para serem facilmente interpretáveis por humanos
	- A formatação dos dados dos blocos pode ser feita de acordo com a função de cada um dos blocos

## Utilização
```bash
# showblock -h imprime a ajuda
./showblock [ OPTION ] <disk filename>
```

### Opções de Visualização
| Option | Description |
|:----:|:-----|
| -x | show block(s) as hexadecimal data |
| -a | show block(s) as ascii/hexadecimal data |
| -s | show block(s) as superblock data |
| -i | show block(s) as inode entries |
| -d | show block(s) as directory entries |
| -r | show block(s) as cluster references |
| -b | show block(s) as bitmap references |

## Exemplos
```bash
# Showblock para o bloco 1 em hexadecimal
./showblock -x 1 disk.sofs17
0000: fd 41 02 00 e8 03 00 00 e8 03 00 00 00 08 00 00 01 00 00 00 dc ee f9 59 dc ee f9 59 dc ee f9 59
0020: 00 00 00 00 ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff
0040: 00 02 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 02 00 00 00 8f 00 00 00
0060: ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff
0080: 00 02 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 03 00 00 00 01 00 00 00
00a0: ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff
00c0: 00 02 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 04 00 00 00 02 00 00 00
00e0: ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff
0100: 00 02 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 05 00 00 00 03 00 00 00
0120: ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff
0140: 00 02 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 06 00 00 00 04 00 00 00
0160: ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff
0180: 00 02 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 07 00 00 00 05 00 00 00
01a0: ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff
01c0: 00 02 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 08 00 00 00 06 00 00 00
01e0: ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff

# A informação não é diretamente percetível por humanos. 
# Sabendo que este bloco corresponde ao primeiro bloco da inode table, 
# se executarmos o mesmo comando mas o output vier formatado para inodes, temos:
./showblock -i 1 disk.sofs17
Inode #0
type =  directory, permissions = rwxrwxr-x, lnkcnt = 2, owner = 1000, group = 1000
size in bytes = 2048, size in clusters = 1
atime = Wed Nov  1 15:57:16 2017, mtime = Wed Nov  1 15:57:16 2017, ctime = Wed Nov  1 15:57:16 2017
d[] = {0 (nil) (nil) (nil) (nil) (nil)}, i1 = (nil), i2 = (nil)
----------------
Inode #1
type = free clean, permissions = ---------, lnkcnt = 0, owner = 0, group = 0
size in bytes = 0, size in clusters = 0
next = 2, , prev = 143
d[] = {(nil) (nil) (nil) (nil) (nil) (nil)}, i1 = (nil), i2 = (nil)
----------------
Inode #2
type = free clean, permissions = ---------, lnkcnt = 0, owner = 0, group = 0
size in bytes = 0, size in clusters = 0
next = 3, , prev = 1
d[] = {(nil) (nil) (nil) (nil) (nil) (nil)}, i1 = (nil), i2 = (nil)
----------------
Inode #3
type = free clean, permissions = ---------, lnkcnt = 0, owner = 0, group = 0
size in bytes = 0, size in clusters = 0
next = 4, , prev = 2
d[] = {(nil) (nil) (nil) (nil) (nil) (nil)}, i1 = (nil), i2 = (nil)
----------------
Inode #4
type = free clean, permissions = ---------, lnkcnt = 0, owner = 0, group = 0
size in bytes = 0, size in clusters = 0
next = 5, , prev = 3
d[] = {(nil) (nil) (nil) (nil) (nil) (nil)}, i1 = (nil), i2 = (nil)
----------------
Inode #5
type = free clean, permissions = ---------, lnkcnt = 0, owner = 0, group = 0
size in bytes = 0, size in clusters = 0
next = 6, , prev = 4
d[] = {(nil) (nil) (nil) (nil) (nil) (nil)}, i1 = (nil), i2 = (nil)
----------------
Inode #6
type = free clean, permissions = ---------, lnkcnt = 0, owner = 0, group = 0
size in bytes = 0, size in clusters = 0
next = 7, , prev = 5
d[] = {(nil) (nil) (nil) (nil) (nil) (nil)}, i1 = (nil), i2 = (nil)
----------------
Inode #7
type = free clean, permissions = ---------, lnkcnt = 0, owner = 0, group = 0
size in bytes = 0, size in clusters = 0
next = 8, , prev = 6
d[] = {(nil) (nil) (nil) (nil) (nil) (nil)}, i1 = (nil), i2 = (nil)
----------------
```

# rawlevel
- Camada que permite a manipulação do disco ao nível do bloco

## Módulos
- **mksofs**  : Formatador
- **rawdisk** : Acesso aos blocos do disco


 
# rawdisk
- Permite o acesso aos blocos do disco
	- Os blocos são a menor unidade lógica no _filesystem_
- Medeia o acesso direto ao disco, impedindo que ocorram erros que podem danificar a estrutura do sistema de ficheiros

## Macros
```c
// block size (in bytes)
#define BlockSize (512U)
```

## Funções
```cpp
void soOpenRawDisk (const char *devname, uint32_t *np=NULL)
```

- Abre o dispositivo de armazenamento, criando um canal de comunicação com esse dispositivo
	- Supoem que o dispositivo está fechado e mais nenhum canal de comunicação para esse dispositivo está aberto
	- O dispositivo de armazenamento tem de existir
	- O dispositivo de armazenamento tem de ter um tamanho múltiplo do block size


###  
```cpp
void soCloseRawDisk (void) 
```

- Fecha o dispositivo de armazenamento e o canal de comunicação.


###
```c++
void soReadRawBlock(uint32_t n, void *buf)  
```

- Lê um bloco de dados do dispositivo
- **Parametros:**
	- _n_: número físico do bloco de dados no disco de onde a informação vai ser lida
	- _buf_: ponteiro para o buffer para onde os dados vão ser lidos


###
```cpp
void soWriteRawBlock ( uint32_t  n, void * buf)
```

- Escreve um bloco de dados do dispositivo
- **Parametros:**
	- _n_: número físico do bloco de dados no disco onde a informação vai ser escrita
	- _buf_: ponteiro para o buffer que contém os dados a ser escritos
# msksofs
- Script responsável por formatar o disco
- Cria um disco utilizável
	- Manipula os blocos do disco para implementar o _sofs17 filesystem_

## Utilização

```bash
USAGE:
Sinopsis: mksofs [OPTIONS] supp-file
  OPTIONS:
  -n name --- set volume name (default: "sofs17_disk")
  -i num  --- set number of inodes (default: N/8, where N = number of blocks)
  -z      --- set zero mode (default: not zero)
  -q      --- set quiet mode (default: not quiet)
  -h      --- print this help
```

- Posso usar mais do que uma das opções na mesma execução
## Exemplos

### No Options
- Basta indicar o número do ficheiro
- Por default, o número de inodes é o número de clusters/8

```bash
./mksofs ../disk.sofs17

Trying to install a 125-inodes SOFS17 file system in ../disk.sofs17.
Computing disk structure... 
Filling in the superblock fields... 
Filling in the table of inodes... 
Filling in the bitmap of free clusters... 
Filling in the root directory... 
144-inodes SOFS17 file system was successfully installed in ../disk.sofs17.
```

### Set name
```bash
$ ./mksofs.bin64 disk.sofs17 -n "my disk"

Trying to install a 125-inodes SOFS17 file system in disk.sofs17.
  Computing disk structure... done.
  Filling in the superblock fields... done.
  Filling in the table of inodes... done.
  Filling in the bitmap of free clusters... done.
  Filling in the root directory... done.
A 144-inodes SOFS17 file system was successfully installed in disk.sofs17.

$ ./showblock disk.sofs17 -s 0
Header:
   Magic number: 0x50F5
   Version number: 0x2017
   Volume name: my disk
   Properly unmounted: yes
   Number of mounts: 0
   Total number of blocks in the device: 1000
Inode table metadata:
   First block of the inode table: 1
   (...)
```

### Set inodes
- O formatador tenta formatar o disco para o número desejado de inodes

```bash
./mksofs.bin64 disk.sofs17 -i 2000

Trying to install a 2000-inodes SOFS17 file system in disk.sofs17.
  Computing disk structure... done.
  Filling in the superblock fields... done.
  Filling in the table of inodes... done.
  Filling in the bitmap of free clusters... done.
  Filling in the root directory... done.
A 2000-inodes SOFS17 file system was successfully installed in disk.sofs17.
```

- Pode não ser possível formatar o disco para o número desejado de inodes
- Nesse caso, o formatador usa o número de inodes possível imediatamente superior ao pretendido

```bash
./mksofs.bin64 disk.sofs17 -i 100

Trying to install a 100-inodes SOFS17 file system in disk.sofs17.
  Computing disk structure... done.
  Filling in the superblock fields... done.
  Filling in the table of inodes... done.
  Filling in the bitmap of free clusters... done.
  Filling in the root directory... done.
A 112-inodes SOFS17 file system was successfully installed in disk.sofs17.
```

### Zero Mode
- Ao usar a opção `-z` todos os clusters livres são preenchidos com zeros

```bash
./mksofs ../disk.sofs17 -z

Trying to install a 125-inodes SOFS17 file system in ../disk.sofs17.
Computing disk structure... 
Filling in the superblock fields... 
Filling in the table of inodes... .
Filling in the bitmap of free clusters... 
Filling in the root directory... 
Filling in free clusters with zeros... cstart: 24, ctotal: 244
A 144-inodes SOFS17 file system was successfully installed in ../disk.sofs17.
```

# computeStruture
- Calcula a divisão da estruturas no disco
	- número de clusters
	- número de blocos para inodes
	- número de blocos para reference map
- 

## Algoritmo
- No mínimo têm de existir 6 blocos no disco
	- 1 superblock
	- 0 inodes
	- 1 reference map
	- 1 cluster de dados
- Por default o número de inodes é $N_{inodes} = \frac{N_{clusters}}{8}$
- Caso o número de inodes não seja divisível por 8, é preciso alocar mais um bloco para os inodes
- O número temporário de blocos livres (falta o reference map) é: $$N_{blocos disco} - N_{blocos inodes} - 1$$
	- o '1' corresponde ao superblock
- O número de clusters é o resultado da divisão do número de blocos livres pelo número de blocos por cluster
- Através do número de clusters pode ser estimado o número de blocos necessários para a reference map
- Depois dessa estimativa é possível calcular o número de blocos restantes e atribuí-los à inode table

## Utilização

```c
void computeStructure( 	uint32_t  	ntotal,
						uint32_t  	itotal,
						uint32_t * 	itsizep,
						uint32_t * 	rmsizep,
						uint32_t * 	ctotalp 
					 ) 		
```

### Parameters
- **ntotal:** total number of blocks of the device
- **itotal:** requested number of inodes
- **itsizep:** pointer to mem where to store the size of inode table in blocks
- **rmsizep:** pointer to mem where to store the size of cluster reference table in blocks
- **ctotalp:** pointer to mem where to store the number of clusters

## Testes
### 1000 blocos, 125 inodes (nblocos/8)
- Começamos por calcular o número de blocos necessários para os inodes 
	- Existem 8 inodes por bloco

```
125 / 8 
120   15
  5
```
- Obtemos 15 blocos para inodes 
- E 5 blocos que sobram
- Se permitimos que 4 sejam usados para um cluster, temos 16 inodes
- O número de clusters é $1000 blocos - 16 inodes - 1 superblock = 983 blocos$

- O número de clusters para dados é:
```
983 / 4 
980   245
  3  
```

- Passamos a ter um sistema de ficheiros $15 + 3 = 18 blocos para inodes$
	- Isto equivale a ter $18 \times 8 = 144 inodes$ e não os 125 como inicialmente se desejava


# fillInSuperBlock
- Preenche os campos dos superblock
- O _magic number_ deve ser 0xFFFF
- As caches estão no _superblock_

## Algoritmo
- Atribuições a serem feitas:
	- o magic number (identifica se o sistema é Big-Endian ou Little-Endian)
	- _version number_
	- nome do disco
		- Tem de ser truncado caso ultrapasse o _PARTITION_NAME_SIZE_
	- Número total de blocos
- Indicar que o disco ainda está unmounted
- Reset ao número de mounts
- Inode table metadata
	- **itstart:** Bloco onde começa a tabela de inodes
	- **itsize:** Número de blocos da inode table
	- **itotal:** Número total de inodes
	- **ifree:** Número de inodes livres
	- **ihead:** Índice para a head do primeiro inode 
- Free Cluster table metadata
	- **rmstart:** bloco onde começa a reference map
	- **rmsize:** número de blocos usados pela reference table
	- **rmidx:** Primeira referência (_root dir_)
- Clusters metadata
	- **czstart:** bloco onde começa a cluster zone
	- **ctotal:** número total de clusters
	- **cfree:** número de clusters livres
- Retrieval cache
	- Inicializar com NullReferences
	- idx -> última posição da cache
- Insertion cache
	- Inicializar com NullReferences
	- idx -> primeira posição da cache

## Utilização
```c
void fillInSuperBlock( 	const char * name,
						uint32_t  	 ntotal,
						uint32_t  	 itsize,
						uint32_t  	 rmsize 
					 ) 		
```

### Parameters
- **name:**	volume name
- **ntotal:** the total number of blocks in the device
- **itsize:** the number of blocks used by the inode table
- **rmsize:** the number of blocks used by the cluster reference table


# fillInInodeTable
- Preenche os blocos da inode table
- O inode **0** deve ser preenchido considerando que está a ser usado pelo diretório raiz
- Todos os outros inodes estão livres

## Algoritmia
- Para cada bloco da inode table
	- Criar a lista biligada
	- Referências para os clusters:
		- Preencher com NullReference as _direct references (d)_
		- Preencher com NullReference as _indirect references (i1)_
		- Preencher com NullReference as _double direct references (i2)_
- Inicializar o inode da root directory (_inode 0_)
	- **mode:** permissões
	- **lnkcnt:** link count - número de caminhos que chegam a este (2: `., ..`)
	- **owner:**
	- **group:**
	- **size:** Tamanho do inode (1 cluster)
	- **clucnt:** file size in bytes
	- Modificar access times
	- Apontar para o root dir usando as referências diretas (_d[0])

## Utilização
```c
void fillInInodeTable( 	uint32_t itstart,
						uint32_t itsize 
					 ) 		
```

### Parameters
- **itstart:** physical number of the first block used by the inode table
- **itsize:** number of blocks of the inode table



---
geometry: margin=1.5cm
fontsize: 10pt
---

# fillInFreeClusterTable
- Preeche a Free Cluster Table:
	- Estrutura que indica se os clusters estão livres ou ocupados
- Existe uma correspondência unívoca entre bits na _reference cluster table_ e clusters no disco
- Os bits na tabela de referências crescem da:
	- Lower blocks to upper blocks
	- Lower bytes to upper bytes
	- Most Significant Bytes (MSB) to Least Significant Bytes (LSB)
- Assim, o bit '0' corresponde ao bit mais significativo do primeiro byte do primeiro bloco da tabela de bitmap
- Valor do bit:
	- **'1':** Cluster Livre
	- **'0':** Cluster Ocupado
- Em geral, o número de bits na tabela é maior que o número de clutsers
	- Os bits não usados (ou seja, que não correspondem ao estado de nenhum cluster) devem ser inicializados como se fosse usados
- Pode existir mais do que um bloco para a reference map table

## Algoritmo
- Começa-se por definir algumas constantes:

```c
#define CLUSTER_IN_USE 0
#define CLUSTER_FREE   1

#define BYTE_FREE   0xFF
#define BYTE_IN_USE 0x00

#define ROOT_DIR_MAP_MASK 0x7F
```

- Calcula-se:
	- Número de Clusters que não ficam referenciados num bloco completo	$$nExtraClusters = c_{total} \% ReferencesPerBitmapBlock$$
	- Número de Clusters da reference zone que estão totalmente ocupados $$nFullRefBlocks = \frac{c_{total}}{ReferencesPerBitmapBlock}$$
	- Número de Blocos para a Reference Bitmap zone $$nRefBlocks = nFullRefBlocks + (nExtraClusters != 0)$$
	- Byte no último bloco de referências onde começam as referências não válidas $$byteStartFreeBitmapPos = \frac{nExtraClusters}{8}$$
	- Bit no byte acima onde começam as referências não válidas $$bitStartFreeBitmapPos = nExtraClusters \% 8$$
- Blocos de referências completos
	- Para cada bloco de referências completo o número de referências é ReferencesPerBitmapBlock
	- O indice é 0 (o primeiro cluster livre está no início do bloco)
- Bloco parcialmente completo
	- Preencher o cnt com o número de clusters que esse bloco tem
	- Colocar os clusters não válidos como usados
- Referência do cluster 0
	- Indicar que está a ser usada pela root dir
	- Decrementar o count, porque existe menos um cluster vazio

### Considerações
- O número de clusters pode ser inferior ou superior ao tamanho de um bloco.
- O primeiro bit do primeiro bloco deve estar em use
- Todos os bits que não referenciem um cluster devem ser colocados como em uso

## Utilização
```c
void fillInFreeClusterTable(uint32_t rmstart,
							uint32_t ctotal 
						   ) 		
```

### Parameters
- **rmstart:** the number of the fisrt block used by the bit table
- **ctotal:** the total number of clusters

### Data Structure
**SORefBlock** : estrutura dos Reference bitmap Block data type

```cpp
struct SORefBlock
{
	/** \brief number of references in block */
	uint16_t cnt;
	/** \brief index of first non-empty byte */
	uint16_t idx;
	/** \brief bit map */
	uint8_t map[ReferenceBytesPerBitmapBlock];
};
```	

## Testes
```bash
# 1000 blocks, 144-inodes, mksofs.bin64
block range: 19
cnt = 244, idx = 0
0000: 7f ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff f8 00
0020: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
0040: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
0060: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
0080: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
00a0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
00c0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
00e0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
0100: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
0120: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
0140: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
0160: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
0180: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
01a0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
01c0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
01e0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00


# 200 blocks, 48-inodes, mksofs.bin64
block range: 7
cnt = 47, idx = 0
0000: 7f ff ff ff ff ff 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
0020: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
0040: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
0060: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
0080: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
00a0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
00c0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
00e0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
0100: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
0120: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
0140: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
0160: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
0180: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
01a0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
01c0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
01e0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00


# 10000 blocks, 1264 inodes, mksofs17.bin64
block range: 159
cnt = 2459, idx = 0
0000: 7f ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff
0020: ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff
0040: ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff
0060: ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff
0080: ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff
00a0: ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff
00c0: ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff
00e0: ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff
0100: ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff
0120: ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff f0 00 00 00 00 00 00 00 00 00 00 00 00
0140: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
0160: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
0180: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
01a0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
01c0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
01e0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
```

# fillInRootDir
- A root dir ocupa um cluster
- Os dois primeiros slots estão reservados para as entradas "." e ".."
- Todos os os outros slots devem estar limpos:
	- o campo _name_ preenchido com zeros
	- o campo _inode_ preenchido com NullReference

## Algoritmia
- Colocar todos os blocos com zeros
- Na entrada 0
	- nome: "."
	- inode: 0 (raiz)
- Na entrada 1
	- nome: ".."
	- inode: 0 (raiz)

## Utilização
```c
void fillInRootDir(uint32_t rtstart)
```

### Parameters
- **rtstart:** number of the block where the root cluster starts.


# resetClusters
- Escrever com zeros um conjunto de clusters

## Algoritmia
- Percorrer a sequência de clusters desejada e escrever zeros

## Utilização
```c
void resetClusters(	uint32_t cstart,
					uint32_t ctotal 
				  ) 		
```

### Parameters
- **cstart:** number of the block of the first free cluster
- **ctotal:** number of clusters to be filled 


# freelists
- Funções para manipular a lista de inodes livres e a lista de clusters livres.
- A lista de inodes livres é mantida usando uma lista biligada de inodes
	- Política FIFO
- A lista de clusters é mantida com duas caches:
	- Retrieval Cache: Clusters livres para serem alocados
	- Insertion Cache: Clusters que foram libertados
- A lista de clusters segue uma estrutura parecida com FIFO
	- Não é bem FIFO porque existe rotatividade nos clusters
		- Impede que exista uma escrita desigual nos clusters
		- Antes de um cluster libertado puder voltar a ser escrito, todos os outros clusters no disco têm de ser escritos
		- Aumenta o tempo útil do disco

## soAllocInode
uint32_t soAllocInode ( uint32_t  type )

   Allocate a free inode.

   An inode is retrieved from the list of free inodes, marked
   in use, associated to the legal file type passed as a
   parameter and is generally initialized.

   Parameters

         type the inode type (it must represent either a file, or a
              directory, or a symbolic link)

   Returns
          the number of the allocated inode



## soFreeCluster
void soFreeCluster ( uint32_t  cn )

   Free the referenced cluster.

   Parameters

          cn the number of the cluster to be freed


## soFreeInode
   void soFreeInode ( uint32_t  in )

   Free the referenced inode.

   The inode is inserted into the list of free inodes.

   Parameters

          in number of the inode to be freed

## soReplenish
   void soReplenish ( )

   replenish the retrieval cache

   References to free clusters should be transfered from the
   free cluster table (bit map) or insertion cache to the
   retrieval cache. Nothing should be done if the retrieval
   cache is not empty. The insertion cache should only be
   used if there are no bits at one in the map. Only a single
   block should be processed, even if it is not enough to
   fulfill the retrieval cache. The block to be processes is
   the one pointed to by the rmidx field of the superblock.
   This field should be updated if the processing of the
   current block reaches its end.


## soDeplete

[MISING IN DOXYGEN]


   Functions

   uint32_t  soAllocInode (uint32_t type)
             Allocate a free inode. More...

       void  soFreeInode (uint32_t in)
             Free the referenced inode. More...

   uint32_t  soAllocCluster ()
             Allocate a free cluster. More...

       void  soReplenish ()
             replenish the retrieval cache More...

       void  soFreeCluster (uint32_t cn)
             Free the referenced cluster. More...

       void  soDeplete ()
             Deplete the insertion cache.

Detailed Description

   Functions to manage the list of free inodes and the list
   of free clusters.




# Cenário Inicial
campo ihead superblock: 101 
campo ifree : 3

inode
previous|next

101
7|5

5
10|7

7
5/101

## freeinode
- Quero libertar o nó numero 200
- mante o tipo
- apenas altera flag para free
- o ficheiro passa a ser deleted file
- o nó que é libertado é obviamente o ultimo

```bash
# Estado inicial
----------------
Inode #3
type =  regular file, permissions = rw-rw-r--, lnkcnt = 1, owner = 1000, group = 1000
size in bytes = 42000, size in clusters = 7
atime = Thu Oct 26 23:02:47 2017, mtime = Thu Oct 26 23:02:47 2017, ctime = Thu Oct 26 23:02:47 2017
d[] = {11 (nil) (nil) (nil) 12 13}, i1 = 14, i2 = (nil)
----------------
Após chamar o freeinode para o inode 3
---------------
Inode #3
type = free regular file, permissions = rw-rw-r--, lnkcnt = 1, owner = 1000, group = 1000
size in bytes = 42000, size in clusters = 7
next = 10, , prev = 1
d[] = {11 (nil) (nil) (nil) 12 13}, i1 = 14, i2 = (nil)
----------------
- Mantem o size in clusters
```
## inserir inode 200
200
7|101


101
200|5

5
10/7

7
5|200

200
7|101

ihead:101
ifree: 4

## soAllocateInode
- Retirar o nó da lista de inodes
- O primeiro no a retirar é o que apontado pelo ihead

ihead:5
ifree:2

5
7|7

7
5|5

Só pode existir uma cópia do suoerbloco

## iOpen
- Abrir o inode
	- so o volta a ler do disco se já foi aberto
	- só posso pedir um pointer para esse inode
	- devolve me um inode handler
	- in: inode number
	- ih: inode handler
	- ip: inode pointer

## iSave
- Guardar um inode

## iClose
- Guardar o ficheiro

## Interface com os inodes é suposto usar uma estrutura de inodes

função replentish tem como função transferir blocos para a cache
	- transforma bits em referências
	- os bits que forem transferidos vão passar de 1 a zero
	- rmidx: primeiro byte do map de bit que tem bits a 1
		- comece por aqui. Antes não há bits a 1

# mais difícis (5)
soReplenish (Patricia)
soDeplete (Bernardo)

# intermédias (3)
soAllocateInode (panda)
soFreeInode (Gradim)

# mais triviais (1)
soAllocClusters (Pedro)
soFreeClusters (Mica)
# AllocInode
- Aloca um inode livre da estrutura de inodes
-

## Utilização 
```c
uint32_t soAllocInode ( uint32_t  type )
```
   Allocate a free inode.

   An inode is retrieved from the list of free inodes, marked in use, associated to the legal file type passed as
   a parameter and is generally initialized.

   Parameters

          type the inode type (it must represent either a file, or a directory, or a symbolic link)

   Returns
          the number of the allocated inode


- Se for possível, aloca o inode que está na HEAD
- Incrementa a HEAD
	- tem de verificar se a inode table está no fim e se tem de voltar aos inodes que entretanto foram libertados
- Decrementa o número de inodes livres 
- O inode tem de ser corretamente inicializado
# Inodes
- Existem 6 posições para referência direta aos clusters do ficheiro
	- d[0 ... 5]
- Uma posição para referência indireta
	- i1
	- Extende o array de d[6 ... 517]
- Uma posição para referência dupla indireta
	- 
- Cada ficheiro possui um inode
	- O número máximo de ficheiros num disco é o número máximo de inodes
- Um inode ocupa 64 bytes
	- Logo num disco com 512 bytes por bloco, existem 8 inodes em cada bloco
# Fileclusters
Functions to manage the clusters belonging by a file

## Doxygen
uint32_t  soGetFileCluster (int ih, uint32_t fcn)
            Get the cluster number of a given file cluster. More...

  uint32_t  soAllocFileCluster (int ih, uint32_t fcn)
            Associate a cluster to a given file cluster position.
            More...

      void  soFreeFileClusters (int ih, uint32_t ffcn)
            Free all file clusters from the given position on.
            More...

      void  soReadFileCluster (int ih, uint32_t fcn, void *buf)
            Read a file cluster. More...

      void  soWriteFileCluster (int ih, uint32_t fcn, void *buf)
            Write a data cluster. More...

Detailed Description

   Functions to manage the clusters belonging by a file.

   Author
          Artur Pereira - 2008-2009, 2016-2017
          Miguel Oliveira e Silva - 2009, 2017
          António Rui Borges - 2010-2015

   Remarks
          In case an error occurs, every function throws an
          SOException

Function Documentation

   uint32_t soAllocFileCluster ( int       ih,
                                 uint32_t  fcn
                               )

   Associate a cluster to a given file cluster position.

   Parameters

          ih  inode handler
          fcn file cluster number

   Returns
          the number of the allocated cluster

   void soFreeFileClusters ( int       ih,
                             uint32_t  ffcn
                           )

   Free all file clusters from the given position on.

   Parameters

          ih   inode handler
          ffcn first file cluster number

###   uint32_t soGetFileCluster ( int       ih,
                               uint32_t  fcn
                             )

   Get the cluster number of a given file cluster.

   Parameters

          ih  inode handler
          fcn file cluster number


   Returns
          the number of the corresponding cluster

- Parece me que apenas tenho de retornar o endereço do cluster
- Não é preciso retornar tudo
- Mandar mail ao professor
- Perguntar panda
- Para que servem as fnções do prof??
- Aquilo que faz é usar o inode handler para saber onde está no disk e o file cluster number para obter a referência
- É preciso rever as duas estruturas
	- superblock
	- inode
	- cluster

#### O que é preciso fazer:
- Obter o inode
- Se o cluster index estiver nos 6 primeiros
	- Sai direto da estrutura de inodes
- Se o cluster index for referenciado diretamente ($i_1$)
	- está no cluster de referências
	- Ler esse cluster do disco
	- Calcular novo index (subtrair 6?)
	- Ler Retornar a referência em que este está
- Se o cluste index estiver no cluster de referências indiretas ($i_2$)
	- Calcular dois novos indexes:
		- index no cluster de referências indiretas
		- index no cluster de referências diretas
	- Ler a referência do disco
	- Retornar o valor
- A testtool já trata de fazer o iOpen
- A soGetFileCluster é chamada com o indice do inode
- É preciso usar a `iGetPointer` para obter o ponteiro para a estrutura


#### Testes

+================================================================+
|                        testing functions                       |
+================================================================+
|   q - exit                     |  sb - show block              |
|  fd - format disk              | spd - set probe depths        |
+--------------------------------+-------------------------------+
|  ai - alloc inode              |  fi - free inode              |
|  ac - alloc cluster            |  fc - free cluster            |
|   r - replenish                |   d - deplete                 |
+--------------------------------+-------------------------------+
| gfc - get file cluster         | afc - alloc file cluster      |
| ffc - free file clusters       |     - NOT USED                |
| rfc - read file cluster        | wfc - write file cluster      |
+--------------------------------+-------------------------------+
| gde - get dir entry            | ade - add dir entry           |
| rde - rename dir entry         | dde - delete dir entry        |
|  tp - traverse path            |     - NOT USED                |
+--------------------------------+-------------------------------+
+ cia - check inode access       | sia - set inode access        +
+ iil - increment inode lnkcnt   | dil - decrement inode lnkcnt  +
+================================================================+

Your command: gfc
Inode number: 1
File cluster index: 7
(711)--> iOpen(1)
(711)--> --iOpenBin(1)
(403)--> soGetFileCluster(0, 7)
(403)--> --soGetFileClusterBin(0, 7)
(712)--> iGetPointer(0)
(712)--> --iGetPointerBin(0)
(714)--> iClose(0)
(714)--> --iCloseBin(0)
Cluster number (nil) retrieved
+================================================================+
|                        testing functions                       |
+================================================================+
|   q - exit                     |  sb - show block              |
|  fd - format disk              | spd - set probe depths        |
+--------------------------------+-------------------------------+
|  ai - alloc inode              |  fi - free inode              |
|  ac - alloc cluster            |  fc - free cluster            |
|   r - replenish                |   d - deplete                 |
+--------------------------------+-------------------------------+
| gfc - get file cluster         | afc - alloc file cluster      |
| ffc - free file clusters       |     - NOT USED                |
| rfc - read file cluster        | wfc - write file cluster      |
+--------------------------------+-------------------------------+
| gde - get dir entry            | ade - add dir entry           |
| rde - rename dir entry         | dde - delete dir entry        |
|  tp - traverse path            |     - NOT USED                |
+--------------------------------+-------------------------------+
+ cia - check inode access       | sia - set inode access        +
+ iil - increment inode lnkcnt   | dil - decrement inode lnkcnt  +
+================================================================+

Your command: gfc
Inode number: 1
File cluster index: 8
(711)--> iOpen(1)
(711)--> --iOpenBin(1)
(403)--> soGetFileCluster(0, 8)
(403)--> --soGetFileClusterBin(0, 8)
(712)--> iGetPointer(0)
(712)--> --iGetPointerBin(0)
(714)--> iClose(0)
(714)--> --iCloseBin(0)
Cluster number (nil) retrieved
+================================================================+
|                        testing functions                       |
+================================================================+
|   q - exit                     |  sb - show block              |
|  fd - format disk              | spd - set probe depths        |
+--------------------------------+-------------------------------+
|  ai - alloc inode              |  fi - free inode              |
|  ac - alloc cluster            |  fc - free cluster            |
|   r - replenish                |   d - deplete                 |
+--------------------------------+-------------------------------+
| gfc - get file cluster         | afc - alloc file cluster      |
| ffc - free file clusters       |     - NOT USED                |
| rfc - read file cluster        | wfc - write file cluster      |
+--------------------------------+-------------------------------+
| gde - get dir entry            | ade - add dir entry           |
| rde - rename dir entry         | dde - delete dir entry        |
|  tp - traverse path            |     - NOT USED                |
+--------------------------------+-------------------------------+
+ cia - check inode access       | sia - set inode access        +
+ iil - increment inode lnkcnt   | dil - decrement inode lnkcnt  +
+================================================================+

Your command: gfc
Inode number: 0
File cluster index: 1
(711)--> iOpen(0)
(711)--> --iOpenBin(0)
(851)--> sbGetPointer()
(851)--> --sbGetPointerBin()
(951)--> soReadRawBlock(1, 0x7fffd273b450)
(403)--> soGetFileCluster(1, 1)
(403)--> --soGetFileClusterBin(1, 1)
(712)--> iGetPointer(1)
(712)--> --iGetPointerBin(1)
(714)--> iClose(1)
(714)--> --iCloseBin(1)
Cluster number (nil) retrieved
+================================================================+
|                        testing functions                       |
+================================================================+
|   q - exit                     |  sb - show block              |
|  fd - format disk              | spd - set probe depths        |
+--------------------------------+-------------------------------+
|  ai - alloc inode              |  fi - free inode              |
|  ac - alloc cluster            |  fc - free cluster            |
|   r - replenish                |   d - deplete                 |
+--------------------------------+-------------------------------+
| gfc - get file cluster         | afc - alloc file cluster      |
| ffc - free file clusters       |     - NOT USED                |
| rfc - read file cluster        | wfc - write file cluster      |
+--------------------------------+-------------------------------+
| gde - get dir entry            | ade - add dir entry           |
| rde - rename dir entry         | dde - delete dir entry        |
|  tp - traverse path            |     - NOT USED                |
+--------------------------------+-------------------------------+
+ cia - check inode access       | sia - set inode access        +
+ iil - increment inode lnkcnt   | dil - decrement inode lnkcnt  +
+================================================================+

Your command:
---

### void soReadFileCluster ( int       ih,
                            uint32_t  fcn,
                            void *    buf
                          )

   Read a file cluster.

   Data is read from a specific data cluster which is
   supposed to belong to an inode associated to a file (a
   regular file, a directory or a symbolic link).

   If the referred file cluster has not been allocated yet,
   the returned data will consist of a byte stream filled
   with the character null (ascii code 0).

   Parameters

          ih  inode handler
          fcn file cluster number
          buf pointer to the buffer where data must be read into

   void soWriteFileCluster ( int       ih,
                             uint32_t  fcn,
                             void *    buf
                           )

   Write a data cluster.

   Data is written into a specific data cluster which is
   supposed to belong to an inode associated to a file (a
   regular file, a directory or a symbolic link).

   If the referred cluster has not been allocated yet, it
   will be allocated now so that the data can be stored as
   its contents.

   Parameters

          ih  inode handler
          fcn file cluster number
          buf pointer to the buffer containing data to be written

## soFreeFileCLusters
- Liberta todos os clusters do inode começando na posição atual
- Se o inode ficar sem clusters, é apagado
# direntries

# soGetDirEntry
- Obtem o inode associado ao nome da função
- É preciso fazer o parse do nome do diretório para chegar ao diretório pretendido
- Chama a traverse Path

- Tem de verificar se a entrada já existe

 uint32_t soGetDirEntry ( int           pih,
                            const char *  name
                          )

   Get the inode associated to the given name.

   The directory contents, seen as an array of directory entries, is parsed to find an entry whose name is name.

   The name must also be a base name and not a path, that is, it can not contain the character '/'.

   Parameters

          pih  inode handler of the parent directory
          name the name entry to be searched for

   Returns
          the corresponding inode number


# soRenameDirEntry
- Renomeia a entrada de um diretório

 void soRenameDirEntry ( int           pih,
                           const char *  name,
                           const char *  newName
                         )

   Rename an entry of a directory.

   A direntry associated from the given directory is renamed.

   Parameters

          pih     inode handler of the parent inode
          name    current name of the entry
          newName new name for the entry

# soTraversePath
- Obtem o inode associado com um dado caminho
- Atravessa a estrutura do sistema do sistema de ficheiros para obter o inode cujo nome do ficheiro é a componente mais à direita do caminho
- O caminho deve ser absoluto
- Todos elementos do caminho (com exceção do último) devem ser diretório ou symbolic links com permissão de travers (x)

 

   uint32_t soTraversePath ( char *  path )

   Get the inode associated to the given path.

   The directory hierarchy of the file system is traversed to find an entry whose name is the rightmost
   component of path. The path is supposed to be absolute and each component of path, with the exception of the
   rightmost one, should be a directory name or symbolic link name to a path.

   The process that calls the operation must have execution (x) permission on all the components of the path
   with exception of the rightmost one.

   Parameters

          path the path to be traversed

   Returns
          the corresponding inode number

# soAddDirEntry
- Adiciona uma nova entrada no diretório pai
- Uma direntry é adicionada ligando o parent inode ao child inode
	-  O lnkcnt do inode filho **não** é incrementado nesta função

void soAddDirEntry ( int           pih,
                        const char *  name,
                        uint32_t      cin
                      )

   Add a new entry to the parent directory.

   A direntry is added connecting the parent inode to the child inode. The refcount of the child inode is not
   incremented by this function.

   Parameters

          pih  inode handler of the parent inode
          name name of the entry
          cin  number of the child inode

# soDeleteDirEntry
- Remove uma entrada do parent directory
- O lnkcnt do inode filho **não** é decrementado

 uint32_t soDeleteDirEntry ( int           pih,
                               const char *  name,
                               bool          clean = false
                             )

   Remove an entry from a parent directory.

   A direntry associated from the given directory is deleted. The refcount of the child inode is not decremented
   by this function.

   Parameters

          pih   inode handler of the parent inode
          name  name of the entry
          clean if true (different than zero) clean the corresponding dir entry, otherwise keep it dirty

   Returns
          the inode number in the deleted entry




# Extra
filesystem check
- atribui ficheiros para a o disco
sempre que uma função falahe gera uma exceção

#soRenameDirEntry
- dá um novo nome ao diretório

#soDeleteDirEntry
- remove o diretório

#soGetDirEntry
- quando alguém a nível superior quer abrir/escrever, ver permissões precisa de sabe o inode
# itdealer
- Conjunto de funções que manipulam diretamente a estrutura de inodes

## iOpen
- Abre um inode
	- Transfere o seu conteudo para a memória
	- Set ao _usecount_
- Caso o inode já esteja aberto, incrementa a _usecount_
- Em qualquer dos casos devolve o handler (referência para a posição de memória) para o inode
# Syscalls

## Main syscalls
- **soLInk:** Cria um link para um ficheiro
- **soMkdir:** Cria um diretório
- **soMknod:** Cria um ficheiro regular com tamanho nulo
- **soRead:** Lê os dados de um ficheiro regular previamente aberto
- **soReaddir:** Lê uma entrada para um diretório de um dado diretório
- **soReadLink:** Lê um _symbolic link_
- **soRename:** Muda um nome de um ficheiro ou a sua localização na estrutura de diretórios
- **soRmdir:** Remover um diretório
	- O diretório de ve estar vazio
- **soSymlink:** Cria um symbolic link com o caminho desejado
- **soTruncate:** Trunca o tamanho de um regular file para o desejado
- **soUnlink:** Remove um link para um ficheiro através de um diretório
	- Remove também o ficheiro se o lnkcnt = 0
- **soWrite:** Escreve dados num regular file previamente aberto

## Other syscalls

- **exceptions** : sofs17 exception definition module
	```cpp
	struct SOException:public std::exception
		int en;             ///< (system) error number
		const char *msg;    ///< name of function that has thrown the exception
	
digraph x{
a -> b [label="b"]
a -> c [label="a"]
a -> d [label="d"]
}
# Existem duas camadas de syscalls
- main syscalls
	- 12 funções (temos de saber para o mini teste)
- other syscalss

## soLink
- Usar a transverse path para saber qual o nó que está na ponta
- link("/b", "a/c")
	- saber qual é o nó que está na pomnta do /b
		- uso o traverse para saber
		- abro e pergunto para saber o tipo
		- base name
		- verifico se é o diretório e tenho permissoes de excrita
		- verifico se ja tem o ficheirp que quero criar
		- chamar idirentry para criar o diretorio
		- chamar increment link count

## unLink
- Não apaga o ficheiro
- Quebra a ligaão
- dde - delete dir entry
- decrementa o link count
- se o tiver 0 links
	- chama a free inode para libertar o inode
	- chama a free cluster para libertar o cluster

- APgar ficheiros é derivado do unlink

Enquanto o ficheiro estive aberto não pode ser destruido
É o close do sistema operativo que apaga um fucheiro

## soRename
- função complicada 
- soRename("/b", "/a/c")
- OU é um rename se o novo pathe e o path antigo orem iguais
	```bash 
		soRename("/b", "/c")
	```
- Equivale no caso do move a fazer delete direentry e add direntry
- Não tem o link nem o dec
- O nó de destino passa a ser o mesmo	

## soMKnod
- Cria um nod do tipo ficheiro
- COrrepsonde a fazer:
	- Começar por criar um inode: alloc indode,
	- add dir entry 
	- increment link count
- Tem de validar primeiro:
	- Verificar se o "/a" existe, é um diretório e tem permisssoes de escrita
	- Verificar se o "/c" não existe

## soRead
- Posso quer ler dois bytes e ter de ler dois clusters
	- Ultimo byte do 1º cluster
	- Primeiro byte do 2º cluster
- A função read não pode ler para a lém do fim de ficheiro
- O size é que determina o fim do ficheiroo
- Indiretamento o write també, pode alocar clusters
- Tipicamente o wrtie tem de ler priemiro caos vá alterar parcialemtne um cluster
- O que interessa em termos de miniteste é o papel e efeito da função, não como o ocódigo é feito
- O que interessa é a consequemncia da execuação de u comando


## soTrucnate
- Alçtera o tamanho de um gicheiro ou para cima ou para baixo
- È assim que se cria buracos num ficheiro
	-  Trunco e vou acrescentando
- Gunção joga com o size e 
- Trunco o tamanho para 10
- Depois volto a troncar para 20
	- Ou no truncar para cima ou no truncar para baixo tenho de garantir que nºao existe lixo entre as zonas dos meus dados
		- Ou escrevo zeros., ou escrevo NullReferences
			- Null References dentro do size são lidas como zeros
	- O ficheiro é o mesmo, estou só a alterar o tamanho que esse ficheiro tem no disco
	- O truncate não quer saber o que lá está, simplesmente trunca os dados inteirores
	- Ou eu ponho zeros quando encolhi, ou ponho zeros quando abro

- Se fize ro fopne de um ficheiro já abero, o SO chama a truncate e mete os dados desse ficheiro a zero

## soMkdir
- Sempre ue há u novo direentry é preciso adicionar o linkcount
- Pressuposto: só se pode aoagar diretórios vazios
- Ler man2 para saber os erros que tem de emitir 

## soReadDIr
- É usado para fazer o ls
- Leê entradas de um diretorio
- Sempre que esta funlção é lida, ele vai ler a próxima direntry
- Em cadainvocaçáo eu tenho que lhe dzwr qenaotos bytes já passei
- Posso ter de processar duas entradas para lhe dar uma
. QUando a fubnção rreaddir devolve zero, já não existem mais entradas naquele deiretorio

##  soSymlink
- Creates a symbolic link

## so ReadLink
- Valor de retorno do symbolic link
# HOW to use sofs17
(so1718 - Aula prática 29 Sep)
## Documentação

```bash
# Gerar documentação
cd ./doc
doxygen
```

A documentação fica na pasta `./doc/html/`

# Make
```bash
# O make compila sempre tudo e não somente o conteudo da pasta
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


# soFreeFileCLusters
- Apagamento da esquerda para a direita
- As posições são alteradas e escritas com Null Reference
- o size só é alterado por syscalls e não ao libertar um inode/cluster

# 
- Um diretório tem um size múltiplo do cluster
- Os diretórios crescem cluster a cluster

# 
uint32_t soGetDirEntry ( int           pih,
                            const char *  name
                          )

   Get the inode associated to the given name.

   The directory contents, seen as an array of directory entries, is parsed to find an entry whose name is name.

   The name must also be a base name and not a path, that is, it can not contain the character '/'.

   Parameters

          pih  inode handler of the parent directory
          name the name entry to be searched for

   Returns
          the corresponding inode number

# 
 uint32_t soDeleteDirEntry ( int           pih,
                               const char *  name,
                               bool          clean = false
                             )

   Remove an entry from a parent directory.

   A direntry associated from the given directory is deleted. The refcount of the child inode is not decremented
   by this function.

   Parameters

          pih   inode handler of the parent inode
          name  name of the entry
          clean if true (different than zero) clean the corresponding dir entry, otherwise keep it dirty

   Returns
          the inode number in the deleted entry
 

Comentários
- soWriteRawBlock
	- char blk[blockSize]
	- SOSuperblock sb;
	- SOTnode it[inodesPerBlock]
	- soWriteRawBlock(uint32_t n, void *buf)
	- blk
	- fsb
	- it


## Notes
função alloc cluster tem de verificar se ficou tudo bem no disco
função replentish transfer da reference bitmao block para a retrieval cache

Capacidade: (6+ 2^9 + (2^9)^2)x 2^11

# 3 Nov 2017
- As direntries não mexem no lnkcnt
- Add mexe no size

# Unlink
1. dde
2. dec 
3. if(dec ==0)
	3.1 ffc
	3.2 fi

O dec devolve o devolve 

# Remove

# mtime vs ctime
- **mtime:** conteudo do ficheiro
- **ctime:** metadados do ficheiro

- Não podemos ter nenhum diretório apontado por dois diretórios
```bash
ln: 'ddd/': hard link not allowwd for directory
```
