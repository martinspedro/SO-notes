## sbdealer
- Serve de mediador do acesso ao disco
- A sua principal função é garantir que só existe uma cópia do superbloco em memória
	- Garantir consistência dos dados
	- Não existir informação duplicada, nem redudante
	- Garantir que não existe perda de informação
- Em caso de erros lança uma SOException
   Functions

             void  soOpenSBDealer ()
                   Open the superblock dealer. More...

             void  soCloseSBDealer ()
                   Close the superblock dealer. More...

   SOSuperBlock *  sbGetPointer ()
                   Get a pointer to the superblock. More...

             void  sbSave ()
                   Save superblock to disk. More...

Detailed Description

 SOSuperBlock* sbGetPointer ( )

   Get a pointer to the superblock.

   Load the superblock, if not done yet

   Returns
          Pointer to superblock

   void sbSave ( )

   Save superblock to disk.

   Do nothing if not loaded

   void soCloseSBDealer ( )

   Close the superblock dealer.

   Save superblock to disk and close dealer

   void soOpenSBDealer ( )

   Open the superblock dealer.

   Prepare the internal data structure of the superblock
   dealer


