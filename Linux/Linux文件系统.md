**superblock**：记录此 filesystem 的整体信息，包括inode/block的总量、使用量、剩余量， 以及文件系统的格式与相关信息等；

> Superblock 是记录整个 filesystem 相关信息的地方， 没有 Superblock ，就没有这个 filesystem 了。他记录的信息主要有：
>
> - block 与 inode 的总量；
> - 未使用与已使用的 inode / block 数量；
> - block 与 inode 的大小 （block 为 1, 2, 4K，inode 为 128Bytes 或 256Bytes）；
> - filesystem 的挂载时间、最近一次写入数据的时间、最近一次检验磁盘 （fsck） 的时间等文件系统的相关信息；
> - 一个 valid bit 数值，若此文件系统已被挂载，则 valid bit 为 0 ，若未被挂载，则 valid bit 为 1 。



在Linux文件系统中，文件的属性（权限，创建时间，大小.....）和内容分开存放

- **inode**：记录文件的属性，一个文件占用一个inode，同时记录此文件的数据所在的 block 号码；
- **block**：实际记录文件的内容，若文件太大时，会占用多个 block 。



文件夹也是一个文件

- inode 
- block 中存放了文件夹里面的文件名和对应得inode





参考资料：

<https://wizardforcel.gitbooks.io/vbird-linux-basic-4e/content/59.html>