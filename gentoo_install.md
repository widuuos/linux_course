gentoo安装教程
===

>网上教程这么多或者说，网上那么多资源你还写什么？主要是技术更新换代太快，有好多东西一直在变，所以我写出来最新的，肯定能让你安装成功的~

####下载镜像

>一般我都是用国内的镜像源，不管是centos,ubuntu还是gentoo在国内的镜像来说肯定比国外快

	#下载地址
	mirrors.163.com/gentoo/
	#我用的x86的
	http://mirrors.163.com/gentoo/releases/x86/current-iso/

	#需要下载，举个国内最新版本，这个地址http://mirrors.163.com/gentoo/releases/x86/current-iso/
	
	install-x86-minimal-20140318.iso   
	stage3-i486-20140318.tar.bz2    

	#http://mirrors.163.com/gentoo/snapshots/下载

	portage-latest.tar.bz2    

>我一般的安装习惯是这样的，把stage3-i486-20140318.tar.bz2和portage-latest.tar.bz2包添加到镜像install-x86-minimal-20140318.iso中省了后边安装在下载麻烦，譬如我用UltraISO把那两个包压缩到镜像中，如下图结构

![gentoo pkg](http://widuu.u.qiniudn.com/linux/gentoo.png)

然后你可以写入U盘，开机启动加载livecd就OK了，然后我们开始安装

####1.启动

>启动界面出现boot:可以直接按下回车，或者输入对应你开启的硬件选项，见最地下附录

![gentoo install](http://widuu.u.qiniudn.com/linux/gentoo1.png)

####配置网络

	ifconfig	#查看网卡，因为并不是所有的网卡都叫eth0,譬如我这个，无线网卡用iwconfig

![gentoo install](http://widuu.u.qiniudn.com/linux/gentoo2.png)

	>ifconfig enp0s3 192.168.1.127 netmask 255.255.255.0  #enp0s3是你的网卡，后边是ip
	>route add default gw  192.168.1.1                    #网关
	>echo "nameserver 192.168.1.1">>/etc/resolv.conf      #配置DNS
	>ping www.baidu.com                                   #测试网络通不通

####启动ssh

	/etc/init.d/sshd start  #启动ssh或者用service sshd start
	passwd root             #输入你的帐号和密码，这个时候你就可以用ssh远程登录了，当然你一台电脑就完全不需要

####分区

	fdisk -l 				#查看可以分区的硬盘和容量，我测试的用8G,sata的

![gentoo install](http://widuu.u.qiniudn.com/linux/gentoo3.png)

	>fdisk /dev/sda

	Command (m for help): n							#输入n，仙剑分区
	Partition type:
	   p   primary (0 primary, 0 extended, 4 free)
	   e   extended
	Select (default p): p							#选择主分区
	Partition number (1-4, default 1):				#回车
	Using default value 1
	First sector (2048-16777215, default 2048):		#回车
	Using default value 2048
	Last sector, +sectors or +size{K,M,G} (2048-16777215, default 16777215): +200M #200M boot
	Partition 1 of type Linux and of size 200 MiB is set

![gentoo install](http://widuu.u.qiniudn.com/linux/gentoo4.png)

	最后及的输入w保存
	
	Command (m for help): w
	The partition table has been altered!
	
	Calling ioctl() to re-read partition table.
	Syncing disks.

>依照上边的方法可以自己分区，我分区是200M boot 7G / 512M swap


![gentoo install](http://widuu.u.qiniudn.com/linux/gentoo5.png)

####格式化分区

	mkfs.ext4 /dev/sda1
	mkfs.ext4 /dev/sda2
	mkswap    /dev/sda3			#格式化swap
	swapon    /dev/sda3			#挂载swap

####挂载分区

	mount /dev/sda2 /mnt/gentoo       #你分的/
	mkdir /mnt/gentoo/boot
	mount /dev/sda1 /mnt/gentoo/boot  #你分的准备挂载boot的分区
	

####安装Stage Tarball

	#配置时间
	date	032714382014    #月日时分年的顺序

>由于之前我们把stage3-i686-20140304.tar.bz2和portage-20140310.tar.bz2压缩到镜像里边了，所以我们就不需要下载了

	cd /mnt/cdrom
	cp *.tar.bz2 /mnt/gentoo     #复制两个文件到/mnt/gentoo下

	cd /mnt/gentoo				 #gentoo目录下
	tar xvjpf stage3-*.tar.bz2   #解压stage3

	tar xvjf /mnt/gentoo/portage-*.tar.bz2 -C /mnt/gentoo/usr	#解压portage到/mnt/gentoo/usr

>如果按照官方文档来说make.conf在/mnt/gentoo/etc/make.conf下，但是我的确是在/mnt/gentoo/etc/portage/make.conf，大家注意一下自己的位置

	#单核配置如下，限制并行编译的数目
	MAKEOPTS="-j2"

####Chroot

 	mirrorselect -i -o >> /mnt/gentoo/etc/portage/make.conf  #后边这个是自己的make.conf的位置，选择镜像源我选择是163,然后回车

	cp -L /etc/resolv.conf /mnt/gentoo/etc/    				 #拷贝dns信息

####挂载/proc和/dev文件系统

	mount -t proc none /mnt/gentoo/proc

####进入新的系统环境
	
	chroot /mnt/gentoo  /bin/bash
	env-update
	source /etc/profile
 
![gentoo6](http://widuu.u.qiniudn.com/linux/gentoo6.png)

####更新Portage树
	
	emerge --sync --quiet
	
	#验证系统profile
	eselect profile list

####配置时区

	ls /usr/share/zoneinfo									#查看时区
	cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime  	#我使用的是上海

####安装内核 

	 emerge gentoo-sources									#安装内核源码
	 ls -l /usr/src/linux									#查看内核连接

![gentoo8](http://widuu.u.qiniudn.com/linux/gentoo8.png)

####手动配置

	cd /usr/src/linux
	make menuconfig
	
>注意：我是32位系统。所以64-bit kernel 前边的*一定要去掉
![gentoo9](http://widuu.u.qiniudn.com/linux/gentoo9.png)
>按需配置，按↓到File System回车进入，因为我用的ext4,所以把EXt4 debugging support选上

>然后Tab选择到save 回车，然后选OK,Exit回车，然后在选Exit推出

####编译内核

	make && make modules_install		#回车之后等着编译完成吧，编译完成后会有bzImage的位置信息

![gentoo10](http://widuu.u.qiniudn.com/linux/gentoo10.png)

####安装内核
		
	  cd /usr/src/linux
	  cp arch/x86/boot/bzImage /boot/kernel-3.12.13-gentoo

####配置系统

>**配置文件系统**

	nano -w /etc/fstab			#根据自己的分区配置
	
	/dev/sda1       /boot           ext4            defaults,noatime        1 2
	/dev/sda2       /               ext4            noatime                 0 1
	/dev/sda3       none            swap            sw                      0 0
	/dev/cdrom      /mnt/cdrom      auto            noauto,ro               0 0

>**修改主机名称**

	nano -w /etc/conf.d/hostname
	
![gentoo11](http://widuu.u.qiniudn.com/linux/gentoo11.png)

>**配置网络**

	nano -w /etc/conf.d/net   #根据自己的网卡配置，我的ifconfig的网卡是enp0s3

	config_enp0s3=( "192.168.1.126 netmask 255.255.255.0 brd 192.168.1.255" )
	routes_enp0s3=( "default via 192.168.1.1" )

	ln -s /etc/init.d/net.lo /etc/init.d/net.enp0s3

	rc-update add net.enp0s3 default				#设置开机启动

>**配置root密码**

	passwd root	#输入你的帐号密码

####安装系统工具

	 emerge syslog-ng						#系统日志工具
	 rc-update add syslog-ng default
	 emerge vixie-cron						#Cron守护进程
	 rc-update add vixie-cron default
	 emerge sys-apps/mlocate					#安装文件索引locate,重启后记得updatedb新建库


#### 配置引导程序
	
>安装grub

	emerge grub			#安装grub

>配置grub

>我emerge grub安装完成后，安装的是grub2，如果你安装之后是grub你可以校对[http://www.widuu.com/gentoo.html](http://www.widuu.com/gentoo.html "《gentoo官方安装文档》")来配置，如果是grub2,配置如下

	/usr/sbin/grub2-install --no-floppy /dev/sda				#安装
	/usr/sbin/grub2-mkconfig -o /boot/grub/grub.cfg			#生成配置

![gentoo12](http://widuu.u.qiniudn.com/linux/gentoo12.png)

####重启安装完成

	exit	#推出chroot
	reboot	#重启

![gentoo13](http://widuu.u.qiniudn.com/linux/gentoo13.png)

	rc-update add sshd default	#设置ssh开机启动
	service sshd start			#开启ssh

	

###附录启动项

	acpi=on
	加载对ACPI的支持，在启动光盘的同时启动acpid后台程序。这个选项只有 在你的系统需要ACPI才能正常运行的情况下才需要。启用超线程的支持 这个选项不是必需的。
	acpi=off
	完全关闭ACPI。这在一些比较老的系统上有用，同时也是使用高级电源管 理（APM）的必要选项。这也将关闭对你的处理器的超线程的支持。
	console=X
	这个选项可以设置光盘的串口访问。第一个选项是设备，在x86上通常为 ttyS0，后面可以跟其他选项并用逗号分隔。默认选项为9600,8,n,1。
	dmraid=X
	这个选项用于向设备映射器RAID子系统传递参数。传递的参数必须包含 在引号内。
	doapm
	这个选项加载高级电源管理（APM）驱动支持。这需要你同时使用acpi=off 选项。
	dopcmcia
	这个选项加载了对PCMCIA和Cardbus硬件的支持，也使得pcmcia卡的 cardmgr能够在光盘启动的时候运行起来。这个选项只有在从 PCMCIA/Cardbus设备启动的时候才需要。
	doscsi
	这个选项加载对大部分SCSI控制器的支持。在大多数USB设备启动的时候 也需要这个选项，因为USB设备使用内核中的SCSI子系统。
	sda=stroke
	这个选项允许你对整个硬盘进行分区，即使你的BIOS不能处理大硬盘。 此选项仅使用在使用旧BIOS的机器上。使用时把sda替换为需要这个选项 的设备。
	ide=nodma
	此选项强制关闭内核中的DMA，一些IDE和CDROM驱动需要这个选项。如果 你的系统在读取IDE接口的光驱的时候遇到麻烦，可以尝试一下此选项。 此选项同时也将关闭hdparm的默认设置。
	noapic
	此选项关闭了在一些新主板上存在的高级可编程中断控制器。它在一些旧 的硬件上会引发一些问题。
	nodetect
	此选项将会关闭光盘会做的所有自动检测，包括设备检测和DHCP探测。这 在调试有问题的光盘或驱动的时候有用。
	nodhcp
	此选项将禁止在检测到的网卡上进行DHCP探测。这对仅有静态地址的网络 很有用。
	nodmraid
	关闭了设备映射RAID的支持，例如用于板载IDE/SATA RAID的控制器。
	nofirewire
	此选项关闭了Firewire模块加载。这个选项只有在你的Firewire硬件导致 光盘启动出现问题的时候才需要。
	nogpm
	此选项关闭了gpm控制台鼠标支持。
	nohotplug
	此选项关闭启动时对热插拔（hotplug）和冷插拔（coldplug）启动脚本的加 载。这个选项在调试失败的光盘和驱动的时候有用。
	nokeymap
	此选项禁止了键盘布局选择。
	nolapic
	此选项关闭了在单处理器内核上的本地APIC。
	nosata
	此选项关闭了对串行ATA（SATA）模块的加载。当你的系统的SATA子系统有 问题的时候，此选项有用。
	nosmp
	此选项关闭了在支持SMP（对称式多处理）的内核中的SMP功能。这个选项用 于调试在特定的驱动和主板上与SMP相关的问题。
	nosound
	此选项关闭了声音支持和音量的设置。这个选项在声音支持出现问题的系 统上有用。
	nousb
	此选项关闭了USB模块的自动加载。这个选项在调试USB问题时有用。
	slowusb
	这个选项在启动过程中增加一些额外的暂停，主要是为了一些慢速USB CDROM，例如IBM BladeCenter的。