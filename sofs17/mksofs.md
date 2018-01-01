# msksofs
- Script responsável por formatar o disco
- Cria um disco utilizável
	- Manipula os blocos do disco para implementar o _sofs17 filesystem_

## Utilização

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

- Posso usar mais do que uma das opções na mesma execução
## Exemplos

### No Options
- Basta indicar o número do ficheiro
- Por default, o número de inodes é o número de clusters/8

```bash
./mksofs ../disk.sofs17

Trying to install a 125-inodes SOFS17 file system in ../disk.sofs17.
Computing disk structure... 
Filling in the superblock fields... 
Filling in the table of inodes... 
Filling in the bitmap of free clusters... 
Filling in the root directory... 
144-inodes SOFS17 file system was successfully installed in ../disk.sofs17.
```

### Set name
```bash
$ ./mksofs.bin64 disk.sofs17 -n "my disk"

Trying to install a 125-inodes SOFS17 file system in disk.sofs17.
  Computing disk structure... done.
  Filling in the superblock fields... done.
  Filling in the table of inodes... done.
  Filling in the bitmap of free clusters... done.
  Filling in the root directory... done.
A 144-inodes SOFS17 file system was successfully installed in disk.sofs17.

$ ./showblock disk.sofs17 -s 0
Header:
   Magic number: 0x50F5
   Version number: 0x2017
   Volume name: my disk
   Properly unmounted: yes
   Number of mounts: 0
   Total number of blocks in the device: 1000
Inode table metadata:
   First block of the inode table: 1
   (...)
```

### Set inodes
- O formatador tenta formatar o disco para o número desejado de inodes

```bash
./mksofs.bin64 disk.sofs17 -i 2000

Trying to install a 2000-inodes SOFS17 file system in disk.sofs17.
  Computing disk structure... done.
  Filling in the superblock fields... done.
  Filling in the table of inodes... done.
  Filling in the bitmap of free clusters... done.
  Filling in the root directory... done.
A 2000-inodes SOFS17 file system was successfully installed in disk.sofs17.
```

- Pode não ser possível formatar o disco para o número desejado de inodes
- Nesse caso, o formatador usa o número de inodes possível imediatamente superior ao pretendido

```bash
./mksofs.bin64 disk.sofs17 -i 100

Trying to install a 100-inodes SOFS17 file system in disk.sofs17.
  Computing disk structure... done.
  Filling in the superblock fields... done.
  Filling in the table of inodes... done.
  Filling in the bitmap of free clusters... done.
  Filling in the root directory... done.
A 112-inodes SOFS17 file system was successfully installed in disk.sofs17.
```

### Zero Mode
- Ao usar a opção `-z` todos os clusters livres são preenchidos com zeros

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

