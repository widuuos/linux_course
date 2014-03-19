linux基础教程-再谈脚本编程(1)
===

####1.脚本第一行

	#!/bin/csh -f   让解释器知道这是csh脚本
	#!/bin/awk		让解释器知道这是awk脚本
	#!/bin/bash		让解释器知道这是bash脚本

	#最简单的脚本
	>vim hello.sh
	#!/bin/bash
	echo "hello word"
	>chmod a+x hello.sh
	>./hello.sh           #这时候就会出现hello word了
	
#####printf
	
	#printf是模仿c语言中的printf
	>printf "your website address %s\n" http://www.widuu.com
	>your website address http://www.widuu.com  #输出

#####输入输出重定向

	#输出重定向
	>ls -l > text.txt #改变输出到text.txt
	>more text.txt    #显示ls -l的信息

	#输入重定向
	>echo "ls -l22222222">text.txt #向text.txt输入
	>tr -d "2" < text.txt          #从text.txt中读取并且删除2
	>ls -l                         #输出的

	tr [-d 删除] [-C 取反] [-s 去重字符串]

#####文件设备 /dev/null和/dev/tty

	/dev/null     #简单来说就是写入到这里的字符串等等都被系统丢到
	/dev/tty      #重定向一个终端或者为终端

#####命令查找

	>echo $PATH
	#/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games

>bin目录下是你可执行文件的存放处，我们可以自己创建

	>mkdir $HOME/bin
	>mv hello $HOME/bin
	>PATH=$PATH:$HOME/bin #这样就可以直接执行了
	
>**注意PATH=只能临时用，开机就不能用了，这个时候我们就可以这样做**

	#假设我安装了golang 在/usr/go
	>vim ~/.profile
	export PATH=$PATH:/usr/go
	>sourse .profile
	

