
###下载内核

>从官方网站https://www.kernel.org/下载内核，我下载的是linux-3.13.7.tar.xz

	cat /proc/version	#先查看一下我的内核版本

![kernel2](http://widuu.u.qiniudn.com/linux/kernel2.png)

	cd /usr/src

	wget https://www.kernel.org/pub/linux/kernel/v3.x/linux-3.13.7.tar.xz

	xz -d linux-*.tar.xz

	tar xf linux-*.tar

	cd linux-3.13.7

	#清楚源码树和.config
	make mrproper

>这个时候你可能报错，会提示如下

	*** Unable to find the ncurses libraries or the

	#解决办法安装

	#centos

	yum install ncurses-devel -y

	#ubuntu

	apt-get install ncurses-dev

###定制内核

	make menuconfig		#最少也要把网卡、文件系统和声卡驱动选上,配置说明见底部附录

	#2.6以前的版本是，但是现在的内核是不需要make dep了
	make dep 
	make bzlmage  
	make modules 
	make modules_install 
	make install

	#编译内核
	make 
	
	make bzImage	#编译内核镜像，会提示如下
	#Kernel: arch/x86/boot/bzImage is ready 

	make modules	#编译内核模块

	make modules_install #安装内核模块

	#我的3.13.7不需要太复杂的配置

	make install   #就直接安装好了，包括内核映射,grub配置都不需要手工配置了,它都脚本自动给你配置好了

![kernel](http://widuu.u.qiniudn.com/linux/kernel.png)

>我们看下，我执行了make install 这个时候install.sh这个脚本都干了什么？

![kernel3](http://widuu.u.qiniudn.com/linux/kernel3.png)

>现在我们看出来了，以前那些繁琐的步骤都给我们省略了，全部脚本都干了，其实脚本替我们做的如下

	mkinitrd initrd-3.13.7.img 3.13.7		#(ubuntu,则使用mkinitramfs命令)
	cp /usr/src/linux-3.13.7/initrd-3.13.7.img  /boot/initrd-3.13.7.img
	cp /usr/src/linux-3.13.7/arch/x86/boot/bzImage  /boot/vmlinuz
	cp System.map	/boot/System.map

	#添加grub或者lilo
	title CentOS (3.13.7)
        root (hd0,0)
        kernel /vmlinuz-3.13.7 ro root=/dev/mapper/vg_livecd-lv_root rhgb quiet
        initrd /initrd-3.13.7.img

	#make install 就替我们做了这些，然后我们就可以重启看效果了

![kernel4](http://widuu.u.qiniudn.com/linux/kernel4.png)




![kernel5](http://widuu.u.qiniudn.com/linux/kernel5.png)

>安装完成
###附录

	1. Code maturity level options 代码成熟等级。
		此处只有一项：prompt for development and/or incomplete code/drivers，
	如果你要试验现在仍处于实验阶段的功能，比如khttpd、IPv6等，就必须把该项选择为Y了；否则可以把它选择为N。 

	2. Loadable module support 
	　　对模块的支持。这里面有三项： 
	
	　　Enable loadable module support：除非你准备把所有需要的内容都编译到内核里面，否则该项应该是必选的。 
	　　Set version information on all module symbols：可以不选它。 
	　　Kernel module loader：让内核在启动时有自己装入必需模块的能力，建议选上。 
	
	3. Processor type and features 
	　　CPU类型。内容蛮多的，不一一介绍了，有关的几个如下： 
	
	　　Processor family：根据你自己的情况选择CPU类型。 
	　　High Memory Support：大容量内存的支持。可以支持到4G、64G，一般可以不选。 
	　　Math emulation：协处理器仿真。协处理器是在386时代的宠儿，现在早已不用了。 
	　　MTTR support：MTTR支持。可不选。 
	　　Symmetric multi-processing support：对称多处理支持。除非你富到有多个CPU，否则就不用选了。 
	
	4. General setup 
	　　这里是对最普通的一些属性进行设置。这部分内容非常多，一般使用缺省设置就可以了。下面介绍一下经常使用的一些选项： 
	
	　　Networking support：网络支持。必须，没有网卡也建议你选上。 
	　　PCI support：PCI支持。如果使用了PCI的卡，当然必选。 
	　　PCI access mode：PCI存取模式。可供选择的有BIOS、Direct和Any，选Any吧。 
	　　Support for hot-pluggabel devices：热插拔设备支持。支持的不是太好，可不选。 
	　　PCMCIA/CardBus support：PCMCIA/CardBus支持。有PCMCIA就必选了。 
	　　System V IPC 
	　　BSD Process Accounting 
	　　Sysctl support：以上三项是有关进程处理/IPC调用的，主要就是System V和BSD两种风格。如果你不是使用BSD，就按照缺省吧。 
	　　Power Management support：电源管理支持。 
	　　Advanced Power Management BIOS support：高级电源管理BIOD支持。 
	
	5. Memory Technology Device（MTD） 
	　　MTD设备支持。可不选。 
	
	6. Parallel port support 
	　　串口支持。如果不打算使用串口，就别选了。 
	
	7. Plug and Play configuration 
	　　即插即用支持。虽然Linux对即插即用目前支持的不如Windows好，但是还是选上吧，这样你可以拔下鼠标之类的体验一下Linux下即插即用的感觉。 
	8. Block devices 
	　　块设备支持。这个就得针对自己的情况来选了，简单说明一下吧： 
	
	　　Normal PC floppy disk support：普通PC软盘支持。这个应该必选。 
	　　XT hard disk support： 
	　　Compaq SMART2 support： 
	　　Mulex DAC960/DAC1100 PCI RAID Controller support：RAID镜像用的。 
	　　Loopback device support： 
	　　Network block device support：网络块设备支持。如果想访问网上邻居的东西，就选上。 
	　　Logical volume manager（LVM）support：逻辑卷管理支持。 
	　　Multiple devices driver support：多设备驱动支持。 
	　　RAM disk support：RAM盘支持。 
	
	9. Networking options 
	　　网络选项。这里配置的是网络协议。内容太多了，不一一介绍了，自己看吧，如果你对网络协议有所了解的话，应该可以看懂的。如果懒得看，使用缺省选项（肯定要选中TCP/IP networking哦）就可以了。让我们看看，TCP/IP、ATM、IPX、DECnet、Appletalk……支持的协议好多哦，IPv6也支持了，Qos and/or fair queueing（服务质量公平调度）也支持了，还有kHTTPd，不过这些都还在实验阶段。 
	
	10. Telephony Support 
	　　电话支持。这个是什么东东？让我查查帮助，原来是Linux下可以支持电话卡，这样你就可以在IP上使用普通的电话提供语音服务了。记住，电话卡可和modem没有任何关系哦。 
	
	11. ATA/IDE/MFM/RLL support 
	　　这个是有关各种接口的硬盘/光驱/磁带/软盘支持的，内容太多了，使用缺省的选项吧，如果你使用了比较特殊的设备，比如PCMCIA等，就到里面自己找相应的选项吧。 
	
	12. SCSI support 
	　　SCSI设备的支持。我没有SCSI的设备，所以根本就不用选，如果你用了SCSI的硬盘/光驱/磁带等设备，自己找好了。 
	
	13. IEEE 1394（FireWire）support 
	　　这个是什么？低版本的没有见过，看看帮助再说。原来是要Fireware硬件来提高串行总线的性能，我没有，不选了。 
	
	14. I2O device support 
	　　这个也不清楚，帮助里说是这个需要I2O接口适配器才能支持的，在智能Input/Output（I2O）体系接口中使用，又是要硬件，不选了。 
	
	15. Network device support 
	　　网络设备支持。上面选好协议了，现在该选设备了，可想而知，内容肯定多得很。还好还好，里面大概分类了，有ARCnet设备、Ethernet（10 or 100 Mbit）、Ethernet（1000Mbit）、Wireless LAN（non-hamradio）、Token Ring device、Wan interfaces、PCMCIA network device support几大类。我用的是10/100M的以太网，看来只需要选则这个了。还是10/100M的以太网设备熟悉，内容虽然多，一眼就可以看到我所用的RealTeck RTL-8139 PCI Fast Ethernet Adapter support，为了免得麻烦，编译到内核里面好了，不选M了，选Y。耐心点，一般说来你都能找到自己用的网卡。如果没有，你只好自己到厂商那里去要驱动了。 
	
	16. Amateur Radio support 
	　　又一个不懂的，应该是配置业余无线广播的吧，没有，不要了。 
	
	17. IrDA（infrared）support 
	　　这个要红外支持，免了。 
	
	18. ISDN subsystem 
	　　如果你使用ISDN上网，这个就必不可少了。自己看着办好了。 
	
	19. Old CD-ROM drivers（not SCSI、not IDE） 
	　　做的可真周到，原来那些非SCSI/IDE口的光驱谁还在用啊，自己选吧，反正我是用的IDE的CD-ROM，不选这个。 
	
	20. Character devices 
	　　字符设备。这个内容又太多了，先使用缺省设置，需要的话自己就修改。把大类介绍一下吧： 
	
	　　I2C support：I2C是Philips极力推动的微控制应用中使用的低速串行总线协议。如果你要选择下面的Video For Linux，该项必选。 
	　　Mice：鼠标。现在可以支持总线、串口、PS/2、C&T 82C710 mouse port、PC110 digitizer pad，自己根据需要选择。 
	　　Joysticks：手柄。即使在Linux下把手柄驱动起来意义也不是太大，游戏太少了。 
	　　Watchdog Cards：虽然称为Cards，这个可以用纯软件来实现，当然也有硬件的。如果你把这个选中，那么就会在你的/dev下创建一个名为watchdog的文件，它可以记录你的系统的运行情况，一直到系统重新启动的1分钟左右。有了这个文件，你就可以恢复系统到重启前的状态了。 
	　　Video For Linux：支持有关的音频/视频卡。 
	　　Ftape, the floppy tape device driver： 
	　　PCMCIA character device support： 
	
	21. File systems 
	　　文件系统。内容又太多了，老法子，在缺省选项的基础上进行修改。介绍以下几项： 
	
	　　Quota support：Quota可以限制每个用户可以使用的硬盘空间的上限，在多用户共同使用一台主机的情况中十分有效。 
	　　DOS FAT fs support：DOS FAT文件格式的支持，可以支持FAT16、FAT32。 
	　　ISO 9660 CD-ROM file system support：光盘使用的就是ISO 9660的文件格式。 
	　　NTFS file system support：ntfs是NT使用的文件格式。 
	　　/proc file system support：/proc文件系统是Linux提供给用户和系统进行交互的通道，建议选上，否则有些功能没法正确执行。 
	
	　　还有另外三个大类都规到这儿了：Network File Systems（网络文件系统）、Partition Types（分区类型）、Native Language Support（本地语言支持）。值得一提的是Network File Systems里面的两种：NFS和SMB分别是Linux和Windows相互以网络邻居的形式访问对方所使用的文件系统，根据需要加以选择。 
	22. Console drivers 
	　　控制台驱动。一般使用VGA text console就可以了，标准的80*25的文本控制台。 
	
	23. Sound 
	　　声卡驱动。如果你能在列表中找到声卡驱动那自然最好，否则就试试OSS了。 
	
	24. USB supprot 
	　　USB支持。很多USB设备，比如鼠标、调制解调器、打印机、扫描仪等，在Linux都可以得到支持，根据需要自行选择。 
	
	25. Kernel hacking 
	　　配置了这个，即使在系统崩溃时，你也可以进行一定的工作了。普通用户是用不着这个功能的。 
	
	　　总算配置完了，现在存盘退出，当然你也可以把现在的配置文件保存起来，这样下次再配置的时候就省力气了。