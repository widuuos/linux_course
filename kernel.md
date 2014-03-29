
###下载内核

>从官方网站https://www.kernel.org/下载内核，我下载的是linux-3.13.6.tar.xz

	cd /usr/src

	wget https://www.kernel.org/pub/linux/kernel/v3.x/linux-3.13.6.tar.xz

	xz -d linux-*.tar.xz

	tar xf linux-*.tar

	cd linux-3.13.6

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

	make menuconfig

	#2.6以前的版本是，但是现在的内核是不需要make dep了
	make dep 
	make bzlmage  
	make modules 
	make modules_install 
	make install

	#编译内核
	make 

	