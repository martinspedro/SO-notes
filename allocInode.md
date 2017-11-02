uint32_t soAllocInode ( uint32_t  type )

   Allocate a free inode.

   An inode is retrieved from the list of free inodes, marked in use, associated to the legal file type passed as
   a parameter and is generally initialized.

   Parameters

          type the inode type (it must represent either a file, or a directory, or a symbolic link)

   Returns
          the number of the allocated inode


- Se for possível, aloca o inode que está na HEAD
- Incrementa a HEAD
	- tem de verificar se a inode table está no fim e se tem de voltar aos inodes que entretanto foram libertados
- Decrementa o número de inodes livres 
- O inode tem de ser corretamente inicializado
