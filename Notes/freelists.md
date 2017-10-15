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
