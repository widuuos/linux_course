硬盘的讲解
===

>主要是针对首页硬盘分区的详细讲解，更深层次一点

>我们要清楚的知道，我们硬盘其实是有一个一个扇区组成的，其中第一个扇区512byte存储着MBR（主引导）和分区表，其中分区表64byte

 - **分区表只能有4个主分区，扩展分区最多只能有一个。**譬如，我前些日子windows8自带的分区工具安装电脑的时候，我想分5个分区不行，只能分区4个主分区

 - 其中我们看到的/dev/hd*是 IDE的现在很少见了，看到多的还是/dev/sd* 也就是sata scsi等硬盘
 
>为什么我们要分区？
>如果一个分区的话,inode会太多，会造成磁盘的读写速度缓慢

>每一个分区里边都由super block,它包括inode和block，其中Inode里边记录着block的号码，block是存放数据用的，而inode是存放用户的属性，权限，时间参数等等,为什么了解这些，这些是基本概念，如果你看七牛云存储的文档的时候，你会看到一个就是断点上传，你如果不理解block和inode就不清楚，它是怎么保证断点上传的，七牛断点上传的block是4MB

>FAT16或者FAT32也就是一般我们的U盘系统都是没有inode的也就是说他们不是记录block存储的，而是根据block号码，1-2-3-4这样顺序存储的，所以删除的时候中间会出现断层，这个时候我们需要磁盘碎片整理

>ext2-ext4,NTFS等都是索引式文件系统，inode记录这block的号码，可能一个文件存储的block块是1,18,19,20,它会按照inode的顺序去读取把文件读取出来

>block一旦格式化就不能被指定了，其中block有1K 2K 和4K的存储格式，这里我们就要合理分配了

	#我们有300个10byte的文件，我们分block是4K，那么我们将浪费4096*1000-300*10的空间

>inode中包含了文件的访问模式，所有者和组，文件大小，时间的属性，文件的特殊标志，文件内容的指向。其中每个inode的大小是128byte，一个文件就会占用一个inode,所以说我们创建文件的数量跟inode有关系。

	>dumpe2fs -h /dev/   #能查看超级块的所有信息
	
	>ls -l /lib/modules/$(uname -r)/kernel/fs #查看可以分区的格式
	>cat /proc/filesystems                    #同上

    >df -hT                                   #-h 显示友好 -T 显示挂载磁盘类型

    >du -sh       #显示某个文件夹的容量
    
    >partprobe    #强制内核找分区表
    
    #格式化
    >mkfs.ext4 -L "卷标名称" -i "多少容量一个inode" -b "block大小" [-c 检查磁盘错误] [-j 一般我们mke2fs格式化ext2为ext3时候用]  设备

    #检查磁盘分区文件系统是否有错
    >fsck [-C 显示进度] [-f 强制] [-a 自动修复] [-t 文件类型] 设备

    #检查有没有坏道
    >badblocks [-v 显示进度] [-s 列出进度] [-w 写入方式] 设备

	#修改卷标名称
	>e2label 设备名称 新的设备名称

    #挂在iso文件
	>mkdir /mnt/iso
	>mount -o loop /root/xxx.iso /mnt/iso

	#创建大文件，也可以用作读写测试
	>dd if=/dev/zero of=/data/data bs=1M count=512
	#上边的语句是从/dev/zero读取，然后输出到/data/data，bs是block的大小，count是block的次数

	#下边是我的点滴SSD云主机和普通的云主机速度大家看下

![ssd](http://widuu.u.qiniudn.com/linux/dd.png)
---
![sata](http://widuu.u.qiniudn.com/linux/dd2.png)
