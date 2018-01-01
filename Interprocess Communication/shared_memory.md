---
title: Shared Memory

author: Pedro Martins
---

# Shared Memory in Unix/Linux
- É um recurso gerido pelo sistema operativo

Os espaços de endereçamento são **independentes** de processo para processo, mas o **espaço de endereçamento** é virtual, podendo a mesma **região de memória física**(memória real) estar mapeada em mais do que uma **memórias virtuais**

## POSIX Shared Memory
- Criação:
	- `shm_open`
	- `ftruncate`
- Mapeamento:
	- `mmap`
	- `munmap`
- Outras operações:
	- `close`
	- `shm_unlink`
	- `fchmod`
	- ...

## System V Shared Memory
- Criação:
	- `shmget`
- Mapeamento:
	- `shmat`
	- `shmdt`
- Outras operações:
	- `shmctl`

