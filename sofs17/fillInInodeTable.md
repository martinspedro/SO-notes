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



