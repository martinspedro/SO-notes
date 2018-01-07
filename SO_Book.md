# Shell Scripting

## Exercise 1 - Command Overview
* **man <arg>** : Documentação dos comandos  
* **ls**        : Listar ficheiros de uma pasta  
* **mkdir**     : Criar uma pasta  
* **pwd**	  : Caminho absoluto do diretório corrente  
* **rm**        : Remover ficheiros  
* **mv** 	  : Renomear ficheiros ou mover ficheiros/pastas entre pastas  
* **cat**	  : Imprimir um ficheiro para o stdout  
* **echo**	  : Imprimir para o stdout uma mensagem  
* **less**	  : paginar um ficheiro (não mostra o texto literal)  
* **head**	  : mostrar as primeiras 10 linhas de um ficheiro  
* **tail**	  : mostrar as ultimas 10 linhas de m ficheiro  
* **cp**	  : copiar ficheiros  
* **diff**      : mostrar as diferenças linha a linha entre dois ficheiros  
* **wc**        : contar linhas, palavras e caracteres de um ficheiro  
* **sort**	  : ordenar ficheiros  
* **grep** 	  : pesquisa de padroes em ficheiros  
* **sed**       : transformacoes de texto  
* **tr**	  : substituir, modificar ou apagar caracteres do stdin e imprimir no stdout  
* **cut**	  : imprimir partes de um ficheiro para o stdout  
* **paste**	  : imprimir linhas de um ficheiro separadas por tabs para o stdout  
* **tee**       : Redireciona para o nome do ficheiro passado como argumento e para o stdout

## Exercise 2 - Redirect input and output
### 1
`>`  : redirecionar o output do comando anterior do stdout para um ficheiro  
`>>` : append do output do comando anterior do stdout para um ficheiro  

### 2
`2>` : redireciona o stderror para um ficheiro  

### 3
`|` : redireciona o stdout de um comando para o stdin do comando seguinte  

### 4
`2>&1` : redireciona o stderror para o stdout  
`1>&2` : redireciona o stdout para o stderror  

## Exercise 3 - Using special characters

### 1  
touch : criar ficheiros caso o ficheiro não exista. Alterar a data de modificação caso o ficheiro exista  
a\*    : [REGEX] Lista todos os ficheiros que o primeiro caracter seja um a, independentemente do número de ficheiros  
a?    : [REGEX] Lista todos os ficheiros começados por a e com mais 1 caracter  
\\*     : [REGEX] Lista qualquer ficheiro independentemente do numero de caracteres  

### 2
\[ac\]  : [REGEX] Lista os ficheiros com os caracteres entre []  
\[a-c\] : [REGEX] Lista os ficheiros com os caracteres entre a e c  
\[ab\]\* : [REGEX] Lista os ficheiros com os caracteres \{a, b\} independentemente do número de caracteres  

### 3
o \\ antes de um caracter especial desativa as capacidades especiais do stdout  

a\*  : [REGEX] Lista todos os ficheiros começados por a independentemente do número de caracteres  
a\\\* : Lista o ficheiro com o nome a*  
a?  : [REGEX] Lista todos os ficheiros começados por a e com mais um caracter  
a\\? : Lista o ficheiro com o nome a?  
a\\\[ : Lista o ficheiro com o nome a[  
a\\\\ : Lista o ficheiro com o nome a\  

### 4 
Usando `''` ou `""` podemos desativar o significado de caracteres especiais  
`a*`   : [REGEX] Lista todos os ficheiros começados por a independentemente do número de caracteres  
`'a*'` : Seleciona o ficheiro a*  
`"a*"` : Seleciona o ficheiro a*  

## Exercise 4 - Declaring and using variables 

### 1
\<variable name>=.... : Atribuição de variáveis em bash. Não deve ter espaço entre o nome da variável e a atribuição  
`$<variable name>` : lê o valor da variável (em bash existe diferença entre atribuir um valor a uma variável e ler o valor da variável). Pode se atribuir nome de ficheiros e usar REGEX (p.e. z=a*)  \
`${<variable name>}` : lê o valor da variável (em bash existe diferença entre atribuir m valor a uma variável e ler o valor da variável)  
`${<variable name>}<etc>` : Concatena o valor da variável com o que está à frente (<etc>)  

### 2
- `$<variable name>` : Acede ao valor da variável  
- `"$<varibale name>"` : Acede ao valor da variável (não aplica quaisquer caracteres especiais). P.e. se v=a*, `"$v"` será igual a a* em vez de todos os ficheiros começados por a com mais um caracter adicional  \
- `'$<variable name>'` : Ignora a leitura da variável e de um possível REGEX, devolvendo `$<variable name>` \


### 3 
- `${<variable name>:start:numero de caracteres}` : trata a variável como string, criando uma substring começando no caracter start com o numero de caracteres especificado. Pode ter espaços entre os :  
- `${<variable name\>/<search substring\>/<replace substring>}` : Procura uma substring na variable name e substitui por outra substring indicada  

## Exercise 5 - Declaring and using functions

### 1 
Para declarar uma função:
```bash
<nome_da_funcao>()
{
	# corpo da função
}
```
### 2
`$#` : Número de argumentos de uma função \
`$1` : Primeiro argumento \
`$2` : Segundo argumento \
`$*` : Todos os argumentos - Ignora sequencias de white space dentro das aspas na passagem de argumentos da bash \
`$@` : Todos os argumentos - Ignora sequencias de white space dentro das aspas na passagem de argumentos da bash \
`"$*"` : Todos os argumentos - Preserva a forma dos argumentos passados entre aspas (i.e., o white space) \
`"$@"` : Todos os argumentos - Preserva a forma dos argumentos passados entre aspas (i.e., o white space)  \ 

## Exercise 6
### 1, 2 e 3 
- `{ .......\}` : Agrupar commandos (pode ser redirecionado o stdout usando | ou >). A lista de comandos é executada na mesma instância da bash em que é chamada (contexto global de execução, com variáveis globais) \
- ( ....... ) : Agrupar comandos (pode ser redirecionado o stdout usando | ou >). O grupo de comandos é executado noutra instância da bash (contexto próprio de execução, com variáveis locais) \

## Exercise 7
### 1
`$?` : Valor de retorno de um comando (semelhante a C/C++). Se for `'1'` existe um erro na execução do comando. Se for `'0'` está tudo bem

### 2 
```bash
echo -e : Faz parse de códigos de cores

"e\33m ... \e[0m" : Código de cores que define a cor de sucesso
"e\31m ... \e[0m" : Código de cores que define a cor de erro 
```
Estrutura de um if:
```bash
if <cond>
then
	<statment>
else
	<statment>
fi
```

### 3
Os parentesis retos na condição do if (p.e. `if [ -f $1]`) que chamam a função test devem estar com pelo menos um espaço entre os outros caracteres

### 4 
Os operadores têm de estar com pelo menos um espaço de intervalo \
! : Operador not

### 5
&& : Operador and \
`||` : Operador or \


## Exercise 8 - The multiple choice case construction

### 1 Case stament
```bash
case <variavel para selecionar> in
	<cond1>) <statment 1>;;
	<cond2>) <statment 2>;;
	<cond3>) <statment 3>;;
	....
esac
```
Onde: \
- A <variavel para selecionar> pode ser `$#`, `$*` ou `$1` \
- O ;; no final da `<ação #>` equivale ao fim da branch (break em C) \
- O | permite a definição de uma várias alternativas (condições) para o mesmo case (e consequentemente ação) \
- O \* segnifica qualquer valor. Ao ser colocado em último permite selecionar todas as outras opções que ainda não forma cobertas (equivalente ao default em C) \

## Exercise 9 - The repetitive for contruction

### 1 
A syntax de um for é:
```bash
for <variavel de iteracao> in <lista de objectos para iterar>
do
	<statment>
done
```
Onde `<lista de objectos para iterar>` podem ser ficheiros e/ou pastas e podem ser usados caracteres especiais como `a*`

### 2

## 10 - The repetitive while and until constructions

### 1
Estrutura de um while:
```bash
while [ <condicao de paragem> ]
do
	<statment>
	shift
done
```
Estrutura de um until
```bash
until [ <condição de paragem> ]
do
	<statment>
	shift
done 
```
Onde a condição de paragem pode ser escrita como:
`<variable> <condição de teste> <fim> `

As condições de teste podem ser:
```bash
-gt : greater than
-eq : equal
-lt : less than
-le : less or equal than
-ge : greater or equal than
```
`shift` é uma palavra equivalente ao continue em C

## Exercise 11 - Script Files
#1 

O cabeçalho do ficheiro de script é:
```bash
#!/bin/bash
# The previous line (comment) tells the operating system that
#  this script is to be executed in bash
#
```
Condições usadas:
```bash
[ $# -ne 1 ] : número de argumentos diferente de 1
! [ -f $1 ] : O primeiro argumento dado não é um ficheiro
1>&2 - Redirecionar o stderror para o stdout
```
## Exercise 12 - Bash supports both indexed and associative arrays
### 1
Os indices de um array não são continuos e não podem ser negativos

A declaração explicita dos arrays pode ser feita fazendo: `declare -a <array>[<idx>]=<value>` \

Outras operações: \
- **Atribuição**: `<array>[<idx>]=<valor>` \
- **Leitura**: `${<array>[<idx]}` \
- **Leitura de todos os elementos do array**: `${a[*]}` \
- **Número de elementos do array**: `${#a[*]}` \
- **Lista dos indices do array** : `${!a[*]}` \


Os indices podem ser obtidos com expressões aritméticas \
A iteração pelos indices é feita da mesma forma que em python \
- **Iterar na lista de elementos**: `for <variavel> in ${<array>[*]}` \
- **Iterar na lista de indices**: `for <variavel> in ${!a[*]}` \

Exemplo de código para imprimir os indices e os elementos
```bash
for v in ${!a[*]}
do
	echo "a[$i] = ${a[$i]}"
done
```
### 2

A declaração de arrays associativos tem de ser feita de forma explicita
`declare -A <array>`

- A **atribuição de valores** para um **array associativo**: `<array>["<key>"]=<value>`
- **Listar os elementos no array** : `${<array>[*]}`
- **Listar o número de elementos no array** : `${#<array>[*]}`
- **Listar os indices usados no array** : `${!<array>[*]}`

Exemplo de código para percorrer as keys e imprimir as keys e os values
```bash
for i in ${!arr[*]}
do
	echo "Key = $i | Value = ${arr[$i]}\"
done
```

\newpage

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

\newpage

---
title: "Interprocess Communication"
---

# Conceitos Introdutórios
Num ambiente multiprogramado, os processos podem ser:

- Independentes:
	- Nunca interagem desde a sua criação à sua destruição
	- Só possuem uma interação implícita: **competir por recursos do sistema**
		- e.g.: jobs num sistema bacth, processos de diferentes utilizadores
	- É da responsabilidade do sistema operativo garantir que a atribuição de recursos é feita de forma controlada
		- É preciso garantir que não ocorre perda de informação
		- Só **um processo pode usar um recurso num intervalo de tempo** - _**Mutual Exclusive Acess**_
- Cooperativos:
	- **Partilham Informação** e/ou **Comunicam** entre si
	- Para **partilharem** informação precisam de ter acesso a um **espaço de endereçamento comum**
	- A comunicação entre processos pode ser feita através de:
		- Endereço de memória comum
		- Canal de comunicação que liga os processos
	- É da **responsabilidade do processo** garantir que o acesso à zona de memória partilhada ou ao canal de comunicação é feito de forma controlada para não ocorrerem perdas de informação
		- Só **um processo pode usar um recurso num intervalo de tempo** - _**Mutual Exclusive Acess**_
		- Tipicamente, o canal de comunicação é um recurso do sistema, pelo quais os **processos competem**

O acesso a um recurso/área partilhada é efetuada através de código. Para evitar a perda de informação, o código de acesso (também denominado zona cŕitica) deve evitar incorrer em **race conditions**.

## Exclusão Mútua
Ao forçar a ocorrência de exclusão mútua no acesso a um recurso/àrea partilhada, podemos originar:

- **deadlock:**
	- Vários processos estão em espera **eternamente** pelas condições/eventos que lhe permitem aceder à sua respetiva **zona crítica**
		- Pode ser provado que estas condições/eventos **nunca se irão verificar**
	- Causa o bloqueio da execução das operações
- **starvation:**
	- Na competição por acesso a uma zona crítica por vários processos, verificam-se um conjunto de circunstâncias na qual novos processos, com maior prioridade no acesso às suas zonas críticas, continuam a aparecer e **tomar posse dos recursos partilhados**
	- O acesso dos processos mais antigos à sua zona crítica é sucessivamente adiado

# Acesso a um Recurso
No acesso a um recurso é preciso garantir que não ocorrem **race conditions**.
Para isso, **antes** do acesso ao recurso propriamente dito é preciso **desativar o acesso** a esse recurso pelos **outros processos** (reclamar _ownership_) e após o acesso é preciso restaurar as condições iniciais, ou seja, **libertar o acesso** ao recurso.


```c
/* processes competing for a resource - p = 0, 1, ..., N-1 */
void main (unsigned int p)
{
	forever
	{	
		do_something();
		access_resource(p);
		do_something_else();
	}	
}

void acess_resource(unsigned int p)
{
	enter_critical_section(p);
	use_resource();		//	critical section
	leave_critical_section(p);
}
```

# Acesso a Memória Partilhada
O acesso à memória partilhada é muito semelhante ao aceso a um recurso (podemos ver a memória partilhada como um recurso partilhado entre vários processos).

Assim, à semelhança do acesso a um recurso, é preciso **bloquear o acesso de outros processos à memória partilhada** antes de aceder ao recurso e após aceder, **re-ativar o acesso a memória partilhada** pelos outros processos.

```c
/* shared data structure */
shared DATA d;

/* processes sharing data - p = 0, 1, ..., N-1 */
void main (unsigned int p)
{
	forever
	{
		do_something();
		access_shared_area(p);
		do_something_else();
	}
}

void access_shared_area(unsigned int p)
{
	enter_critical_section(p);
	manipulate_shared_area(); // critical section
	leave_critical_section(p);
}
```

## Relação Produtor-Consumidor
O acesso a um recurso/memória partilhada pode ser visto como um problema Produtor-Consumidor:

- Um processo acede para **armazenar dados**, **escrevendo** na memória partilhada _(Produtor)_
- Outro processo acede para **obter dados**, **lendo** da memória partilhada _(Consumidor)_

### Produtor
O produtor "produz informação" que quer guardar na FIFO e enquanto não puder efetuar a sua escrita, aguarda até puder **bloquear** e **tomar posse** do zona de memória partilhada

```c
/* communicating data structure: FIFO of fixed size */
shared FIFO fifo;

/* producer processes - p = 0, 1, ..., N-1 */
void main (unsigned int p)
{
	DATA val;
	bool done;


	forever
	{
		produce_data(&val);
		done = false;
		do
		{
			// Beginning of Critical Section
			enter_critical_section(p);
			if (fifo.notFull())
			{
				fifo.insert(val);
				done = true;
			}
			leave_critical_section(p);
			// End of Critical Section
		} while (!done);
		do_something_else();
	}
}
```

### Consumidor
O consumidor quer ler informação que precisa de obter da FIFO e enquanto não puder efetuar a sua leitura, aguarda até puder **bloquear** e **tomar posse** do zona de memória partilhada

```c
/* communicating data structure: FIFO of fixed size */
shared FIFO fifo;

/* consumer processes - p = 0, 1, ..., M-1 */
void main (unsigned int p)
{
DATA val;
bool done;
	forever
	{
		done = false;
		do
		{
			// Beginning of Critical Section
			enter_critical_section(p);
			if (fifo.notEmpty())
			{
				fifo.retrieve(&val);
				done = true;
			}
			leave_critical_section(p);
			// End of Critical Section
		} while (!done);
		consume_data(val);
		do_something_else();
	}
}
```

# Acesso a uma Zona Crítica
Ao aceder a uma zona crítica devem ser verificados as seguintes condições:

- **Efective Mutual Exclusion:** O **acesso** a uma **zona crítica** associada com o mesmo recurso/memória partilhada só pode ser **permitida a um processo de cada vez** entre **todos os processos** a competir pelo acesso a esse mesmo recurso/memória partilhada
- **Independência** do número de processos intervenientes e na sua velocidade relativa de execução
- Um processo fora da sua zona cŕitica não pode impedir outro processo de entrar na sua zona crítica
- Um processo **não deve ter de esperar indefinidamente** após pedir acesso ao recurso/memória partilhada para que possa aceder à sua zona crítica
- O período de tempo que um processo está na sua **zona crítica** deve ser **finito**

## Tipos de Soluções
Para controlar o acesso às zonas críticas normalmente é usado um endereço de memória. A gestão pode ser efetuada por:

- **Software:**
	- A solução é baseada nas instruções típicas de acesso à memória
	- Leitura e Escrita são indepentes e correspondem a instruções diferentes
- **Hardware:**
	- A solução é baseada num conjunto de instruções especiais de acesso à memória
	- Estas instruções permitem ler e de seguida escrever na memória, de forma **atómica**

## Alternância Estrita _(Strict Alternation)_
**Não é uma solução válida**

- Depende da velocidade relativa de execução dos processos intervenientes
- O processo com menos acessos impõe o ritmo de acessos aos restantes processos
- Um processo fora da zona crítica não pode prevenir outro processo de entrar na sua zona crítica
- Se não for o seu turno, um processo é obrigado a esperar, mesmo que não exista mais nenhum processo a pedir acesso ao recurso/memória partilhada

```c
/* control data structure */
#define R 		/* process id = 0, 1, ..., R-1 */

shared unsigned int access_turn = 0;
void enter_critical_section(unsigned int own_pid)
{
	while (own_pid != access_turn);
}

void leave_critical_section(unsigned int own_pid)
{
	if (own_pid == access_turn)
		access_turn = (access_turn + 1) % R;
}
```

## Eliminar a Alternância Estrita

```c
/* control data structure */
#define R 2 	/* process id = 0, 1 */

shared bool is_in[R] = {false, false};

void enter_critical_section(unsigned int own_pid)
{
	unsigned int other_pid_ = 1 - own_pid;

	while (is_in[other_pid]);
	is_in[own_pid] = true;
}

void leave_critical_section(unsigned int own_pid)
{
	is_in[own_pid] = false;
}
```

Esta solução não é válida porque não garante **exclusão mútua**. 

Assume que:

- $P_0$ entra na função `enters_critical_section` e testa `is_in[1]`, que retorna  Falso
- $P_1$ entra na função `enter_critical_section` e testa `is_in[0]`, que retorna Falso
- $P_1$ altera `is_in[0]` para _true_ e entra na zona crítica
- $P_0$ altera `is_in[1]` para _true_ e entra na zona crítica

Assim, ambos os processos entra na sua zona crítica **no mesmo intervalo de tempo**.

O principal problema desta implementação advém de **testar primeiro** a variável de controlo do **outro processo** e só **depois** alterar a **sua variável** de controlo.

## Garantir a exclusão mútua

```c
/* control data structure */
#define R 2 	/* process id = 0, 1 */

shared bool want_enter[R] = {false, false};

void enter_critical_section(unsigned int own_pid)
{
	unsigned int other_pid_ = 1 - own_pid;

	want_enter[own_pid] = true;
	while (want_enter[other_pid]);
}

void leave_critical_section(unsigned int own_pid)
{
	want_enter[own_pid] = false;
}
```

Esta solução, apesar de **resolver a exclusão mútua**, **não é válida** porque podem ocorrer situações de **deadlock**. 

Assume que:

- $P_0$ entra na função `enter_critical_section` e efetua o _set_ de `want_enter[0]`
- $P_1$ entra na função `enter_critical_section` e efetua o _set_ de `want_enter[1]`
- $P_1$ testa `want_enter[0]` e, como é _true_, **fica em espera** para entrar na zona crítica
- $P_0$ testa `want_enter[1]` e, como é _true_, **fica em espera** para entrar na zona crítica

Com **ambos os processos em espera** para entrar na zona crítica e **nenhum processo na zona crítica** entramos numa situação de **deadlock**.

Para resolver a situação de deadlock, **pelo menos um dos processos** tem recuar na intenção de aceder à zona crítica.

## Garantir que não ocorre deadlock

```c
/* control data structure */
#define R 2 	/* process id = 0, 1 */

shared bool want_enter[R] = {false, false};

void enter_critical_section(unsigned int own_pid)
{
	unsigned int other_pid_ = 1 - own_pid;

	want_enter[own_pid] = true;
	while (want_enter[other_pid])
	{
		want_enter[own_pid] = false;	// go back
		random_dealy();
		want_enter[own_pid] = true;		// attempt a to go to the critical section
	}
}

void leave_critical_section(unsigned int own_pid)
{
	want_enter[own_pid] = false;
}
```

A solução é quase válida. Mesmo um dos processos a recuar ainda é possível ocorrerem situações de **deadlock** e **starvation**:

- Se ambos os processos **recuarem ao "mesmo tempo"** (devido ao `random_delay()` ser igual), entramos numa situação de **starvation**
- Se ambos os processos **avançarem ao "mesmo tempo"** (devido ao `random_delay()` ser igual), entramos numa situação de **deadlock**

A solução para **mediar os acessos** tem de ser **determinística** e não aleatória.

## Mediar os acessos de forma determinística: _Dekker agorithm_

```c
/* control data structure */
#define R 2 	/* process id = 0, 1 */

shared bool want_enter[R] = {false, false};
shared uint p_w_priority = 0;

void enter_critical_section(unsigned int own_pid)
{
	unsigned int other_pid_ = 1 - own_pid;

	want_enter[own_pid] = true;
	while (want_enter[other_pid])
	{
		if (own_pid != p_w_priority)			// If the process is not the prioritary process
		{
			want_enter[own_pid] = false;		// go back
			while (own_pid != p_w_priority);	// waits to acess to his critical section while
												//  its is not the prioritary process 
			want_enter[own_pid] = true;			// attempt to go to his critical section
		}
	}
}

void leave_critical_section(unsigned int own_pid)
{
	unsigned int other_pid_ = 1 - own_pid;
	p_w_priority = other_pid;					// when leaving the its critical section, assign the
												// priority to the other process
	want_enter[own_pid] = false;				
}
```

É uma **solução válida**:

- Garante exclusão mútua no acesso à zona crítica através de um mecanismo de alternância para resolver o conflito de acessos
- **deadlock** e **starvation** **não estão presentes**
- Não são feitas suposições relativas ao tempo de execução dos processos, i.e., o algoritmo é **independente** do tempo de execução dos processos

No entanto, **não pode ser generalizado**  para mais do que 2 processos e garantir que continuam a ser satisfeitas as condições de **exclusão mútua** e a ausência de **deadlock** e **starvation**


## Dijkstra algorithm (1966)

```c
/* control data structure */
#define R 2 	/* process id = 0, 1 */

shared uint want_enter[R] = {NO, NO, ..., NO};
shared uint p_w_priority = 0;

void enter_critical_section(uint own_pid)
{
	uint n;
	do
	{
		want_enter[own_pid] = WANT;					// attempt to access to the critical section
		while (own_pid != p_w_priority)				// While the process is not the prioritary process
		{
			if (want_enter[p_w_priority] == NO)		// Wait for the priority process to leave its critical section
				p_w_priority = own_pid;
		}

		want_enter[own_pid] = DECIDED;				// Mark as the next process to access to its critical section

		for (n = 0; n < R; n++)				// Search if another process is already entering its critical section
		{
			if (n != own_pid && want_enter[n] == DECIDED)	// If so, abort attempt to ensure mutual exclusion
				break;
		}
	} while(n < R);
}

void leave_critical_section(unsigned int own_pid)
{
	p_w_priority = (own_pid + 1) % R;			// when leaving the its critical section, assign the
												// priority to the next process
	want_enter[own_pid] = false;				
}
```

Pode sofrer de **starvation** se quando um processo iniciar a saída da zona crítica e alterar `p_w_priority`, atribuindo a prioridade a outro processo, outro processo tentar aceder à zona crítica, sendo a sua execução interompida no for. Em situações "especiais", este fenómeno pode ocorrer sempre para o mesmo processo, o que faz com que ele nunca entre na sua zona crítica


## Peterson Algorithm (1981)

```c
/* control data structure */
#define R 2 	/* process id = 0, 1 */

shared bool want_enter[R] = {false, false};
shared uint last;

void enter_critical_section(uint own_pid)
{
	unsigned int other_pid_ = 1 - own_pid;

	want_enter[own_pid] = true;
	last = own_pid;
	while ( (want_enter[other_pid]) && (last == own_pid) );		// Only enters the critical section when no other
																// process wants to enter and the last request
																// to enter is made by the current process
}

void leave_critical_section(unsigned int own_pid)
{
	want_enter[own_pid] = false;
}
```

O algoritmo de _Peterson_ usa a **ordem de chegada** de pedidos para resolver conflitos:

- Cada processo tem de **escrever o seu ID numa variável partilhada** (_last_), que indica qual foi o último processo a pedir para entrar na zona crítica
- A **leitura seguinte** é que vai determinar qual é o processo que foi o último a escrever e portanto qual o processo que deve entrar na zona crítica



\begin{table}[H]
\centering
\renewcommand{\arraystretch}{1.2}
\begin{tabular}{lccccc}
\multicolumn{1}{c}{ } & \multicolumn{2}{c}{$P_0$ quer entrar} & & \multicolumn{2}{c}{$P_1$ quer entrar} \\ \cmidrule{2-3} \cmidrule{5-6}
\multicolumn{1}{c}{\multirow{-2}{*}} & \multicolumn{1}{c}{$P_1$ não quer entrar} & \multicolumn{1}{c}{$P_1$ quer entrar} & & $P_0$ não quer entrar & $P_0$ quer entrar \\ \toprule
\multicolumn{1}{l}{last = $P_0$} & \multicolumn{1}{c}{$P_0$ entra} & \multicolumn{1}{c}{$P_1$ entra} & & - & $P_1$ entra \\
last = $P_1$ & - & $P_0$ entra & & $P_1$ entra & $P_0$ entra \\ \bottomrule
\end{tabular}
\end{table}


É uma solução válida que:

- Garante exclusão mútua
- Previne deadlock e starvation
- É independente da velocidade relativa dos processos 
- Pode ser generalizada para mais do que dois processos (variável partilhada -> fila de espera)

## Generalized Peterson Algorithm (1981)

```c
/* control data structure */
#define R ... 	/* process id = 0, 1, ..., R-1 */

shared bool want_enter[R] = {-1, -1, ..., -1};
shared uint last[R-1];

void enter_critical_section(uint own_pid)
{
	for (uint i = 0; i < R -1; i++)				
	{
		want_enter[own_pid] = i;		
		
		last[i] = own_pid;

		do
		{
			test = false;
			for (uint j = 0; j < R; j++)
			{
				if (j != own_pid)
					test = test || (want_enter[j] >= i)
			}
		} while ( test && (last[i] == own_pid) );		// Only enters the critical section when no other
																// process wants to enter and the last request
																// to enter is made by the current process
	}
}

void leave_critical_section(unsigned int own_pid)
{
	want_enter[own_pid] = -1;
}
```

> _needs clarification_

# Soluções de Hardware

## Desativar as interrupções

Num ambiente computacional com **um único processador:**

- A alternância entre processos, num ambiente **multiprogramado**, é sempre causada por um evento/dispositivo externo
	- **real time clock (RTC):** origina a transição de time-out em sistemas _preemptive_
	- **device controller:** pode causar transições _preemptive_ no caso de um fenómeno de _wake up_ de um **processo mais prioritário**
	- Em qualquer dos casos, o **processador é interrompido** e a execução do processo atual parada
- A garantia de acesso em **exclusão mútua** pode ser feita desativando as interrupções
- No entanto, só pode ser efetuada em **modo kernel**
	- Senão código malicioso ou com _bugs_ poderia bloquear completamente o sistema

Num ambiente computacional **multiprocessador**, desativar as interrupções num único processador não tem qualquer efeito.

Todos os outro processadores (ou _cores_) continuam a responder às interrupções.

## Instruções Especiais em Hardware

### Test and Set (TAS primitive)
A função de hardware, `test_and_set` se for implementada atomicamente (i.e., sem interrupções) pode ser utilizada para construir a primitiva  **lock**, que permite a entrada na zona crítica

Usando esta primitiva, é possível criar a função _lock_, que permite entrar na zona crítica


```c
shared bool flag = false;

bool test_and_set(bool * flag)
{
	bool prev = *flag;
	*flag = true;
	return prev;
}

void lock(bool * flag)
{
	while (test_and_set(flag);	// Stays locked until and unlock operation is used
}

void unlock(bool * flag)
{
	*flag = false;
}
```

### Compare and Swap
Se implementada de forma atómica, a função `compare_and_set` pode ser usada para implementar a primitiva lock, que permite a entrada na zona crítica

O comportamento esperado é que coloque a variável a 1 sabendo que estava a 0 quando a função foi chamada e vice-versa.

```c
shared int value = 0;

int compare_and_swap(int * value, int expected, int new_value)
{
	int v = *value;
	if (*value == expected)
		*value = new_value;
	return v;
}

void lock(int * flag)
{
	while (compare_and_swap(&flag, 0, 1) != 0);
}

void unlock(bool * flag)
{
	*flag = 0;
}
```

## Busy Waiting
Ambas as funções anteriores são suportadas nos _Instruction Sets_ de alguns processadores, implementadas de forma atómica

No entanto, ambas as soluções anteriores sofrem de **busy waiting**. A primitva lock está no seu **estado ON** (usando o CPU) **enquanto espera** que se verifique a condição de acesso à zona crítica. Este tipo de soluções são conhecidas como **spinlocks**, porque o processo oscila em torno da variável enquanto espera pelo acesso

Em sistemas **uniprocessador**, o **busy_waiting** é **indesejado** porque causa:

- **Perda de eficiência:** O **time quantum** de um processo está a ser desperdiçado porque não está a ser usado para nada
- ** Risco de deadlock**: Se um **processo mais prioritário** tenciona efetuar um **lock** enquanto um processo menos prioritário está na sua zona crítica, **nenhum deles pode prosseguir**.
	- O processo menos prioritário tenta executar um unlock, mas não consegue ganhar acesso a um _time qantum_ do CPU devido ao processo mais prioritário
	- O processo mais prioritário não consegue entrar na sua zona crítica porque o processo menos prioritário ainda não saiu da sua zona crítica


Em sistemas **multiprocessador** com **memória partilhada**, situações de busy waiting podem ser menos críticas, uma vez que a troca de processos _(preempt)_ tem custos temporais associados. É preciso:

- guardar o estado do processo atual
	- variáveis
	- stack
	- $PC
- copiar para memória o código do novo processo


## Block and wake-up
Em **sistemas uniprocessor** (e em geral nos restantes sistemas), existe a o requerimento de **bloquear um processo** enquanto este está à espera para entrar na sua zona crítica

A implementação das funções `enter_critical_section` e `leave_critical_section` continua a precisar de operações atómicas.



```c
#define  R ... /* process id = 0, 1, ..., R-1 */

shared unsigned int access = 1;		// Note that access is an integer, not a boolena

void enter_critical_section(unsigned int own_pid)
{
	// Beginning of atomic operation
	if (access == 0) 
		block(own_pid);
	
	else access -= 1;
	// Ending of atomic operation
}

void leave_critical_section(unsigned int own_pid)
{
	// Beginning of atomic operation
	if (there_are_blocked_processes)
		wake_up_one();
	 else access += 1;
	// Ending of atomic operation
}
```

```c
/* producers - p = 0, 1, ..., N-1 */
void producer(unsigned int p)
{
	DATA data;
	forever
	{
		produce_data(&data);
		bool done = false;
		do
		{
			lock(p);
			if (fifo.notFull())
			{
				fifo.insert(data);
				done = true;
			}
		unlock(p);
	} while (!done);
	do_something_else();
	}
}
```

```c
/* consumers - c = 0, 1, ..., M-1 */
void consumer(unsigned int c)
{
	DATA data;
	forever
	{
		bool done = false;
		do
		{
			lock(c);
			if (fifo.notEmpty())
			{
				fifo.retrieve(&data);
				done = true;
			}
			unlock(c);
		} while (!done);
		consume_data(data);
		do_something_else();
	}
}
```

---
title: Semáforos
---

# Semáforos
No ficheiro `IPC.md` são indicadas as condições e informação base para:

- Sincronizar a entrada na zona crítica
- Para serem usadas em programação concurrente
- Criar zonas que garantam a exclusão mútua

Semáforos são **mecanismos** que permitem por implementar estas condições e **sincronizar a atividade** de **entidades concurrentes em ambiente multiprogramado**

Não são nada mais do que **mecanismos de sincronização**.


## Implementação
Um semáforo é implementado através de:

- Um tipo/estrutura de dados
- Duas operações **atómicas**:
	- down (ou wait)
	- up (ou signal/post)

```c
typedef struct
{
	unsigned int val;	/* can not be negative */
	PROCESS *queue;		/* queue of waiting blocked processes */
} SEMAPHORE;
```

### Operações
As únicas operações permitidas são o **incremento**, up, ou **decremento**, down, da variável de controlo. A variável de controlo, `val`, **só pode ser manipulada através destas operações!**

Não existe uma função de leitura nem de escrita para `val`.

- `down`
	- **bloqueia** o processo se `val == 0`
	- **decrementa** `val` se `val != 0`
- `up`
	- Se a `queue` não estiver vazia, **acorda** um dos processos
	- O processo a ser acordado depende da **política implementada**
	- **Incrementa** `val` se a `queue` estiver vazia

### Solução típica de sistemas _uniprocessor_

```c
/* array of semaphores defined in kernel */
#define R /* semid = 0, 1, ..., R-1 */

static SEMAPHORE sem[R];

void sem_down(unsigned int semid)
{
	disable_interruptions;
	if (sem[semid].val == 0)
		block_on_sem(getpid(), semid);
	else
		sem[semid].val -= 1;
	enable_interruptions;
}

void sem_up(unsigned int semid)
{
	disable_interruptions;
	if (sem[sem_id].queue != NULL)
		wake_up_one_on_sem(semid);
	else
		sem[semid].val += 1;
	enable_interruptions;
}
```

A solução apresentada é típica de um sistema _uniprocessor_ porque recorre à diretivas **disable_interrutions** e **enable_interruptions** para garantir a exclusão mútua no acesso à zona crítica.

Só é possível garantir a exclusão mútua nestas condições se o sistema só possuir um único processador, poruqe as diretivas irão impedir a interrupção do processo que está na posse do processador devido a eventos externos. Esta solução não funciona para um sistema multi-processador porque ao  executar a diretiva **disable_interrutions**, só estamos a **desativar as interrupções para um único processador**. Nada impede que noutro processador esteja a correr um processo que vá aceder à mesma zona de memória partilhada, não sendo garantida a exclusão mútua para sistemas multi-processador.

Uma solução alternativa seria a extensão do **disable_interruptions** a todos os processadores. No entanto, iriamos estar a impedir a troca de processos noutros processadores do sistema que poderiam nem sequer tentar aceder às variáveis de memória partilhada.

## Bounded Buffer Problem

```c
shared FIFO fifo; /* fixed-size FIFO memory */

/* producers - p = 0, 1, ..., N-1 */
void producer(unsigned int p)
{
	DATA data;
	forever
	{
		produce_data(&data);
		bool done = false;
		do
		{
			lock(p);
			if (fifo.notFull())
			{
				fifo.insert(data);
				done = true;
			}
			unlock(p);
		} while (!done);
	do_something_else();
	}
}

/* consumers - c = 0, 1, ..., M-1 */
void consumer(unsigned int c)
{
	DATA data;
	forever
	{
		bool done = false;
		do
		{
			lock(c);
			if (fifo.notEmpty())
			{
				fifo.retrieve(&data);
				done = true;
			}
			unlock(c);
		} while (!done);
		consume_data(data);
		do_something_else();
	}
}
```

### Como Implementar usando semáforos?
A solução para o _Bounded-buffer Problem_ usando semáforos tem de:

- Garantir **exclusão mútua**
- Ausência de busy waiting

```c
shared FIFO fifo;	/*fixed-size FIFO memory */
shared sem access;	/*semaphore to control mutual exclusion */
shared sem nslots;	/*semaphore to control number of available slots*/
shared sem nitems;	/*semaphore to control number of available items */


/* producers - p = 0, 1, ..., N-1 */
void producer(unsigned int p)
{
	DATA val;

	forever
	{
		produce_data(&val);
		sem_down(nslots);
		sem_down(access);
		fifo.insert(val);
		sem_up(access);
		sem_up(nitems);
		do_something_else();
	}
}

/* consumers - c = 0, 1, ..., M-1 */
void consumer(unsigned int c)
{
	DATA val;

	forever
	{
		sem_down(nitems);
		sem_down(access);
		fifo.retrieve(&val);
		sem_up(access);
		sem_up(nslots);
		consume_data(val);
		do_something_else();
	}
}
```

Não são necessárias as funções `fifo.empty()` e `fifo.full()` porque são implementadas indiretamente pelas variáveis:

- **nitens:** Número de "produtos" prontos a serem "consumidos"
	- Acaba por implementar, indiretamente, a funcionalidade de verificar se a FIFO está empty
- **nslots:** Número de slots livres no semáforo. Indica quantos mais "produtos" podem ser produzidos pelo "consumidor"
	- Acaba por implementar, indiretamente, a funcionalidade de verificar se a FIFO está full


Uma alternativa **ERRADA** a uma implementação com semáforos é apresentada abaixo:

```c
shared FIFO fifo;	/*fixed-size FIFO memory */
shared sem access;	/*semaphore to control mutual exclusion */
shared sem nslots;	/*semaphore to control number of available slots*/
shared sem nitems;	/*semaphore to control number of available items */


/* producers - p = 0, 1, ..., N-1 */
void producer(unsigned int p)
{
	DATA val;

	forever
	{
		produce_data(&val);
		sem_down(access);		// WRONG SOLUTION! The order of this
		sem_down(nslots);		// two lines are changed 
		fifo.insert(val);
		sem_up(access);
		sem_up(nitems);
		do_something_else();
	}
}

/* consumers - c = 0, 1, ..., M-1 */
void consumer(unsigned int c)
{
	DATA val;

	forever
	{
		sem_down(nitems);
		sem_down(access);
		fifo.retrieve(&val);
		sem_up(access);
		sem_up(nslots);
		consume_data(val);
		do_something_else();
	}
}
```

A diferença entre esta solução e a anterior está na troca de ordem de instruções  `sem_down(access)` e `sem_down(nslots)`. A função `sem_down`, ao contrário das funções anteriores, **decrementa** a variável, não tenta decrementar. 

Assim, o produtor tenta aceder à sua zona crítica sem primeiro decrementar o número de slots livres para ele guardar os resultados da sua produção _(needs_clarification)_

## Análise de Semáforos

### Vantagens
- **Operam ao nível do sistema operativo:**
	- As operações dos semáforos são implementadas no _kernel_
	- São disponibilizadas aos utilizadores através de _system_calls_
- São **genéricos** e **modulares**
	- por serem implementações de baixo nível, ganham **versatilidade**
	- Podem ser usados em qualquer tipo de situação de programão concurrente


### Desvantagens
- Usam **primitivas de baixo nível**, o que implica que o programador necessita de conhecer os **princípios da programação concurrente**, uma vez que são aplicadas numa filosofia _bottom-up_
		- Facilmente ocorrem **race conditions**
		- Facilmente se geram situações de **deadlock**, uma vez que **a ordem das operações atómicas são relevantes**
- São tanto usados para implementar **exclusão mútua** como para **sincronizar processos**

### Problemas do uso de semáforos
Como tanto usados para implementar **exclusão mútua** como para **sincronizar processos**, se as condições de acesso não forem satisfeitas, os processos são bloqueados **antes** de entrarem nas suas regisões críticas.

- Solução sujeita a erros, especialmente em situações complexas
	- pode existir **mais do que um ponto de sincronismos** ao longo do programa
	
## Semáforos em Unix/Linux

**POSIX:**

- Suportam as operações de `down` e `up`
	- `sem_wait`
	- `sem_trywait`
	- `sem_timedwait`
	- `sem_post`
- Dois tipos de semáforos:
	- **named semaphores:**
		- São criados num sistema de ficheiros virtual (e.g. /dev/sem)
		- Suportam as operações:
			- `sem_open`
			- `sem_close`
			- `sem_unlink`
	- **unnamed semaphores:**
		- São _memory based_
		- Suportam as operações
			- `sem_init`
			- `sem_destroy`

**System V:** 

- Suporta as operações:
	- `semget` : criação
	- `semop` : as diretivas `up` e `down`
	- `semctl` : outras operações



# Monitores
Mecanismo de sincronização de alto nível para resolver os problemas de sincronização entre processos, numa perspetiva __top-down__. Propostos independentemente por Hoare e Brinch Hansen

Seguindo esta filosofia, a **exclusão mútua** e **sincronização** são tratadas **separadamente**, devendo os processos:

1. Entrar na sua zona crítica
2. Bloquear caso nao possuam condições para continuar


Os monitores são uma solução que suporta nativamente a exclusão mútua, onde uma aplicação é vista como um conjunto de _threads_ que competem para terem acesso a uma estrutura de dados partilhada, sendo que esta estrutura só pode ser acedida pelos métodos do monitor.

Um monitor assume que todos os seus métodos **têm de ser executados em exclusão mútua**:

- Se uma _thread_ chama um **método de acesso** enquanto outra _thread_ está a exceutar outro método de acesso, a sua **execução é bloqueada** até a outra terminar a execução do método

A sincronização entre threads é obtida usando **variáveis condicionais**:

- `wait`: A _thread_ é bloqueada e colocada fora do monitor
- `signal`: Se existirem outras _threads_ bloqueadas, uma é escolhida para ser "acordada"


## Implementação
```cpp
	monitor example
	{
	/* internal shared data structure */
	DATA data;
	
	condition c; /* condition variable */
	
	/* access methods */
	method_1 (...)
	{
		...
	}
	method_2 (...)
	{
		...
	}
	
	...

	/* initialization code */
	...
```

## Tipos de Monitores

### Hoare Monitor
![Diagrama da estrutura interna de um Monitor de Hoare](Pictures/hoare_monitor.png)

- Monitor de aplicação geral
- Precisa de uma stack para os processos que efetuaram um `wait` e são colocados em espera
- Dentro do monitor só se encontra a _thread_ a ser executada por ele
- Quando existe um `signal`, uma _thread_ é **acordada** e posta em execução


### Brinch Hansen Monitor
![Diagrama da estrutura interna de um Monitor de Brinch Hansen](Pictures/brinch_hansen_monitor.png)

- A última instrução dos métodos do monitor é `signal`
	- Após o `signal` a  _thread_ sai do monitor
- **Fácil de implementar:** não requer nenhuma estrutura externa ao monitor
- **Restritiva:** **Obriga** a que cada método só possa possuir uma instrução de `signal`


### Lampson/Redell Monitors
![Diagrama da estrutura interna de um Monitor de Lampson/Redell](Pictures/lampson_redell_monitor.png)

- A _thread_ que faz o `signal` é a que continua a sua execução (entrando no monitor)
- A _thread_ que é acordada devido ao `signal` fica fora do monitor, **competindo pelo acesso** ao monitor
- Pode causar **starvation**.
	- Não existem garantias que a __thread__ que foi acordada e fica em competição por acesso vá ter acesso
	- Pode ser **acordada** e voltar a **bloquear**
	- Enquanto está em `ready` nada garante que outra _thread_ não dê um `signal` e passe para o estado `ready`
	- A _thread_ que ti nha sido acordada volta a ser **bloqueada**


## Bounded-Buffer Problem usando Monitores
```c
shared FIFO fifo;			 /* fixed-size FIFO memory */
shared mutex access;		 /* mutex to control mutual exclusion */
shared cond nslots;		 /* condition variable to control availability of slots*/
shared cond nitems;		 /* condition variable to control availability of items */

/* producers - p = 0, 1, ..., N-1 */
void producer(unsigned int p)
{
	DATA data;
	forever
	{
		produce_data(&data);
		lock(access);
		if/while (fifo.isFull())
		{
			wait(nslots, access);
		}
		fifo.insert(data);
		unlock(access);
		signal(nitems);
		do_something_else();
	}
}

/* consumers - c = 0, 1, ..., M-1 */
void consumer(unsigned int c)
{
	DATA data;
	forever
	{
		lock(access);
		if/while (fifo.isEmpty())
		{
			wait(nitems, access);
		}
		fifo.retrieve(&data);
		unlock(access);
		signal(nslots);
		consume_data(data);
		do_something_else();
	}
}
```

O uso de `if/while` deve-se às diferentes implementações de monitores:

- `if`: **Brinch Hansen** 
	- quando a _thread_ efetua o `signal` sai imediatamente do monitor, podendo entrar logo outra _thread_
- `while`: **Lamson Redell**
	- A _thread_ acordada fica à espera que a _thread_ que deu o `signal` termine para que possa **disputar** o acesso
	

- O `wait` internamente vai **largar a exlcusão mútua**
	- Se não larga a exclusão mútua, mais nenhum processo consegue entrar
	- Um wait na verdade é um  `lock(..)` seguid de `unlock(...)`
- Depois de efetuar uma **inserção**, é preciso efetuar um `signal` do nitems
- Depois de efetuar um **retrieval** é preciso fazer um `signal` do nslots
	- Em comparação, num semáforo quando faço o up é sempre incrementado o seu valor
- Quando uma _thread_ emite um `signal` relativo a uma variável de transmissão, ela só **emite** quando alguém está à escuta
	- O `wait` só pode ser feito se a FIFO estiver cheia
	- O `signal` pode ser sempre feito
	
É necessário existir a `fifo.empty()` e a `fifo.full()` porque as variáveis de controlo não são semáforos binários.

O valor inicial do **mutex** é 0.


## POSIX support for monitors
A criação e sincronização de _threads_ usa o _Standard POSIX, IEEE 1003.1c_.

O _standard_ define uma API para a **criação** e **sincronização** de _threads_, implementada em unix pela biblioteca _pthread_

O conceito de monitor **não existe**, mas a biblioteca permite ser usada para criar monitores _Lampsom/Redell_ em C/C++, usando:

- `mutexes`
- `variáveis de condição`

	
As funções disponíveis são:

- `ptread_create`: **cria** uma nova _thread_ (similar ao _fork_)
- `ptread_exit`: equivalente à `exit`
- `ptread_join`: equivalente à `waitpid`
- `ptread_self`: equivalente à `getpid`
- `pthread_mutex_*`: manipulação de **mutexes**
- `ptread_cond_*`: manipulação de **variáveis condicionais**
- `ptread_once`: inicialização

# Message-passing

Os processos podem comunicar entre si usando **mensagens**. 

- Não existe a necessidade de possuirem memória partilhada
- Mecanismos válidos quer para sistemas **uniprocessador** quer para sistemas **multiprocessador**

	
A **comunicação** é efetuada através de **duas operações**:

- `send`
- `receive`

Requer a existência de um **canal de comunicação**. Existem 3 implementações possíveis:

1. **Endereçamento direto/indireto**
2. Comunicação **síncrona/assíncrona**
	- Só o `sender` é que indica o **destinatário**
	- O destinatário **não indica** o `sender`
	- Quando existem **caixas partilhadas**, normalmente usam-se mecanismos com políticas de **round-robin**
		1. Lê o processo $N$
		2. Lê o processo $N+1$
		3. etc...
	- No entanto, outros métodos podem ser usados
3. **Automatic or expliciting buffering**

## Direct vs Indirect

### Symmetric direct communication
O processo que pretende comunicar deve **explicitar o nome do destinatário/remetente:**

- Quando o `sender` envia uma mensagem tem de indicar o **destinatário**
	- `send(P, message`
- O destinatário tem de indicar de quem **quer receber** (`sender`)
	- `receive(P, message)`


A comunicação entre os **dois processos** envolvidos é **peer-to-peer**, e é estabelecida automaticamente entre entre um conjunto de processos comunicantes, só existindo **um canal de comunicação**

## Assymetric direct communications
Só o `sender` tem de explicitar o destinatário:

- `send(P, message`: 
- `receive(id, message)`: receve mensagens de qualquer processo

## Comunicação Indireta
As mensagens são enviadas para uma **mailbox** (caixa de mensagens) ou **ports**, e o `receiver` vai buscar as mensagens a uma `poll`

- `send(M, message`
- `receive(M, message)`


O canal de comunicação possui as seguintes propriedades:

- Só é estabelecido se o **par de processos** comunicantes possui uma **`mailbox` partilhada**
- Pode estar associado a **mais do que dois processos**
- Entre um par de processos pode existir **mais do que um link** (uma mailbox por cada processo)


Questões que se levantam. Se **mais do que um processo** tentar **receber uma mensagem da mesma `mailbox`**...

- ... é permitido?
	- Se sim. qual dos processos deve ser bem sucedido em ler a mensagem?


## Implementação
Existem várias opções para implementar o **send** e **receive**, que podem ser combinadas entre si:

- **blocking send:** o `sender` **envia** a mensagem e fica **bloquedo** até a mensagem ser entregue ao processo ou mailbox destinatária
- **nonblocking send:** o `sender` após **enviar** a mensagem, **continua** a sua execução
- **blocking receive:** o `receiver` bloqueia-se até estar disponível uma mensagem para si
- **nonblocking receiver:** o `receiver` devolve a uma mensagem válida quando tiver ou uma indicação de que não existe uma mensagem válida quando não tiver

## Buffering
O link pode usar várias políticas de implementação:

- **Zero Capacity:** 
	- Não existe uma `queue`
	- O `sender` só pode enviar uma mensagem de cada vez. e o envio é **bloqueante**
	- O `receiver` lê uma mensagem de cada vez, podendo ser bloqueante ou não
- **Bounded Capacity:**
	- A `queue` possui uma capacidade finita
	- Quando está cheia, o `sender` bloqueia o envio até possuir espaço disponível
- **Unbounded Capacity:**
	- A `queue` possui uma capacidade (potencialmente) infinita
	- Tanto o `sender` como o `receiver` podem ser **não bloqueantes**
 
## Bound-Buffer Problem usando mensanges
```c
shared FIFO fifo;			 /* fixed-size FIFO memory */
shared mutex access;		 /* mutex to control mutual exclusion */
shared cond nslots;		 /* condition variable to control availability of slots*/
shared cond nitems;		 /* condition variable to control availability of items */

/* producers - p = 0, 1, ..., N-1 */
void producer(unsigned int p)
{
	DATA data;
	MESSAGE msg;

	forever
	{
		produce_data(&val);
		make_message(msg, data);
		send(msg);
		do_something_else();
	}
}

/* consumers - c = 0, 1, ..., M-1 */
void consumer(unsigned int c)
{
	DATA data;
	MESSAGE msg;

	forever
	{
		receive(msg);
		extract_data(data, msg);
		consume_data(data);
		do_something_else();
	}
}
```

## Message Passing in Unix/Linux
**System V:**

- Existe uma fila de mensagens de **diferentes tipos**, representados por um inteiro
- `send` **bloqueante** se **não existir espaço disponível**
- A receção possui um argumento para espcificar o **tipo de mensagem a receber**:
	- Um tipo específico
	- Qualquer tipo
	- Um conjunto de tipos
- Qualquer que seja a política de receção de mensagens:
	- É sempre **obtida** a mensagem **mais antiga** de uma dado tipo(s)
	- A implementação do `receive` pode ser **blocking** ou **nonblocking**
- System calls:
	- `msgget`
	- `msgsnd`
	- `msgrcv`
	- `msgctl`

**POSIX**

- Existe uma **priority `queue`**
- `send` **bloqueante** se **não existir espaço disponível**
- `receive` obtêm a mensagem **mais antiga** com a **maior prioridade**
	- Pode ser blocking ou nonblocking
- Funções:
	- `mq_open`
	- `mq_send`
	- `mq_receive`

---
title: Shared Memory

author: Pedro Martins
---

# Shared Memory in Unix/Linux
- É um recurso gerido pelo sistema operativo

Os espaços de endereçamento são **independentes** de processo para processo, mas o **espaço de endereçamento** é virtual, podendo a mesma **região de memória física**(memória real) estar mapeada em mais do que uma **memórias virtuais**

## POSIX Shared Memory
- Criação:
	- `shm_open`
	- `ftruncate`
- Mapeamento:
	- `mmap`
	- `munmap`
- Outras operações:
	- `close`
	- `shm_unlink`
	- `fchmod`
	- ...

## System V Shared Memory
- Criação:
	- `shmget`
- Mapeamento:
	- `shmat`
	- `shmdt`
- Outras operações:
	- `shmctl`

---
title: "Deadlock"
author: Pedro Martins
---

# Deadlock
- **recurso:** algo que um processo precisa para proseeguir com a sua execução. Podem ser:
	- **componentes físicos** do sistema computacional, como:
		- processador
		- memória
		- dispositivos de I/O
		- ...
	- **estruturas de dados partilhadas**. Podem estar definidas 
		- Ao nível do sistema operativo
			- PCT
			- Canais de Comunicação
		- Entre vários processos de uma aplicação

Os recursos podem ser:

- **preemptable:** podem ser retirados aos processos que estão na sua posse por entidades externas
	- processador
	- regiões de memória usadas no espaço de endereçamento de um processo
- **non-preemptable:** os recursos só podem ser libertados pelos processos que estão na sua posse
	- impressoras
	- regiões de memória partilhada que requerem acesso por exclusão mútua

	
O **deadlock** só é importante nos recursos **non-preemptable**.

O caso mais simples de deadlock ocorre quando:

1. O processo $P_0$ pede a posse do recurso $A$
	- É lhe dada a posse do recurso $A$, e o processo $P_0$ passa a possuir o recurso $A$ em sua posse
2. O processo $P_1$ pede a posse do recurso $B$
	- É lhe dada a posse do recurso $B$, e o processo $P_1$ passa a possuir o recurso $B$ em sua posse
3. O processo $P_0$ pede agora a posse do recurso $B$ 
	- Como o recurso $B$ está na posse do processo $P_1$, é lhe negado
	- O processo $P_0$ fica em espera que o recurso $B$ seja libertado para puder continuar a sua execução
	- No entanto, o processo $P_0$ não liberta o recurso $A$
4. O processo $P_1$ necessita do recurso $A$
	- Como o recurso $A$ está na posse do processo $P_0$, é lhe negado
	- O processo $P_1$ fica em espera que o recurso $A$ seja libertado para puder continuar a sua execução
	- No entanto, o processo $P_1$ não liberta o recurso $B$
5. Estamos numa situação de **deadlock**. Nenhum dos processos vai libertar o recurso que está na sua posse mas cada um deles precisa do recurso que está na posse do outro

## Condições necessárias para a ocorrência de deadlock
Existem 4 condições necessárias para a ocorrência de **deadlock:**

1. **exclusão mútua:**
	- Pelo menos um dos recursos fica em posse de um processo de forma não partilhável
	- Obriga a que outro processo que precise do recurso espere que este seja libertado
2. **hold and wait:**
	- Um processo mantêm em posse pelo menos um recurso enquanto espera por outro recurso que está na posse de outro processo
3. **no preemption:**
	- Os recursos em causa são non-preemptive, o que implica que só o processo na posse do recurso o pode libertar
4. **espera circular:**
	- é necessário um conjunto de processos em espera tais que cada um deles precise de um recurso que está na posse de outro processo nesse conjunto

Se **existir deadlock**, todas estas condições se verificam. _(A => B)_

Se **uma delas não se verifica**, não há deadlock. _(~B => ~A)_

### O Problema da Exclusão Mútua 
Dijkstra em 1965 enunciou um conjunto de regras para garantir o acesso **em exclusão mútua** por processo em competição por recursos de memória partilhados entre eles.[^1]

1. **Exclusão Mútua:** Dois processos não podem entrar nas suas zonas críticas ao mesmo tempo
2. **Livre de Deadlock:** Se um process está a tentar entrar na sua zona crítica, eventualemnte algum processo (não necessariamento o que está a tentar entrar), mas entra na sua zona crítica
3. **Livre de Starvation:** Se um processo está atentar entrar na sua zona crítica, eentão eventualemnte esse processo entr na sua zona crítica
4. **First-In-First-Out:** Nenhum processo qa iniciar pode entrar na sua zona crítica antes de um processo que já está à espera do seu trunos para entrar na sua zona crítica



[^1]: _"Concurrent Programming, Mutual Exclusion (1965; Dijkstra)"._ Gadi Taubenfeld, The Interdisciplinary Center, Herzliya, Israel

## Jantar dos Filósofos
- 5 filósofos sentados à volta de uma mesa, com comida à sua frente
	- Para comer, cada filósofo precisa de 2 garfos, um à sua esquerda e outro à sua direita
	- Cada filósofo alterna entre períodos de tempo em que medita ou come
- Cada **filósofo** é um **processo/thread** diferente
- Os **garfos** são os **recursos**

Uma possível solução para o problema é:

![Ciclo de Vida de um filósofo](Pictures/philosopher_life.png)

```c
enum {MEDITATING, HUNGRY, HOLDING, EATING};

typedef struct TablePlace
{
	int state;
} TablePlace;

typedef struct Table
{
	Int semid;
	int nplaces;
	TablePlace place[0];
} Table;

int set_table(unsigned int n, FILE *logp);
int get_hungry(unsigned int f);
int get_left_fork(unsigned int f);
int get_right_fork(unsigned int f);
int drop_forks(unsigned int f);
```

Quando um filósofo fica _hungry_:

1. Obtém o garfo à sua esquerda
2. Obtém o garfo à sua direita

A solução **pode sofrer de deadlock:**

1. **exclusão mútua:**
	- Os garfos são partilháveis
2. **hold and wait:**
	- Se conseguir adquirir o `left_fork`, o filósofo fica no estado `holding_left_fork` até conseguir obter o `right_fork` e não liberta o `left_fork`
3. **no preemption:**
	- Os garfos são recursos non-preemptive. Só o filósofo é que pode libertar os seus garfos após obter a sua posse e no fim de comer
4. **espera circular:**
	- Os garfos são partilhados por todos os filósofos de forma circular
		- O garfo à esquerda de um filósofo, `left_fork` é o garfo à direita do outro, `right_fork`

Se todos os filósofos estiverem a pensar e decidirem comer, pegando todos no garfo à sua esquerda ao mesmo tempo, entramos numa situação de **deadlock**.

## Prevenção de Deadlock
Se uma das condições necessárias para a ocorrência de deadlock não se verificar, não ocorre deadlock.

As **políticas de prevenção de deadlock** são bastantes **restritas**, **pouco efetivas** e **difíceis de aplicar** em várias situações.

- **Negar a exclusão mútua** só pode ser aplicada a **recursos partilhados**
- **Negar _hold-and-wait_** requer **conhecimento _a priori_ dos recursos necessários** e considera sempre o pior caso, no qual os recursos são todos necessários em simultâneo (o que pode não ser verdade)
- **Negar _no preemption_**, impondo a libertação (e posterior re-aquisição) de recursos adquiridos por processos que não têm condições (aka, todos os recursos que precisam) para continuar a execução pode originar grandes atrasos na execução da tarefa
- **Negar a _circular wait_** pode resultar numa má gestão de recursos

### Negar a exclusão mútua
- Só é possível se os recursos puderem ser partilhados, senão podemos incorrer em **race conditions**
- Não é possível no jantar dos filśofos, porque os garfos não podem ser partilhados entre os filósofos
- Não é a condição mais vulgar a negar para prevenir _deadlock_

### Negar _hold-and-wait_
- É possível fazê-lo se um processo é obrigado a pedir todos os recursos que vai precisar antes de iniciar, em vez de ir obtendo os recursos à medida que precisa deles
- Pode ocurrer **starvation**, porque um processo pode nunca ter condições para obter nenhum recurso
	- É comum usar _aging mechanisms_ to para resolver este problema
- No jantar dos filósofos, quando um filósofo quer comer, passa a adquirir os dois garfos ao mesmo tempo
	- Se estes não tiverem disponíveis, o filósofo espera no `hungry state`, podendo ocorrer **starvation**

![Negar _hold-and-wait_](Pictures/hold_and_wait_denial.png)

Solução equivalente à proposta por Dijkstra.

### Negar _no preemption_
- A condição de os recursos serem _non-preemptive_ pode ser implementada fazendo um processo libertar o(s) recurso(s) que possui se não conseguir adquirir o próximo recurso que precisa para continuar em execução
- Posteriormente o processo tenta novamente adquirir esses recursos
- Pode ocorrer **starvation** and **busy waiting**
	- podem ser usados _aging mechanisms_ para resolver a starvation
	- para evitar busy waiting, o processo pode ser bloqueado e acordado quando o recurso for libertado
- No janta dos filósofos, o filósofo tenta adquirir o `left_fork`
	- Se conseguir, tenta adquirir o `right_fork`
		- Se conseguir, come
		- Se não conseguir, liberta o `left_fork` e volta ao estado `hungry`


![Negar a condição de  _no preemption_ dos recursos](Pictures/no_preempt_denial.png)


### Negar a espera circular
- Através do uso de IDs atribuídos a cada recurso e impondo uma ordem de acesso (ascendente ou descendente) é possível evitar sempre a espera em círculo
- Pode ocorrer **starvation**
- No jantar dos filósofos, isto implica que nalgumas situações, um dos filśofos vai precisar de adquirir primeiro o `right_fork` e de seguida o `left_fork`
	- A cada filósofo é atribuído um número entre 0 e N
	- A cada garfo é atribuído um ID (e.g., igual ao ID do filósofo à sua direita ou esquerda)
	- Cada filśofo adquire primeiro o garfo com o menro ID
	- obriga a que os filósofos 0 a N-2 adquiram primeiro o `left_fork` enquanto o filósofo N-1 adquir primeiro o `right_fork`

![Negar a condição de espera circular no acesso aos recursos](Pictures/circular_wait_denial.png)

 
## Deadlock Avoidance
Forma menos restritiva para resolver situações de deadlock, em que **nenhuma das condições necessárias à ocorrência de deadlock é negada**. Em contrapartida, o sistema é **monitorizado continuamente** e um recurso **não é atribuído** se como consequência o sistema entrar num **estado inseguro/instável**

Um estado é considerado seguro se existe uma sequência de atribuição de recursos na qual todos os processos possa terminar a sua execução (não ocorrendo _deadlock_). 

Caso contrário, poderá ocorrer deadlock (pode não ocorrer, mas estamos a considerar o pior caso) e o estado é considerado inseguro.

Implica que:

- exista uma lista de todos os recursos do sistema
- os processos intervenientes têm de declarar _a priori_ todas as suas necessidades em termos de recursos


### Condições para lançar um novo processo
Considerando:

- $NTR_i$ - o número total de recursos do tipo `i` _(i = 0, 1, ..., N-1)_
- $R_{i, j}$: o número de recursos do tipo `i` requeridos pelo processo `j`, _(i=0, 1, ..., N-1 e j=0, 1, ..., M-1)_

O sistema pode impedir um novo processo, `M`, de ser exectuado se a sua terminação não pode ser garantida. Para que existam certezas que um novo processo pode ser terminado após ser lançado, tem de se verificar:

$$ NTR_i \geq R_{i, M} + \sum_{j=0}^{M-1} R_{i, j} $$

 
### Algoritmo dos Banqueiros
Considerando:

- $NTR_i$: o número total de recursos do tipo `i` _(i = 0, 1, ..., N-1)_
- $R_{i, j}$: o número de recursos do tipo `i` requeridos pelo processo `j`, _(i=0, 1, ..., N-1 e j=0, 1, ..., M-1)_
- $A_{i, j}$: o número de recursos do tipo `i` atribuídos/em posse do processo `j`, _(i=0, 1, ..., N-1 e j=0, 1, ..., M-1)_

Um novo recurso do tipo `i` só pode ser atribuído a um processo **se e só se** existe uma sequência $j'=f(i, j)$ tal que:

$$ R_{i, j'} - A_{i, j'}  < \sum_{k \geq ji'}^{M-1} A_{i, k} $$

\begin{table}[H]
\renewcommand{\arraystretch}{1.3}
\centering
\caption{Banker's  Algorithm Example} 
\begin{tabular}{cccccc}
						   &                            & A & B & C & D \\ \cline{3-6} 
						   & \multicolumn{1}{c}{total}  & 6 & 5 & 7 & 6 \\
						   & \multicolumn{1}{c}{free}   & 3 & 1 & 1 & 2 \\ \toprule
\multirow{3}{*}{maximum}   & p1                         & 3 & 3 & 2 & 2 \\
						   & p2                         & 1 & 2 & 3 & 4 \\
			 			   & p3                         & 1 & 3 & 5 & 0 \\ \midrule
\multirow{3}{*}{granted}   & p1                         & 1 & 2 & 2 & 1 \\
						   & p2                         & 1 & 0 & 3 & 3 \\
						   & p3                         & 1 & 2 & 1 & 0 \\ \midrule
\multirow{3}{*}{needed}    & p1                         & 2 & 1 & 0 & 1 \\
						   & p2                         & 0 & \cellcolor{blue!25}2 & 0 & 1 \\
						   & p3                         & 0 & 1 & \cellcolor{blue!25}4 & 0 \\ \midrule
\multirow{3}{*}{new Grant} & p1                         & 0 & 0 & 0 & 0 \\
						   & p2                         & 0 & 0 & 0 & 0 \\
						   & p3                         & 0 & 0 & 0 & 0 \\ \bottomrule
\end{tabular}
\end{table}

Para verificar se posso atribuir recursos a um processo, aos recursos `free` subtraio os recursos `needed`, ficando com os recursos que sobram. Em seguida simulo o que aconteceria se atribuisse o recurso ao processo, tendo em consideração que o processo pode usar o novo recurso que lhe foi atribuído sem libertar os que já possui em sua posse (estou a avaliar o pior caso, para garantir que não há deadlock)

Se o processo **p3** pedir 2 recursos do tipo C, o **pedido é negado**, porque **só existe 1 disponível**

Se o processo **p3** pedir 1 recurso do tipo B, o **pedido é negado**, porque apesar de existir 1 recurso desse tipo disponível, ao **longo da sua execução processo vai necessitar de 4** e só **existe 1 disponível**, podendo originar uma situação de **deadlock**, logo o **acesso ao recurso é negado**

#### Algoritmo dos banqueiros aplicado ao Jantar dos filósofos
- Cada filósofo primeiro obtém o `left_fork` e depois o `right_fork`
- No entanto, se um dos filósofos tentar obter um `left_fork` e o filósofo à sua esquerda já tem na sua posse um `left_fork`, o acesso do filósofo sem garfos ao `left_fork` é negado para não ocorrer **deadlock**

![Algoritmo dos banqueiros aplicado ao Jantar dos filósofos](Pictures/bankers_philosophers.png)


## Deadlock Detection
**Não são usados mecanismos nem para prevenir nem par evitar o deadlock**, podendo ocorrer situações de deadlock:

- O estado dos sistema deve ser examinado para determinar se ocorreu uma situação de deadlock
	- É preciso verificar se existe uma **dependência circular de recursos** entre os processos
	- Periodicamente é executado um algoritmo que verifica o estado do registo de recursos:
		- recursos `free` vs recursos `granted` vs recursos `needed`
	- Se tiver ocorrido uma situação de deadlock, o SO deve possuir uma **rotina de recuperação** de situações de deadlock e executá-la
- Alternativamente, de um ponto de vista "arrogante", o problema pode ser ignorado


Se **ocorrer uma situação de deadlock**, a rotina de recuperação deve ser posta em prática com o objetivo de interromper a dependência circular de processos e recursos.

Existem três métodos para recuperar de deadlock:

- **Libertar recursos de um processo**, se possível
	- É atividade de um processo é suspensa até se puder devolver o recurso que lhe foi retirado
	- Requer que o estado do processo seja guardado e em seguida recarregado
	- Método eficiente
- **Rollback**
	- O estado de execução dos diferentes processos é guardado periodicamente
	- Um dos processos envolvidos na situação de deadlock é _rolled back_ para o instante temporal em que o recurso lhe foi atribuído
	- A recurso é assim libertado do processo
- **Matar o processo**
	- Quando um processo entra em deadlock, é terminado
	- Método radical mas fácil de implementar

	
Alternativamente, existe sempre a opção de não fazer nada, entrando o processo em deadlock. Nestas situações, o utilizador é que é responsável por corrigir as situações de deadlock, por exemplo, terminando o programa com `CTRL + C`


