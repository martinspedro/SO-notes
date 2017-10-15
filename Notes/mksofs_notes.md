# **msksofs17**
Creates the sofs17 filesystem by formatting the disk

# Functions
Functions
```c
void 	computeStructure (uint32_t ntotal, uint32_t itotal, uint32_t *itsizep, uint32_t *rmsizep, uint32_t *ctotalp)
 	//computes the structural division of the disk More...
 
void 	fillInSuperBlock (const char *name, uint32_t ntotal, uint32_t itsize, uint32_t rmsize)
 	// Fill in the fields of the superblock. 
 
void 	fillInInodeTable (uint32_t itstart, uint32_t itsize)
 	Fill in the blocks of the inode table. More...
 
void 	fillInFreeClusterTable (uint32_t rmstart, uint32_t ctotal)
 	Fill in the blocks of the free cluster table. More...
 
void 	fillInRootDir (uint32_t rtstart)
 	Fill in the root directory. More...
 
void 	resetClusters (uint32_t cstart, uint32_t ctotal)
 	Fill with zeros the given set of clusters. More...
 
Detailed Description

Formatting functions.

Author
    Artur Pereira - 2007-2009, 2016-2017 
    Miguel Oliveira e Silva - 2009, 2017 
    António Rui Borges - 2010-2015 

Function Documentation
void computeStructure 	( 	uint32_t  	ntotal,
		uint32_t  	itotal,
		uint32_t *  	itsizep,
		uint32_t *  	rmsizep,
		uint32_t *  	ctotalp 
	) 		

computes the structural division of the disk

The structural division is computed, possible adjusting the number of inodes in order to use the whole disk. If the given number of inodes is zero, a default value, equal to the number of blocks divided by 8, is used.

Parameters
    ntotal	total number of blocks of the device
    itotal	requested number of inodes
    itsizep	pointer to mem where to store the size of inode table in blocks
    rmsizep	pointer to mem where to store the size of cluster reference table in blocks
    ctotalp	pointer to mem where to store the number of clusters

void fillInFreeClusterTable 	( 	uint32_t  	rmstart,
		uint32_t  	ctotal 
	) 		

Fill in the blocks of the free cluster table.

There is a one to one correspondence between bits in the table and clusters. Bits on the table are considered to grow up from lower blocks to upper blocks, from lower bytes to upper bytes, and from most significant bits (MSB) to least significant bits (LSB). Thus, bit 0 corresponds to the most significant bit of the first byte of first block of the table. A bit at 1 means the corresponding cluster is free. Conversely, a bit at 0 means the corresponding cluster is in use. Bit 0, as the root occupies the first cluster, must be set in use. The number of bits in the table is, in general, greater than the number of clusters. The non used bits should be put as in use.

Parameters
    rmstart	the number of the fisrt block used by the bit table
    ctotal	the total number of clusters

void fillInInodeTable 	( 	uint32_t  	itstart,
		uint32_t  	itsize 
	) 		

Fill in the blocks of the inode table.

The inode 0 must be filled assuming it is used by the root directory. The other inodes are free

Parameters
    itstart	physical number of the first block used by the inode table
    itsize	number of blocks of the inode table

void fillInRootDir 	( 	uint32_t  	rtstart	) 	

Fill in the root directory.

The root directory occupies one cluster, with the first two slots filled in with "." and ".." directory entries. The other slots must be cleaned: field name filled with zeros and field inode filled with NullReference.

Parameters
    rtstart	number of the block where the root cluster starts.

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

void resetClusters 	( 	uint32_t  	cstart,
		uint32_t  	ctotal 
	) 		

Fill with zeros the given set of clusters.

Parameters
    cstart	number of the block of the first free cluster
    ctotal	number of clusters to be filled 
