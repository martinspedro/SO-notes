Comentários
- soWriteRawBlock
	- char blk[blockSize]
	- SOSuperblock sb;
	- SOTnode it[inodesPerBlock]
	- soWriteRawBlock(uint32_t n, void *buf)
	- blk
	- fsb
	- it

## Comandos para executar
./showblock /tmp/zzz -i 1

## Notes
função alloc cluster tem de verificar se ficou tudo bem no disco
função replentish transfer da reference bitmao block para a retrieval cache


