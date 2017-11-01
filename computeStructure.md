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


