## soAllocCluster
- Aloca uma referência para um cluster livre
	- as referências para os clusters livres estão armazenadas na retrieval cache
- A referência de um cluster livre é o seu número
- A retrieval cache está cheia quando o indice de referências na cahce for 0


### Algoritmia
- Se a retrieval cache estiver vazia, chama a função replenish para encher a cache
	- A replenish trata de carregar para a retrieval cache as referências de clusters livres
	- O ridx pode não ser nulo se não exisitirem referências para encher a cache
- A allocCluster acede à retrieval cache para obter uma referência para um cluster livre
- Coloca a posição da cache de onde foi obtida a referência da retrieval cache como Null Reference
- Incrementa o ridx para apontar para a próxima referência livre
- Decrementa o cfree do superblock sempre que é alocado um cluster
- É preciso incluir o dealer do superblock (sbdealer)
	- A retrieval cache está no superblock
	- **Não preciso de abrir o superblock**
	- obter pointeiro para o superblock
	- com o ponteiro obter a retrieval cache

### Utilização
```c
uint32_t soAllocCluster ( )
```

#### Parameters
- None.



### Testes
1. Superblock view
ESTÀ ERRADO!!!!
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
