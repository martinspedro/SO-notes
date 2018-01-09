Comentários
- soWriteRawBlock
	- char blk[blockSize]
	- SOSuperblock sb;
	- SOTnode it[inodesPerBlock]
	- soWriteRawBlock(uint32_t n, void *buf)
	- blk
	- fsb
	- it


## Notes
função alloc cluster tem de verificar se ficou tudo bem no disco
função replentish transfer da reference bitmao block para a retrieval cache

Capacidade: $(6+ 2^9 + (2^9)^2) \cdot 2^11$

# 3 Nov 2017
- As direntries não mexem no lnkcnt
- Add mexe no size

# Unlink
1. dde
2. dec 
3. if(dec ==0)
	3.1 ffc
	3.2 fi

O dec devolve o devolve 

# Remove

# mtime vs ctime
- **mtime:** conteudo do ficheiro
- **ctime:** metadados do ficheiro

- Não podemos ter nenhum diretório apontado por dois diretórios
```bash
ln: 'ddd/': hard link not allowwd for directory
```
