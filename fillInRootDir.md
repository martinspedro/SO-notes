# fillInRootDir
- A root dir ocupa um cluster
- Os dois primeiros slots estão reservados para as entradas "." e ".."
- Todos os os outros slots devem estar limpos:
	- o campo _name_ preenchido com zeros
	- o campo _inode_ preenchido com NullReference

## Algoritmia
- Colocar todos os blocos com zeros
- Na entrada 0
	- nome: "."
	- inode: 0 (raiz)
- Na entrada 1
	- nome: ".."
	- inode: 0 (raiz)

## Utilização
```c
void fillInRootDir(uint32_t rtstart)
```

### Parameters
- **rtstart:** number of the block where the root cluster starts.


