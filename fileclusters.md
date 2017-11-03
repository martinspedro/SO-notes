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
