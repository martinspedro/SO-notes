# createDisk

- Cria um disco **não formatado** que serve de suporte a um sistema de ficheiros.
- Na prática, um disco é um ficheiro que possui uma estrutura de blocos fixa.

- Apenas é garantida que a estrutura do disco possui:
	- o número desejado de clusters
	- o número desejado de bytes por cluster

- Para o disco ser um sistema de ficheiros válido é necessário formatá-lo com ferramentas adequadas para o tipo de sistemas de ficheiros pretendido 

## Exemplo de utilização
```bash
./createDisk <diskfile> <numblocks>
```

O _output_ após a execução do script para um disco com 1000 blocos é:

```bash
./createDisk <diskfle> 1000
1000+0 records in                               
1000+0 records out
512000 bytes (512 kB) copied, 0.05734 s, 8.9 MB/s
``` 

## Implementação
O createDisk usa o comando [_dd_](https://en.wikipedia.org/wiki/Dd_(Unix) "Wikipedia page") para escrever para o disco/ficheiro e preenche-o com valores aleatórios obtidos do _/dev/urandom_.

```bash
#!/bin/bash                
           
if [ $# != 2 ]; then
        echo "$0 diskfile numblocks"
        exit 1                     
fi
 
dd if=/dev/urandom of=$1 bs=512 count=$2
```


