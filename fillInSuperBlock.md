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


