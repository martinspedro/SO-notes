# itdealer
- Conjunto de funções que manipulam diretamente a estrutura de inodes

## iOpen
- Abre um inode
	- Transfere o seu conteudo para a memória
	- Set ao _usecount_
- Caso o inode já esteja aberto, incrementa a _usecount_
- Em qualquer dos casos devolve o handler (referência para a posição de memória) para o inode
