# freelists
Functions to manage the list of free inodes and the list of free clusters

## soAllocCluster
uint32_t soAllocCluster ( )

   Allocate a free cluster.

   The cluster is retrieved from the retrieval cache of free
   cluster references. If the cache is empty, it has to be
   replenished before the retrieval may take place.

   Returns
          the number of the allocated cluster


- Devolve um cluster livre da retrieval cache
- Se a cache estiver vazia, replenish
- Devolve o número do cluster alocado
- A cache tem o valor máximo quando está cheia
- quando está vazia o replenish é que dá reset ao idx
- o idx pode não ser 0 se não estirem referências para encher a retrieval cache

- Tenho de incluir o dealer do superblock (sbdealer)
	- **Não preciso de abrir o superblock**
	- obter pointeiro para o superblock
	- com o ponteiro obter a retrieval cache
- Tenho de decrementar o cfree do superblock
- Só incremento o idx da cache quando tiro um valor
- Tenho de escrever NullReference

### Tests
1. Superblock view
```
Header:
   Magic number: 0x41FD
   Version number: 0x2
   Volume name:
   Properly unmounted: no
   Number of mounts: 89
   Total number of blocks in the device: 0
Inode table metadata:
   First block of the inode table: 4294967295
   Number of blocks of the inode table: 4294967295
   Total number of inodes: 4294967295
   Number of free inodes: 4294967295
   Head of list of free inodes: (nil)
Free cluster table metadata:
   First block of the free cluster table: 4294967295
   Number of blocks of the free cluster table: 4294967295
   Index of first byte to retrieve references: 512
Clusters metadata:
   First block of the cluster zone: 0
   Total number of clusters: 0
   Number of free clusters: 0
   Retrieval cache:
      Index of the first filled cache element:  4294967295
      Cache contents:
         0 0 2 143 (nil) (nil) (nil) (nil) (nil) (nil)
         (nil) (nil) 512 0 0 0 0 0 3 1
         (nil) (nil) (nil) (nil) (nil) (nil) (nil) (nil) 512 0
         0 0 0 0 4 2 (nil) (nil) (nil) (nil)
         (nil) (nil) (nil) (nil) 512 0 0 0 0 0
         5 3 (nil)
   Insertion cache:
      Index of the first free cache element:  4294967295
      Cache contents:
         (nil) (nil) (nil) (nil) (nil) (nil) 512 0 0 0
         0 0 6 4 (nil) (nil) (nil) (nil) (nil) (nil)
         (nil) (nil) 512 0 0 0 0 0 7 5
         (nil) (nil) (nil) (nil) (nil) (nil) (nil) (nil) 512 0
         0 0 0 0 8 6 (nil) (nil) (nil) (nil)
         (nil) (nil) (nil)
```

2. Testtool opening
```bash
(691)--> soOpenDisk("disk.sofs17", (nil))
(991)--> soOpenRawDisk("disk.sofs17", (nil))
(891)--> soOpenSBDealer()
(891)--> --soOpenSBDealerBin()
(951)--> soReadRawBlock(0, 0x613200)
(721)--> soOpenITDealer()
(721)--> --soOpenITDealerBin()
(791)--> soOpenCZDealer()
(791)--> --soOpenCZDealerBin()
(851)--> sbGetPointer()
(851)--> --sbGetPointerBin()
```

3. Allocate first cluster
```bash
(501)--> soAllocCluster()
(501)--> --soAllocClusterBin()
(851)--> sbGetPointer()
(851)--> --sbGetPointerBin()
(542)--> soReplenish()
(542)--> --soReplenishBin()
(851)--> sbGetPointer()
(851)--> --sbGetPointerBin()
(951)--> soReadRawBlock(3, 0x7ffcbb541930)
(952)--> soWriteRawBlock(3, 0x7ffcbb541930)
(852)--> sbSaveBin()
(852)--> --sbSaveBin()
(952)--> soWriteRawBlock(0, 0x613200)
(852)--> sbSaveBin()
(852)--> --sbSaveBin()
(952)--> soWriteRawBlock(0, 0x613200)
Cluster number 1 allocated
```

4. Allocate cluster without replenish
```bash
(501)--> soAllocCluster()
(501)--> --soAllocClusterBin()
(851)--> sbGetPointer()
(851)--> --sbGetPointerBin()
(852)--> sbSaveBin()
(852)--> --sbSaveBin()
(952)--> soWriteRawBlock(0, 0x613200)
Cluster number 2 allocated
```

5. No space left on device
```bash
(501)--> soAllocCluster()
(501)--> --soAllocClusterBin()
(851)--> sbGetPointer()
(851)--> --sbGetPointerBin()
soAllocClusterBin: No space left on device (28).
```

6. Retrieval cache empty
```bash
(501)--> soAllocCluster()
(501)--> --soAllocClusterBin()
(851)--> sbGetPointer()
(851)--> --sbGetPointerBin()
(542)--> soReplenish()
(542)--> --soReplenishBin()
(851)--> sbGetPointer()
(851)--> --sbGetPointerBin()
(951)--> soReadRawBlock(19, 0x7ffec9df02c0)
(952)--> soWriteRawBlock(19, 0x7ffec9df02c0)
(852)--> sbSaveBin()
(852)--> --sbSaveBin()
(952)--> soWriteRawBlock(0, 0x613200)
(852)--> sbSaveBin()
(852)--> --sbSaveBin()
(952)--> soWriteRawBlock(0, 0x613200)
Cluster number 54 allocated
```

[NOTA]: 
1. Disco 10
```bash
./createDisk disk.sofs17 10 && ./mksofs.bin64 disk.sofs17 && ./testtool disk.sofs17 -l 0,999
```
Cluster number é sempre MAX_UINT32
Dá replenish ad eternum

2. Disco 100
```bash
./createDisk disk.sofs17 100 && ./mksofs.bin64 disk.sofs17 && ./testtool disk.sofs17 -l 0,999
```
Inicia com MAX_UINT32
Depois as referências ficam bem mas o idx começa em 30
Indice chega aos 53 com o cluster em 23. Dá replenish e core dumped

Para 200 a mesma coisa -> 48 inodes
Para 250 não -> 32 inodes
Para 300 não -> 48 inodes

[AQUI]
### sbdealer
 superblock dealer: mediates access to the disk
   superblock More...

   Functions

             void  soOpenSBDealer ()
                   Open the superblock dealer. More...

             void  soCloseSBDealer ()
                   Close the superblock dealer. More...

   SOSuperBlock *  sbGetPointer ()
                   Get a pointer to the superblock. More...

             void  sbSave ()
                   Save superblock to disk. More...

Detailed Description

   superblock dealer: mediates access to the disk
   superblock

   This module guarantees that only a single copy of the
   superblock is in memory, thus improving its
   consistency.

   Remarks
          In case an error occurs, every function throws a
          SOException

 SOSuperBlock* sbGetPointer ( )

   Get a pointer to the superblock.

   Load the superblock, if not done yet

   Returns
          Pointer to superblock

   void sbSave ( )

   Save superblock to disk.

   Do nothing if not loaded

   void soCloseSBDealer ( )

   Close the superblock dealer.

   Save superblock to disk and close dealer

   void soOpenSBDealer ( )

   Open the superblock dealer.

   Prepare the internal data structure of the superblock
   dealer












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
