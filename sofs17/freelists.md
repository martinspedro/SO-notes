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
