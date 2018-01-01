# direntries

# soGetDirEntry
- Obtem o inode associado ao nome da função
- É preciso fazer o parse do nome do diretório para chegar ao diretório pretendido
- Chama a traverse Path

- Tem de verificar se a entrada já existe

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


# soRenameDirEntry
- Renomeia a entrada de um diretório

 void soRenameDirEntry ( int           pih,
                           const char *  name,
                           const char *  newName
                         )

   Rename an entry of a directory.

   A direntry associated from the given directory is renamed.

   Parameters

          pih     inode handler of the parent inode
          name    current name of the entry
          newName new name for the entry

# soTraversePath
- Obtem o inode associado com um dado caminho
- Atravessa a estrutura do sistema do sistema de ficheiros para obter o inode cujo nome do ficheiro é a componente mais à direita do caminho
- O caminho deve ser absoluto
- Todos elementos do caminho (com exceção do último) devem ser diretório ou symbolic links com permissão de travers (x)

 

   uint32_t soTraversePath ( char *  path )

   Get the inode associated to the given path.

   The directory hierarchy of the file system is traversed to find an entry whose name is the rightmost
   component of path. The path is supposed to be absolute and each component of path, with the exception of the
   rightmost one, should be a directory name or symbolic link name to a path.

   The process that calls the operation must have execution (x) permission on all the components of the path
   with exception of the rightmost one.

   Parameters

          path the path to be traversed

   Returns
          the corresponding inode number

# soAddDirEntry
- Adiciona uma nova entrada no diretório pai
- Uma direntry é adicionada ligando o parent inode ao child inode
	-  O lnkcnt do inode filho **não** é incrementado nesta função

void soAddDirEntry ( int           pih,
                        const char *  name,
                        uint32_t      cin
                      )

   Add a new entry to the parent directory.

   A direntry is added connecting the parent inode to the child inode. The refcount of the child inode is not
   incremented by this function.

   Parameters

          pih  inode handler of the parent inode
          name name of the entry
          cin  number of the child inode

# soDeleteDirEntry
- Remove uma entrada do parent directory
- O lnkcnt do inode filho **não** é decrementado

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




# Extra
filesystem check
- atribui ficheiros para a o disco
sempre que uma função falahe gera uma exceção

#soRenameDirEntry
- dá um novo nome ao diretório

#soDeleteDirEntry
- remove o diretório

#soGetDirEntry
- quando alguém a nível superior quer abrir/escrever, ver permissões precisa de sabe o inode
