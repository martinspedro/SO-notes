## createDisk

Cria um disco que serve de suporte ao sistema de ficheiros. O disco **não está formatado** apenas contém: \
- o número desejado de clusters
- o número desejado de bytes por cluster

Ainda falta formatar os clusters de acordo com o sistema de ficheiros, para este disco ser um sistema de ficheiros válido

```bash
USAGE
./createDisk <diskfile> <numblocks>
```

O output parece-se com o seguinte:
```bash
./createDisk <diskfle> 1000
1000+0 records in                               
1000+0 records out
512000 bytes (512 kB) copied, 0.05734 s, 8.9 MB/s
```

O createDisk usa o `dd`para criar o disco e preenche-o com valores aleatórios.

```bash
#!/bin/bash                
           
if [ $# != 2 ]; then
        echo "$0 diskfile numblocks"
        exit 1                     
fi
 
dd if=/dev/urandom of=$1 bs=512 count=$2
```

## Criar um disco
```bash
./createDisk /tmp/so # a pasta tmp é dada reset a cada reboot
```

```bash
# permite me olhar para uma sequência ou range de um disco da forma que eu quiser
./showblock

# conteudo interno do bloco 0 em hexadecimal formatado considerando que é um valor hexadecimal
./showblock /tmp/so -x 0
```

|:----:|:-----:|
| -h   | help |
|  -x  | hex |
| -s   | superblock |
| -b   | bits


