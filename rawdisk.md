# rawdisk
- Permite o acesso aos blocos do disco
	- Os blocos são a menor unidade lógica no _filesystem_
- Medeia o acesso direto ao disco, impedindo que ocorram erros que podem danificar a estrutura do sistema de ficheiros

## Macros
```c
// block size (in bytes)
#define BlockSize (512U)
```

## Funções
```cpp
void soOpenRawDisk (const char *devname, uint32_t *np=NULL)
```

- Abre o dispositivo de armazenamento, criando um canal de comunicação com esse dispositivo
	- Supoem que o dispositivo está fechado e mais nenhum canal de comunicação para esse dispositivo está aberto
	- O dispositivo de armazenamento tem de existir
	- O dispositivo de armazenamento tem de ter um tamanho múltiplo do block size


###  
```cpp
void soCloseRawDisk (void) 
```

- Fecha o dispositivo de armazenamento e o canal de comunicação.


###
```c++
void soReadRawBlock(uint32_t n, void *buf)  
```

- Lê um bloco de dados do dispositivo
- **Parametros:**
	- _n_: número físico do bloco de dados no disco de onde a informação vai ser lida
	- _buf_: ponteiro para o buffer para onde os dados vão ser lidos


###
```cpp
void soWriteRawBlock ( uint32_t  n, void * buf)
```

- Escreve um bloco de dados do dispositivo
- **Parametros:**
	- _n_: número físico do bloco de dados no disco onde a informação vai ser escrita
	- _buf_: ponteiro para o buffer que contém os dados a ser escritos
