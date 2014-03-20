文件查找
===

####find

>find命令功能强大切易用，但是一般时候我们能不用就不用，尤其譬如你的服务器负载正跑高的时候，他对CPU和内存的消耗是很大的，但是find的命令非常强大，所以我们必须还待学会。

#####find -name

	>find /etc -name host?
	#输出 /etc/hosts 其中？作为占位符使用
	
	>find /etc -name host*
	#这里的*号匹配任意字符
	/etc/hosts
	/etc/hosts.allow
	/etc/init.d/hostname
	/etc/hosts.deny
	/etc/host.conf
	/etc/init/hostname.conf
	/etc/hostname

#####find -iname

	>find /etc -iname host* # -iname是忽略大小写，跟我们编程语言中的模式修正符i效果是一样的

#####find -size

	>find ./ -size (+大于） （-小于） （无符号就是等于）
	#别被坑了哈 最小是512byte 所以要查找1M size就是2048
	
#####用户群组查找

	>find ./ -user root       #查找用户root的文件
	>find ./ -group root      #查找用户组的文件

#####文件时间属性查找

	#- 在多少分钟内 + 大于多长时间
	>find ./ -amin -5  #5分钟内访问时间改变的
	>find ./ -cmin -5  #5分钟内文件属性改变的
	>find ./ -mmin +5  #大于5分钟的文件内容被改变
	
#####多语句查询

	-a 				满足几个条件
	-o 				满足一个就可以了

	-exec 命令  {} \;		搜素执行操作

#####类型查找

	-type [-f 文件] [-d 目录] [-l 软链接]

	-inum 根据inode节点号查找

	-perm 按照文件权限来查找文件

#####其它

	 -nogroup 查找无有效所属组的文件，即该文件所属的组在/etc/groups中不存在
     
 	 -nouser 在/etc/passwd中不存在

	 -mount 在查找文件时不跨越文件系统mount点

####locate

>这个速度非常快，因为它有自己的文件库，所以我们建议使用locate，但是缺点是你创新的文件如果文件库没有更新就查找不到

	>locate hosts
	
	#下边就出来了
	/etc/hosts
	/lib/x86_64-linux-gnu/security/pam_rhosts.so
	/usr/share/man/man5/hosts.5.gz
	/usr/share/man/man5/hosts.equiv.5.gz
	/usr/share/man/man8/pam_rhosts.8.gz
	/usr/share/vim/vim73/ftplugin/denyhosts.vim
	/usr/share/vim/vim73/ftplugin/hostsaccess.vim
	/usr/share/vim/vim73/syntax/denyhosts.vim
	/usr/share/vim/vim73/syntax/hostsaccess.vim

	>updatedb           #更新文件库


####which和whereis

>查找命令使用，譬如/bin/ /usr/bin 如果你是管理员，还能出来/sbin/usr/bin/

	#查找命令和alias
	>which ls
	#/bin/ls

	grep [-i 不区分大小写] [-v 排除制定字符串]       #文件中字符串查找 我们以后正则的时候详细介绍

	#可以查找到命令和帮助文档
	>whereis ls
	#ls: /bin/ls /usr/share/man/man1/ls.1.gz

	