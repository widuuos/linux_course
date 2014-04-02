帐号管理
===

>认识一下linux的权限管理其实认的不是你的用户名和密码而是识别的你的UID和GID说白了，就是你的用户ID和群组ID

	>cat /etc/passwd
	
	root:x:0:0:root:/root:/bin/bash
	daemon:x:1:1:daemon:/usr/sbin:/bin/sh

>上边的0就是用户id，群组id也是0，当然是我现在的用户root

>用户登录的时候，会根据你的用户名，譬如我的root来去/etc/passwd有没有这个用户，如果没有跳出去，如果有就去/etc/shadow来找对应uid的密码，最后执行登录

	
	 root  : x  :  0  :  0:  root  :  /root:   /bin/bash
     root    	#用户名
	 x       	#代表口令，现在存放在/etc/shadow中
	 0       	#用户的Uid
	 
	 #其中0是root，1-100是发行版自动创建的，100-499是系统需求时可以创建，500以后是一般使用的
	
	 0       	#用户的GId
	 root    	#用户信息栏说明
	 /root   	#家目录
	 /bin/bash  #支持shell

	# /etc/shadow
	>cat /etc/shadow
	root:$6$5SjcaJAS$EH0r0LsApaWsmpoz5Zoz4iSavI7nr./VVmUi5GnFYLsScVS7fTWz9JK7E8..bNajM7jV2qBBn9fck5oiGzx0N1:16147:0:99999:7:::  

	root 		#用户名
	$……1		#特别长那个是密码
	16147		#更改口令的日期
	0			#口令可以更改的日期，就是相对上一个16147的更改日期，0 就是随时修改
	99999		#口令重新变更的天数
	7			#口令警告，就是口令要过期了，windows用户应该看见过
	第7个没有值	#这个是口令失效日期
	第8个字段	#帐号失效日期
	第9个字段	#保留

	
###一般使用命令
	
**useradd -G 用户 次要群组**

	 >usermod -G users xiaowei
	 >grep xiaowei /etc/shadow /etc/passwd /etc/group
	 /etc/shadow:xiaowei:!:16147:0:99999:7:::
	 /etc/passwd:xiaowei:x:1001:1001::/home/xiaowei:/bin/sh
	 /etc/group:users:x:100:xiaowei
	 /etc/group:xiaowei:x:1001:

**groups** 支持的群组

	> groups
	root xiaowei

**newgrp** 切换群组

	>newgrp xiaowei
	>id
	uid=0(root) gid=1001(xiaowei) groups=0(root),1001(xiaowei)

**useradd** 添加帐号

	 useradd [-u UID] [-g 初始群组] [-G 次要群组] [-mM] [-c 说明栏] [-d 家目录绝对路径] [-s shell] 使用者账号名
	
	选项与参数：
	-u  ：后面接的是 UID ，是一组数字。直接指定一个特定的 UID 给这个账号；
	-g  ：后面接的那个组名就是我们上面提到的 initial group 啦～
	      该群组的 GID 会被放置到 /etc/passwd 的第四个字段内。
	-G  ：后面接的组名则是这个账号还可以加入的群组。
	      这个选项与参数会修改 /etc/group 内的相关数据喔！
	-M  ：强制！不要创建用户家目录！(系统账号默认值)
	-m  ：强制！要创建用户家目录！(一般账号默认值)
	-c  ：这个就是 /etc/passwd 的第五栏的说明内容啦～可以随便我们配置的啦～
	-d  ：指定某个目录成为家目录，而不要使用默认值。务必使用绝对路径！
	-r  ：创建一个系统的账号，这个账号的 UID 会有限制 (参考 /etc/login.defs)
	-s  ：后面接一个 shell ，若没有指定则默认是 /bin/bash 的啦～
	-e  ：后面接一个日期，格式为『YYYY-MM-DD』此项目可写入 shadow 第八字段，
	      亦即账号失效日的配置项目啰；
	-f  ：后面接 shadow 的第七字段项目，指定口令是否会失效。0为立刻失效，
	      -1 为永远不失效(口令只会过期而强制于登陆时重新配置而已。)
	
	# 添加一个widuuweb用户，uid为800 群组id1001 强制创建家目录，描述为test 家目录/root/test 不允许登录
	> useradd -u 800 -g 1001 -m -c test -d /root/test -s /sbin/nologin widuuweb
	> grep widuuweb /etc/passwd
	widuuweb:x:800:1001:test:/root/test:/sbin/nologin

**passwd** 修改密码

	passwd [-l] [-u] [--stdin] [-S]  [-n 日数] [-x 日数] [-w 日数] [-i 日期] 账号 <==root 功能
	选项与参数：
	--stdin ：可以透过来自前一个管线的数据，作为口令输入，对 shell script 有帮助！
	-l  ：是 Lock 的意思，会将 /etc/shadow 第二栏最前面加上 ! 使口令失效；
	-u  ：与 -l 相对，是 Unlock 的意思！
	-S  ：列出口令相关参数，亦即 shadow 文件内的大部分信息。
	-n  ：后面接天数，shadow 的第 4 字段，多久不可修改口令天数
	-x  ：后面接天数，shadow 的第 5 字段，多久内必须要更动口令
	-w  ：后面接天数，shadow 的第 6 字段，口令过期前的警告天数
	-i  ：后面接『日期』，shadow 的第 7 字段，口令失效日期
	
	>passwd -l xiaowei  #这个时候就锁定无法登录了
	>passwd -u xiaowei  #解锁可以登录了
	>passwd  xiaowei    #输入密码更换密码
	
	#同passwd -S有相同功能的chage
	选项与参数：
	-l ：列出该账号的详细口令参数；
	-d ：后面接日期，修改 shadow 第三字段(最近一次更改口令的日期)，格式 YYYY-MM-DD
	-E ：后面接日期，修改 shadow 第八字段(账号失效日)，格式 YYYY-MM-DD
	-I ：后面接天数，修改 shadow 第七字段(口令失效日期)
	-m ：后面接天数，修改 shadow 第四字段(口令最短保留天数)
	-M ：后面接天数，修改 shadow 第五字段(口令多久需要进行变更)
	-W ：后面接天数，修改 shadow 第六字段(口令过期前警告日期)

**usermod** 修改用户参数

	-c  ：后面接账号的说明，即 /etc/passwd 第五栏的说明栏，可以加入一些账号的说明。
	-d  ：后面接账号的家目录，即修改 /etc/passwd 的第六栏；
	-e  ：后面接日期，格式是 YYYY-MM-DD 也就是在 /etc/shadow 内的第八个字段数据啦！
	-f  ：后面接天数，为 shadow 的第七字段。
	-g  ：后面接初始群组，修改 /etc/passwd 的第四个字段，亦即是 GID 的字段！
	-G  ：后面接次要群组，修改这个使用者能够支持的群组，修改的是 /etc/group 啰～
	-a  ：与 -G 合用，可『添加次要群组的支持』而非『配置』喔！
	-l  ：后面接账号名称。亦即是修改账号名称， /etc/passwd 的第一栏！
	-s  ：后面接 Shell 的实际文件，例如 /bin/bash 或 /bin/csh 等等。
	-u  ：后面接 UID 数字啦！即 /etc/passwd 第三栏的数据；
	-L  ：暂时将用户的口令冻结，让他无法登陆。其实仅改 /etc/shadow 的口令栏。
	-U  ：将 /etc/shadow 口令栏的 ! 拿掉，解冻啦！

	>usermod -c nihao xiaowei 
	xiaowei:x:1001:1001:nihao:/home/xiaowei:/bin/sh

	>usermod -d /root/xiaowei xiaowei #xiaowei家目录就是这个位置了
	
	>usermod -s /sbin/nologin xiaowei
	>su xiaowei
	#无法登录了
	Cannot execute /sbin/nologin: No such file or directory

**userdel** 删除用户
	
	>userdel widuuweb  #删除用户 userdel -r widuuweb 连同家目录一起删除
	
**id**

>前边使用过了

	>id xiaowei  #显示用户的用户群组信息
	uid=1001(xiaowei) gid=1001(xiaowei) groups=1001(xiaowei),100(users)

**groupadd** 添加组

	>groupadd -g 102 -r xiaowei  #-g 组id可有可无 -r 组名
	>groupadd widuuweb
	> grep widuuweb /etc/group
	#widuuweb:x:1003:

**groupmod** 修改组信息
	
	groupmod [-g gid] [-n group_name] 群组名
	>groupmod -g 201 -n widuu xiaowei 

**groupdel** 删除群组
	
	>groupdel widuu
	
**gpasswd** 群组管理

	选项与参数：
    ：若没有任何参数时，表示给予 groupname 一个口令(/etc/gshadow)
	-A  ：将 groupname 的主控权交由后面的使用者管理(该群组的管理员)
	-M  ：将某些账号加入这个群组当中！
	-r  ：将 groupname 的口令移除
	-R  ：让 groupname 的口令栏失效

	>gpasswd -A xiaowei widuu #xiaowei加入widuu群组
	>gpasswd widuu            #设置口令 
	>gpasswd widuu xiaowei1   #讲xiaowei1加入群组
<<<<<<< HEAD
=======
	


>>>>>>> 4fea5aed0a907844e1b53d75ab29037816b46388

