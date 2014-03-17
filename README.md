###运行级别

>为什么说这个呢？因为懂了这个你对一些基础的，你对以后的开机，重启，程序的加载都会比较深的了解！Linux的用户级别有这几种

	>vim /etc/inittab	#init启动时候加载的启动级别

	0 关机
	1 单用户
	2 不支持nfs(网络文件系统)
	3 终端页面
	4 未被使用
	5 图形界面
	6 重启

>当你是终端的时候你会发现你登录的是3级别，如果是X11图形界面的时候是5,可以修改`id:3:initdefault`,来修改启动级别

>其中譬如我们开机启动mysql,我们设置如下

	>chkconfig mysql on				#设置开机启动
	>chkconfig --list|grep mysql 	#查看哪些启动级别,发现除了0，1，6关闭其他都开启
	>cd /etc/rc.d && ls -l			#rcx.d是启动级别
	>cd rc3.d && ll|grep mysql		#显示如下，当然你进入2，4，5也是一样的，这就是说为什么服务器要保证开机3，5启动
	lrwxrwxrwx  1 root root 16 3月  14 19:22 S64mysqld -> ../init.d/mysqld
 					

###压缩与解压

####gz压缩包

	>tar czf file.tar.gz file
	#联想记忆
 	#c create
	#z gz
	#f file 

	>tar zxf file.tar.gz -C /data
	#x extract 记住x
	#-C 这是解压到哪里去

	#查看压缩包
	>tar tf file.tar.gz
	
####zip压缩包

	>yum install unzip zip -y #安装包
	>unzip file.zip           #解压
	>unzip -l file.zip        #查看压缩包中的内容
	>zip  file.zip file       #压缩

####tar.bz2

	>tar jxf file.tar.bz2      #解压
	>>tar tf file.tar.bz2      #查看压缩包内容
	>tar jcf file.tar.bz2 file #压缩

###网络配置

####1. 修改ip 只能暂时的，重启就失效了
	
	ifconfig eth0 192.168.1.1
	ifconfig eth0 192.168.0.2 netmask 255.255.255.0

####2. 修改网络配置文件

	#centos位置
	>cd /etc/sysconfig/network-scripts/
	#ubuntu
	>vim /etc/network/interfaces

	#centos配置

	>vim /etc/sysconfig/netword-scripts/ifcfg-eth1 #针对自己的网卡
	
	DEVICE=eth1  								#网卡名称
	TYPE=Ethernet								#网卡类型
	IPADDR=192.168.1.1							#IP地址
	NETMASK=255.255.255.0						#子网掩码
	GATEWAY=192.168.1.1							#网卡
	DNS1=202.102.224.68							#DNS
	DNS2=8.8.8.8
	ONBOOT=yes									#主机启动加载	
	NM_CONTROLLED=yes							#实施生效，修改后不需要重启网卡立即生效
	BOOTPROTO=static							#静态ip

	>service network restart					#重启网卡

	>vim  vim /etc/resolv.conf					#修改DNS
		
	nameserver 202.102.224.68
	nameserver 8.8.8.8

###防火墙设置

####iptables

	#开启防火墙
	>service iptables start
	
	#开机启动
	>chkconfig iptables on           
	
	#保存策略文件在 /etc/sysconfig/iptables -- centos
	iptabels [-t 表名] 
	>iptables -t nat -L 			#查看nat表
	>iptables -L	 				#查看防火墙，默认是filter
	
	>iptables -F					#删除防火墙 -X清除自定义链接 -Z清除所有链统计
	
	>service iptables save		#保存防火墙策略
	>service iptables restart	#重启
	>iptables -I INPUT -s 0.0.0.0 -j ACCEPT

	iptables 
		-A 追加规则链			再最后加入此规则
		-I INPUT 			插入INPUT
		-D 链 				删除链
		-P 如果策略值缺省就强制使用此策略链
		-i 输入网卡
		-o 输出网卡
		-p 协议
		-s 源 ip
		-d 目标ip
		-j 动作	
	
	iptables -A INPUT -i eth0 -p all -s 源ip --sport 源端口 -d 目标ip --dport 目标端口 -j 动作
	
	#常用模块状态
	>iptables -I INPUT 1 -m state --state ESTABLISHED,RELATED -j ACCEPT
	
	#允许192.168.1.109登录ssh端口	
	>iptables -A INPUT -i eth0 -p tcp -s 192.168.1.109 --dport 22 -j ACCEPT
	
	#禁止这个IP访问我的80端口
	>iptables -A INPUT -i eth0 -p all -s 192.168.1.108 --dport 80 -j ACCEPT
		
 	>iptables -D INPUT 1						#删除第一条规则

	>iptables -A INPUT -m mac --mac-source 00:00:00:00:00  -j DROP #禁止mac地址访问我
 
	>iptables -t filter -P INPUT DROP		#默认规则清除所有设置后的所有端口，注意哈，当初我学习的时候，第一个设置的，结果我自己上不去了（最后）

####一般服务器简单设置实例

	iptables -F
	#内部网络使用
	iptables -A INPUT -i lo -j ACCEPT			
	#状态连接				
	iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
	#允许80端口
	iptables -A INPUT -p tcp --dport -p 80 -j ACCEPT 
	#允许ssh
	iptables -A INPUT -p tcp --dport -p 22 -s x.x.x.x -j ACCEPT
	#允许mysql
	iptables -A INPUT -p tcp --dport -p 3306 -j ACCEPT
	#允许ftp
	iptables -A INPUT -p tcp --dport -p 21 -j ACCEPT
 	#允许邮件服务器
	iptables -A INPUT -p tcp --dport 110 -j ACCEPT
	iptables -A INPUT -p tcp --dport 25 -j ACCEPT
	#关闭掉其他所有
	iptables -P INPUT  DROP
	#保存防火墙规则
	service iptables save
	#重启生效
	service iptables restart
 


#####selinux

	>sestatus	              #查看selinux的状态
	>vim /etc/selinux/config
	SELINUX=disabled	      #禁用selinux
	>reboot 		          #重启服务器才能生效

###软件安装yum

####光盘安装

>这个如果是服务器的话，估计很少用到，也不可能谁没事就挂载个光盘来吧！当然你没事你可以做测试玩~

#####首先，你要挂载关盘

	mount /dev/cdrom /media

#####修改源
	
	#进入源仓库配置文件
	cd /etc/yum.repos.d/

	#移除根仓库源 不然会首先找这个
	mv CentOS-Base.repo CentOS-Base.repo.bak

	#修改配置信息

	[c6-media]
	name=CentOS-$releasever - Media
	baseurl=file:///media/
	#关闭签名
	gpgcheck=0				
	#开启原件源仓库
	enabled=1
	gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

#####安装软件

	>rpm -ivh xxx.rpm  #软件的时候,可能依赖包没安装，最可能的是你安装的依赖会很多，所以我们用yum
	>yum list		   #查看软件列表
	>yum install mysql #安装mysql 如果你感觉输入y比较烦，就yum install mysql -y
	>rpm -qa|grep mysql#查看安装mysql的一些包和依赖包
	>yum remove  mysql #卸载，可能卸载掉一些依赖库，但是这些依赖库可能对你还很有用
	>rpm -e xxxx       #单个卸载 --nodeps 是强制卸载

#####修改镜像源

>这个我们经常使用，因为我的服务器一般都在国内，一般安装好的都是centos的原有镜像，速度来说肯定没有国内快，所以我就换成国内的镜像源，一般我用的是163的，因为换太简单了，当然你也可以自己手动写，没有什么技术含量

	>cd /etc/yum.repos.d/										#进入
	>mv CentOS-Base.repo	 CentOS-Base.repo.bak					#备份
	>wget http://mirrors.163.com/.help/CentOS6-Base-163.repo		#下载
	>mv CentOS6-Base-163.repo  CentOS-Base.repo					
	>yum makecache												#清楚本地缓存
	

	#按照这种格式就可以填写你自己的仓库源 其中$releasever是版本,我的环境是6,$basearch我的是i386
	[base]
	name=CentOS-$releasever - Base - 163.com							#镜像源名称
	baseurl=http://mirrors.163.com/centos/$releasever/os/$basearch/	#镜像源地址
	gpgcheck=1														#gbk签名
	gpgkey=http://mirror.centos.org/centos/RPM-GPG-KEY-CentOS-6		#gpk签名地址
	
	#released updates 
	[updates]
	name=CentOS-$releasever - Updates - 163.com
	baseurl=http://mirrors.163.com/centos/$releasever/updates/$basearch/
	gpgcheck=1
	gpgkey=http://mirror.centos.org/centos/RPM-GPG-KEY-CentOS-6
	
	#additional packages that may be useful
	[extras]
	name=CentOS-$releasever - Extras - 163.com
	baseurl=http://mirrors.163.com/centos/$releasever/extras/$basearch/
	gpgcheck=1
	gpgkey=http://mirror.centos.org/centos/RPM-GPG-KEY-CentOS-6
	
	#additional packages that extend functionality of existing packages
	[centosplus]
	name=CentOS-$releasever - Plus - 163.com
	baseurl=http://mirrors.163.com/centos/$releasever/centosplus/$basearch/
	gpgcheck=1
	enabled=0
	gpgkey=http://mirror.centos.org/centos/RPM-GPG-KEY-CentOS-6
	
	#contrib - packages by Centos Users
	[contrib]
	name=CentOS-$releasever - Contrib - 163.com
	baseurl=http://mirrors.163.com/centos/$releasever/contrib/$basearch/
	gpgcheck=1
	enabled=0
	gpgkey=http://mirror.centos.org/centos/RPM-GPG-KEY-CentOS-6		

	>yum makecache
	>yum list				


####查看服务器进程

	>ps -ef |grep httpd	  #查看进程
	>pstrese | grep httpd #从进程树查看

	>netstat -tunpl | grep 80 
	-t	tcp
	-u  udp 
	
	>uptime #显示登录信息和平均负载
	>who 	#显示正在登录的用户

#####关闭进程
	
	>pkill mysql
	>kill -9 pid
	

 
###修改主机名称
	
>临时修改
	
	hostname widuu

>修改配置文件修改

	vim /etc/sysconfig/network	

###硬盘分区和挂载

>这个很重要的了，如果你用云主机或者其它的服务器的时候肯定都待有分区和挂载，因为我发现大部分都不会给你挂载的，譬如阿里云、金山云、电信云等

1.分区表配置文件

	>vim /etc/fstab			#分区配置文件表
	/dev/cdrom  /media iso9660	defaults 0 0 

	mount -a #测试光驱挂载是否成功

	fdisk /dev/sdb
	p #查看当前分区表
	l #查看linux支持的文件系统
	n #增加新分区
	w #保存分区

	>fdisk -l 								#查看文件分区，找到为分区的

![fdisk -l](http://widuu.u.qiniudn.com/linux/fdiskl.png)

	>fdisk /dev/sdb							#我们需要的分区/dev/sdb
	
![fdiskm](http://widuu.u.qiniudn.com/linux/fdiskm.png)

	>Command(m for help):m					#寻求帮助
	
	>Command action							#增加一个分区，然后选择 e扩展分区 p主分区
	   e   extended
	   p   primary partition (1-4)
	>p										#分为主分区
	>Partition number (1-4): 1				#主分区number
	>First cylinder (1-1044, default 1): 1  #设置柱面
	>Last cylinder, +cylinders or +size{K,M,G} (1-1044, default 1044): +3076M #设置硬盘大小

![fdiskp](http://widuu.u.qiniudn.com/linux/fdiskp.png)	

	>p 										#查看系统分区
	>t										#修改系统类型
	>l										#显示系统类型
	
![fdiskt](http://widuu.u.qiniudn.com/linux/fdiskt.png)

	>w										#保存系统分区
	>mkfs.ext4	/dev/sdb1					#格式化硬盘
	>mkdir /data && mount /dev/sdb1	/data	#挂载硬盘
	>df -h 									#查看硬盘挂载状况
		
![fdisk](http://widuu.u.qiniudn.com/linux/fdisk.png)

	>vim /etc/fstab							#设置开机挂载硬盘,添加如下
	/dev/sdb1  /data ext4   defaults  1 2	#这个和如下一一对应
	fs_spec　		fs_file　	fs_type　	fs_options　fs_dump　  fs_pass	
	挂载的文件系统	挂载点位置	文件系统类型  参数选项     是否dump   无需启动扫描0 跟分区启动扫描1 其它2即可
	>mount -a 								#测试一挂载，千万要测试，我记得我做的最低级错误就是写错了，结果主机重启了，你懂得起不来了，我擦 损失大了
	>unmout /dev/sdb1						#卸载硬盘分区


###用户权限管理

1. `ls -l`
2. `chmod a+x xxx.sh`
3. `sudo /usr/sbin/useradd widuu`
4. 


###文件权限

>文件权限三种 r-w-x 可读，可写，可执行 ，针对与文件和目录是不同的

>文件的权限如下

![rwx](http://widuu.u.qiniudn.com/linux/rwx.png)	

>上边我们切割一下

>d --文件类型 目录

>rwx|rwx|r-xm,每三个分成一组

 - 第一组 是文件所有者，这里是root有可读可写可执行(u)
 
 - 第二组 是root组的其它用户有可读可写可执行		 (g)
 
 - 第三组 是other用户，就是既不属于文件所有者用户也不属于用户所有组，可读可执行 	(o)

r w x --> 4 2 1 对应这4个值 ，你可以想成2的0次方，1次方，2次方

	对于目录来说 x 可执行权限意思是可以切换进入到这个目录 也就是cd /filedir
	对于目录来说 w 可写就是创建文件的权限或者创建文件夹的权限  mkdir test
	对于目录来说 r 可读就是列出目录的权限 譬如 ls -l

	对于文件来说 x 可执行，譬如.sh脚本有没有执行的权利
	对于文件来说 w 可写，就是写入，譬如vi demo.txt必须有w权限
	对于文件来说 r 可读权限，就是more cat less tail等能不能读取数据

>chmod 给文件或者目录权限

	>chmod 0777 test.txt              #test.txt的权限-rwxrwxrwx 
	>chmod u+rwx,g+rwx,o+rwx test.txt #同上的权限 +给权限 -是删除权限
	>chmod a+x                        #所有用户追加x权限
	>chmod -R 777 dir                 #递归添加dir目录所有权限

>chown 分配用户和组

	>chown www:www /var/www/html     #给html目录 www用户和www用户组权限

>id 查看用户信息

	>id xiaowei 
	uid=501(xiaowei) gid=501(xiaowei) groups=501(xiaowei)
	
	>gpasswd -a xiaowei root #将xiaowei用户添加到root
	>gpasswd -d xiaowei root #从root组中删除

	>usermod -L xiaowei      #锁定xiaowei用户 
	>usermod -U xiaowei      #解锁xiaowei用户


>sudo 权限设置

	>visudo 
	>xiaowei localhost=/usr/sbin/useradd #添加到文件中，给xiaowei有useradd权限
	>sudo useradd xiaowei1               #可以添加用户了

>添加删除用户

	>groupadd   xiaowei                 #添加用户组
 	>useradd -g xiaowei xiaowei         #添加用户并且添加到用户组
 	>userdel -r xiaowei                 #删除用户


###ACL权限分配

>一个公司里不可能让所有人都有root权限，所以在一些文件和文件夹处理，以及权限处理的时候就很棘手，这个时候我们就需要ACL给用户进行权限分配

1.加权限
	
	>setfacl -m u:username:rwx filedir #设置ACL

2.查看权限

	>getfacl filedir                   #获取ACL
	
	# file: filedir
	# owner: root
	# group: root
	user::rwx
	user:username:rwx
	group::r-x
	mask::rwx
	other::r-x


3.删除权限

	>setfacl -b u:username filedir     #删除u这个用户的acl权限 
	>setfacl -b filedir                #删除filedir的ACL所有权限
	
4.分配权限

>nginx有时候就是因为权限的问题造成了nginx对cpu的负载过高，我们可以直接使用ACL对其分配权限

	#nginx执行的用户是www，我们要www有nginx文件夹的所有权限
	#nginx的安装目录是/usr/local/nginx
	>setfacl -m u:www:rwx -R /usr/local/nginx #-R 文件递归





	



