# Fileclusters
Functions to manage the clusters belonging by a file

## Doxygen
uint32_t  soGetFileCluster (int ih, uint32_t fcn)
            Get the cluster number of a given file cluster. More...

  uint32_t  soAllocFileCluster (int ih, uint32_t fcn)
            Associate a cluster to a given file cluster position.
            More...

      void  soFreeFileClusters (int ih, uint32_t ffcn)
            Free all file clusters from the given position on.
            More...

      void  soReadFileCluster (int ih, uint32_t fcn, void *buf)
            Read a file cluster. More...

      void  soWriteFileCluster (int ih, uint32_t fcn, void *buf)
            Write a data cluster. More...

Detailed Description

   Functions to manage the clusters belonging by a file.

   Author
          Artur Pereira - 2008-2009, 2016-2017
          Miguel Oliveira e Silva - 2009, 2017
          António Rui Borges - 2010-2015

   Remarks
          In case an error occurs, every function throws an
          SOException

Function Documentation

   uint32_t soAllocFileCluster ( int       ih,
                                 uint32_t  fcn
                               )

   Associate a cluster to a given file cluster position.

   Parameters

          ih  inode handler
          fcn file cluster number

   Returns
          the number of the allocated cluster

   void soFreeFileClusters ( int       ih,
                             uint32_t  ffcn
                           )

   Free all file clusters from the given position on.

   Parameters

          ih   inode handler
          ffcn first file cluster number

   uint32_t soGetFileCluster ( int       ih,
                               uint32_t  fcn
                             )

   Get the cluster number of a given file cluster.

   Parameters

          ih  inode handler
          fcn file cluster number

	[AQUI]

   Returns
          the number of the corresponding cluster

   void soReadFileCluster ( int       ih,
                            uint32_t  fcn,
                            void *    buf
                          )

   Read a file cluster.

   Data is read from a specific data cluster which is
   supposed to belong to an inode associated to a file (a
   regular file, a directory or a symbolic link).

   If the referred file cluster has not been allocated yet,
   the returned data will consist of a byte stream filled
   with the character null (ascii code 0).

   Parameters

          ih  inode handler
          fcn file cluster number
          buf pointer to the buffer where data must be read into

   void soWriteFileCluster ( int       ih,
                             uint32_t  fcn,
                             void *    buf
                           )

   Write a data cluster.

   Data is written into a specific data cluster which is
   supposed to belong to an inode associated to a file (a
   regular file, a directory or a symbolic link).

   If the referred cluster has not been allocated yet, it
   will be allocated now so that the data can be stored as
   its contents.

   Parameters

          ih  inode handler
          fcn file cluster number
          buf pointer to the buffer containing data to be written


