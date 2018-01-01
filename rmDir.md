# rmDir
- Só posso apagar uma pasta se estiver vazia
	- Sò possui a entrada . e ..
- Apagar uma pasta implica libertar o cluster
- Libertar o inode
- Chamar o unlink para a ..
- remover as entradas que vão dar a essa pasta
	- How?
- As coisas não estão necessariamente por ordem
- Preciso de chamar a transverse path para chegar ao path pretendido
- é preciso alterar os times??
	- Sim!
	- Access
	- Modify no nó '.'
- A entrada '..' é alterada sempre que leio so stats

- Errors
	- **EACCES:** Write  access  to the directory containing pathname was not allowed, or one of the directories in the path prefix of pathname did not allow search permission.  (See also path_resolution(7).
	- ~~**EBUSY:** pathname is currently in use by the system or some process that prevents its removal.  On Linux  this means pathname is currently used as a mount point or is the root directory of the calling process.~~
	- ~~**EFAULT:** pathname points outside your accessible address space.~~
	- **EINVAL:** pathname has .  as last component.
	- ~~**ELOOP:**  Too many symbolic links were encountered in resolving pathname.~~
	- ~~**ENAMETOOLONG:** pathname was too long.~~
	- **ENOENT:** A directory component in pathname does not exist or is a dangling symbolic link.
	- ~~**ENOMEM:** Insufficient kernel memory was available.~~
    - **ENOTDIR:** pathname, or a component used as a directory in pathname, is not, in fact, a directory.
    - **ENOTEMPTY:** pathname  contains  entries  other  than  .  and  ..  ;  or, pathname has ..  as its final component. POSIX.1-2001 also allows EEXIST for this condition.
	- **EPERM:**  The directory containing pathname has the sticky bit (S_ISVTX) set and the process's  effective  user ID  is neither the user ID of the file to be deleted nor that of the directory containing it, and the process is not privileged (Linux: does not have the CAP_FOWNER capability).
	- **EPERM:** The filesystem containing pathname does not support the removal of directories.
    - **EROFS:** pathname refers to a directory on a read-only filesystem.
- verificar se é diretório!
ferifica r se +e dieotio e se tenho permissoes (icheckaccess)
 - thore excepção
check if bn exist -> enoent

