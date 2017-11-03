# soFreeFileCLusters
- Apagamento da esquerda para a direita
- As posições são alteradas e escritas com Null Reference
- o size só é alterado por syscalls e não ao libertar um inode/cluster

# 
- Um diretório tem um size múltiplo do cluster
- Os diretórios crescem cluster a cluster

# 
uint32_t soGetDirEntry ( int           pih,
                            const char *  name
                          )

   Get the inode associated to the given name.

   The directory contents, seen as an array of directory entries, is parsed to find an entry whose name is name.

   The name must also be a base name and not a path, that is, it can not contain the character '/'.

   Parameters

          pih  inode handler of the parent directory
          name the name entry to be searched for

   Returns
          the corresponding inode number

# 
 uint32_t soDeleteDirEntry ( int           pih,
                               const char *  name,
                               bool          clean = false
                             )

   Remove an entry from a parent directory.

   A direntry associated from the given directory is deleted. The refcount of the child inode is not decremented
   by this function.

   Parameters

          pih   inode handler of the parent inode
          name  name of the entry
          clean if true (different than zero) clean the corresponding dir entry, otherwise keep it dirty

   Returns
          the inode number in the deleted entry
 

