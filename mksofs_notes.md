[WIP]



# **rawlevel**
Manipulation of the disk at block level

## Modules
- **mksofs**  : Formatting functions
- **rawdisk** : Access to disk blocks at raw level 
                     
## **msksofs**
Creates the sofs17 filesystem by formatting the disk
Função responsável por formatar o disco para criar o sistem de ficheiros sofs17

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

Por default ao indicar apenas um nome de ficheiro. O número de inodes é o número de clusters/8:

```bash
./mksofs ../disk.sofs1

Trying to install a 125-inodes SOFS17 file system in ../disk.sofs17.
Computing disk structure... 
Filling in the superblock fields... 
Filling in the table of inodes... 
Filling in the bitmap of free clusters... 
Filling in the root directory... 
144-inodes SOFS17 file system was successfully installed in ../disk.sofs17.
```

Ao usar a opção `-z` todos os clusters livres são preenchidos com zeros:

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

### Functions

#### computeStruture

Computes the structural division of the disk

The structural division is computed, possible adjusting the number of inodes in order to use the whole disk. If the given number of inodes is zero, a default value, equal to the number of blocks divided by 8, is used.

```c
void computeStructure (uint32_t ntotal, uint32_t itotal, uint32_t *itsizep, uint32_t *rmsizep, uint32_t *ctotalp)
 	//computes the structural division of the disk
```
 
##### Function Documentation
void computeStructure 	( 	uint32_t  	ntotal,
		uint32_t  	itotal,
		uint32_t *  	itsizep,
		uint32_t *  	rmsizep,
		uint32_t *  	ctotalp 
	) 		

##### Parameters
    ntotal	total number of blocks of the device
    itotal	requested number of inodes
    itsizep	pointer to mem where to store the size of inode table in blocks
    rmsizep	pointer to mem where to store the size of cluster reference table in blocks
    ctotalp	pointer to mem where to store the number of clusters



## fillInSuperBlock
void 	fillInSuperBlock (const char *name, uint32_t ntotal, uint32_t itsize, uint32_t rmsize)
 	// Fill in the fields of the superblock. 
 
As caches estão no spuerblock
 
void fillInSuperBlock 	( 	const char *  	name,
		uint32_t  	ntotal,
		uint32_t  	itsize,
		uint32_t  	rmsize 
	) 		

Fill in the fields of the superblock.

The magic number should be put at 0xFFFF.

Parameters
    name	volume name
    ntotal	the total number of blocks in the device
    itsize	the number of blocks used by the inode table
    rmsize	the number of blocks used by the cluster reference table


## fillInInodeTable
void 	fillInInodeTable (uint32_t itstart, uint32_t itsize)
 	Fill in the blocks of the inode table. More...

void fillInInodeTable 	( 	uint32_t  	itstart,
		uint32_t  	itsize 
	) 		

Fill in the blocks of the inode table.

The inode 0 must be filled assuming it is used by the root directory. The other inodes are free

Parameters
    itstart	physical number of the first block used by the inode table
    itsize	number of blocks of the inode table


## fillInFreeClusterTable
void 	fillInFreeClusterTable (uint32_t rmstart, uint32_t ctotal)
 	Fill in the blocks of the free cluster table. More...

void fillInFreeClusterTable 	( 	uint32_t  	rmstart,
		uint32_t  	ctotal 
	) 		

Fill in the blocks of the free cluster table.

There is a one to one correspondence between bits in the table and clusters. Bits on the table are considered to grow up from lower blocks to upper blocks, from lower bytes to upper bytes, and from most significant bits (MSB) to least significant bits (LSB). Thus, bit 0 corresponds to the most significant bit of the first byte of first block of the table. A bit at 1 means the corresponding cluster is free. Conversely, a bit at 0 means the corresponding cluster is in use. Bit 0, as the root occupies the first cluster, must be set in use. The number of bits in the table is, in general, greater than the number of clusters. The non used bits should be put as in use.

Parameters
    rmstart	the number of the fisrt block used by the bit table
    ctotal	the total number of clusters


Fill in the blocks of the free cluster table.

- There is a one to one correspondence between bits in the table and clusters. 
- Bits on the table are considered to grow up from lower blocks to upper blocks, from lower bytes to upper bytes, and from most significant bits (MSB) to least significant bits (LSB). 
	- Thus, bit 0 corresponds to the most significant bit of the first byte of first block of the table. 
	- A bit at 1 means the corresponding cluster is free. 
	- Conversely, a bit at 0 means the corresponding cluster is in use. 
	- Bit 0, as the root occupies the first cluster, must be set in use. 
- The number of bits in the table is, in general, greater than the number of clusters. 
- The non used bits should be put as in use.

```bash
void fillInFreeClusterTable (uint32_t rmstart, uint32_t ctotal)
# Fill in the block of the free cluster table
#
# PARAMETERS:
# 	- rmstart : the number of the first block used by the bit table
# 	- ctotal  : the total number of clusters
```

Problemas:
- É preciso ter em consideração que o número de clusters pode ser inferior ou superior ao tamanho de um bloco.
- É preciso ter em consideração que o primeiro bit do primeiro bloco deve estar em use
- É preciso ter em consderação que todos os bits que não referenciem um cluster devem ser colocados como em uso


Notas: \
- **rawdisk** contem todas as funções para mainuplar diretamente o disco
	- soOpenRawDisk - abre o disco
	```bash
	void soOpenRawDisk(const char *devname, uint32_t * np = NULL);
	```
	- soCloseRawDisk - fecha o disco em segurança
	```bash
	void soCloseRawDisk(void)
	```
	- soReadRawBlock - lê um bloco de dados do disco
	```bash
	 void soReadRawBlock(uint32_t n, void *buf);
	```
	- soWriteRawBlock - escreve um bloco de dados para o disco
	```bash
	void soWriteRawBlock(uint32_t n, void *buf)
	```
	 
- **datatypes**: um conjunto de constantes que podem ser usadas para aceder aos ficheiros
	- InodesPerBlock
	- ReferencesPerBlock
	- ReferencesPerCluster
	- ReferencesPerBitmapBlock
	- BlocksPerCluster
	- CLusterSize
	- DirentriesPerCluster
	- NullReference

- **exceptions** : sofs17 exception definition module
	```cpp
	struct SOException:public std::exception
		int en;             ///< (system) error number
		const char *msg;    ///< name of function that has thrown the exception
	```

- **SORefBlock** : estrutura dos Reference bitmaop Block data type
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

### 1000 blocks, 144-inodes, mksofs.bin64
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

### 200 blocks, 48-inodes, mksofs.bin64
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

### 10000 blocks, 1264 inodes, mksofs17.bin64
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

#### 100000 blocks, 12512 inodes, mksofs17.bin64




## fillInRootDir
void 	fillInRootDir (uint32_t rtstart)
 	Fill in the root directory. More...

void fillInRootDir 	( 	uint32_t  	rtstart	) 	

Fill in the root directory.

The root directory occupies one cluster, with the first two slots filled in with "." and ".." directory entries. The other slots must be cleaned: field name filled with zeros and field inode filled with NullReference.

Parameters
    rtstart	number of the block where the root cluster starts.


## resetClusters
void 	resetClusters (uint32_t cstart, uint32_t ctotal)
 	Fill with zeros the given set of clusters. More...

void resetClusters 	( 	uint32_t  	cstart,
		uint32_t  	ctotal 
	) 		

Fill with zeros the given set of clusters.

Parameters
    cstart	number of the block of the first free cluster
    ctotal	number of clusters to be filled 


-----

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

## Exemplo
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


