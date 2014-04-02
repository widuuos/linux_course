##shell 教程（上）
###到底什么是shell?
#####1.操作系统最外层，能够操纵应用程序的接口/软件都可以叫做shell.
#####2.shell也是一种程序设计语言。
如图：

![rcconf](http://linuxcourse.qiniudn.com/shell.jpg)

>linux下的默认shell为"Bourne Again SHell"（简称bash).此外还有许多版本的shell.另一个广为流传的shell是C shell.linux是C编写的，所以C shell也比较热门。我们这里讲的是Bash shell.

>我们可使用的shell存在/etc/shells.这个文件的存在的意义在于：当系统某些服务在运行过程中会检查能够使用的shells（Centos release 6.2 Final）：

![rcconf](http://linuxcourse.qiniudn.com/etcshells.jpg)


>这有什么用呢？

>1.举个例子，如果你想控制用户可以使用的服务，比如只让用户使用FTP，你就可以在检查用户可用shell的同时给他一些奇怪的shell,让用户无法以其它服务使用主机资源。

>2.而用户什么时候取得shell来工作？我这个用户默认取得的shell在哪儿看？事实上当用户登陆的时候系统就会给用户一个shell来工作了。这个shell可以在/etc/passwd查看：

![rcconf](http://linuxcourse.qiniudn.com/etcpasswd.jpg)

#####Bash shell的功能

    >1.命令记忆能力（history)
    >2.命令补全功能（[Tab]）[Tab]接在一串命令的第一个字的后面，为命令补全;接在一串命令的第二字后面，为文件补全
    >3.命令别名设置功能（alias）
    >4.作业控制、前台、后台控制（job control,foreground,background）
    >5.程序脚本（shell script)
    >6.通配符（Wildcard）例如X开头 以X为开头的文件
    
#####Bash shell的内置命令：type
>为了方便shell的操作，bash内置了很多命令。我们可以使用type命令获知你输入的命令是外部命令（非bash所提供的命令）还是内置在bash的命令。

    #type [-tpa](参数) name(命令的名称)
	type:不加任何参数，type会显示命令的路径和基本信息
	-t:将那name以下面这些字眼显示出它的意义:
		file:外部命令
		alias:该命令为命令别名所设置的名称（例如list命令别名为ls 当使用type -t ls时 显示alias 即ls是某（list）命令的别名）
		builtin:bash内置的命令
	-p:如果name是外部命令（file）显示完整文件名
	-a:会由PATH变量定义的路径中，将所有含name的命令都列出来，包括alias

>注意：type后面接的命令如果是未执行过的文件，显示路径；接的是执行过的文件，显示路径以及基本信息；如果后面并不是执行文件，该命令不会被显示出来。

>由此可见，type找出的是"执行文件"而并非一般的文件名。

#####命令的执行

	[root@www ~]# command [-options] parameter1 parameter2 ...
				命令		  选项		 参数
	1. command是命令或可执行文件。
	2. 实际情况下'[]'是不存在的。当加入参数设置时，其形式是'-'加上参数。例如 ls -l；有时候会使用参数的完整全名,例如--help
	3. parameter1 等为依附在option后面的参数，或是command的参数。
	4. 命令，-option，参数以空格区分，不论空几个格shell都视为一格。
	5. 按下[Enter]立即执行。如果命令太长，可使用反斜杠(\)转义[Enter].注意，反斜杠必须紧跟着[Enter].如果在下一行出>则表示[Enter]转义成功。
	6. 在linux系统中，英文大小写字母不一样。
  
#####shell 变量
>shell 作为一种程序设计语言，也有变量存在。

>变量就是一组文字或符号，来替代一些设置或一串保留的数据。变量主要可分为环境变量和个人设置的变量。环境变量通常用大写字符来表示，例如PATH,HOME,MAIL,SHELL等。个人变量由用户自行设置。

#####变量的显示
>可以使用echo.例如

    [root@www ~]# echo $variable  //自定义的变量
	[root@www ~]# echo $PATH      //系统的环境变量
           or     echo ${PATH}
	/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin:/root/bin

#####变量的修改与设置
    [root@www ~]# echo $myname //这是用户自定义的变量 未被设置 是空的。如果我想让这个变量输出我的名字呢
    [root@www ~]# myname=likeog
	[root@www ~]# echo $myname
    显示:likeog.变量的值可以随便修改的。当自定义变量未被设置的时候，其值默认为空。
    变量的设置规则:
    1.变量名=变量内容。例如myname=likeog.
    2.'='两边不能直接接空格符。以下三种情况都是错误的。

![rcconf](http://linuxcourse.qiniudn.com/bianliang.jpg)     

    3.变量名称只能是英文字母与数字，开头不能是数字。
    4.变量内容若有空格符可使用双引号" " "或者单引号" ' "将变量内容内容结合起来。
    (1)双引号内的特殊字符如$,可以显示其"内容"。
	
![rcconf](http://linuxcourse.qiniudn.com/syh.jpg)

    (2)单引号内的特殊字符为一般字符(纯文本)。 

![rcconf](http://linuxcourse.qiniudn.com/dyh.jpg) 

    5.可使用转义字符"\"将特殊符号(如[Enter]、$、\、空格符、！)变成一般字符。
    6.在一串命令中，还需要通过其它的命令提供的信息，可以使用反单引号" `命令` "或"$(命令)"。这个反单引号就是数字按键1左边那个按键。     
    下面的uname是当前操作系统的名称。       

![rcconf](http://linuxcourse.qiniudn.com/fyh.jpg)

    7.若为变量增加内容，直接用"$变量名称"或${变量}累加内容。
    
![rcconf](http://linuxcourse.qiniudn.com/blupdate.jpg)

    8.注意你的变量只能在本进程使用，如果想在子进程执行，需要以export把变量声明为环境变量方可。
	export PATH

>这里有必要解释一下什么是"子进程"。所谓子进程就是在我目前这个shell的情况下，去打开另一个新的shell,这个新的shell就是子进程。一般情况下父进程的变量是无法在子进程里面使用的。当你使用export变量将变量转换为环境变量以后就能在子进程使用了。
    
	9.通常大写字符为系统默认变量，自定义变量为小写字母。
	10.取消变量使用unset
	unset shiyan
    
#####环境变量的功能
>我们可以使用env和export查看当前shell环境下所有环境变量与其内容。
>env用来查看当前产看环境变量

	[root@www ~]# env
	HOSTNAME=localhost //这台主机的主机名
	TERM=xterm		   //这个终端机使用的环境类型
	SHELL=/bin/bash    //我们使用的什么shell
	HISTSIZE=1000	   //记录命令的条数，默认是1000
	SSH_CLIENT=114.113.221.164 5493 22 //安全外壳协议客户端信息 依次是IP地址
	SSH_TTY=/dev/pts/0 //使用的哪个tty端口
	USER=root		   //当前用户
	LS_COLORS=rs=0:... //颜色显示
	PATH=/usr/local/...//执行文件的查找目录
	PWD=/root		   //当前用户工作目录
	LANG=en_US		   //所使用的语系
	HOME=/root		   //当前用户的主文件夹
	_=/bin/env		   //上一次使用命令的最后一个参数（或命令本身）

>set查看所有变量（含有环境变量和自定义变量）set变量可以显示环境变量以及bash内的其他变量。

	[root@www ~]# set
	(同env的略)
	HISTFILE=/root/.bash_history    //历史命令记录的放置文件 隐藏文件
	MAILCHECK=60					//默认每隔60s扫描一次信箱有无新邮件
	PS1='[\u@\h \w]$ '              //命令提示符也就是我们常见的[root@www ~]# 后面是可设置的参数
	PS2='> '						//如果你使用转义符号(\)第二行以后的提示符也可以被列出来
	$								//当前这个shell所使用的PID
    ？								//刚才执行完命令的回传码
	OSTYPE=linux-gnu					//linux内核-gnu的自由软件系统
	HOSTTYPE=x86_64					//x86_64 64位的CPU 注意 新的软件在旧的机器上可能不支持
	MACHTYPE=x86_64-redhat-linux-gnu

>下面来讲解一下PS1(提示符的设置)当我们使用回车键值执行某个命令后，最后要再次出现提示符时，就会读取这个变量。

	\d				  //可显示"星期月日"的日期格式 如"Mon Feb 2"
	\H				  //完整的主机名
	\h				  //仅取主机名在第一个"."的名字 
	\t				  //显示时间 "HH:MM:SS" 24小时制
	\T 				  //显示时间 "HH:MM:SS" 12小时制
	\A				  //显示时间 "HH:MM"    24小时制
	\@				  //显示时间  am/pm	   12小时制
	\u				  //显示当前用户的账号名称 如'root'
	\v				  //BASH的版本信息
	\w			 	  //完整的工作目录名称
	\W				  //利用basename函数取得工作目录名称。但主文件夹会以~替代。
	\#				  //执行的第几个命令
	\$				  //提示符。如果是root账号是"#" 否则是"$"
	
>PS1的默认内容是[\u@\h \w]\$。来，我们来尝试修改一下提示符的内容。

![rcconf](http://linuxcourse.qiniudn.com/PS1.jpg)

>$本身也是一个变量。代表的是目前这个shell的线程代号,就是PID.

>?这个是上个命令的回传码。这也是个bash变量.这个变量的用处是返回上个命令是否正确执行。如果正确返回0，反之则返回一个错误码(数字)。

	[root@localhost ~]# echo $SHELL
	/bin/bash		//正确显示变量 没有错误。
	[root@localhost ~]# echo $?
	0				//返回0 表示上一个命令是正确执行的
	[root@localhost ~]# 1myname=likeog //这个变量是错误的，开头不能是数字
	-bash:1myname=likeog:command not found
	[root@localhost ~]# echo $?
	127				//返回127 上个命令错误
	[root@localhost ~]# echo $?
	0 				//注意哦 返回上一个命令的是否正确 上一个命令成功执行 所以返回0

#####环境变量/自定义变量的转换

>环境变量能被子进程继续引用，自定义变量需要声明为环境变量(export)后才能为子进程继续使用。

   在实际的系统调用情况下是这样的:

![rcconf](http://linuxcourse.qiniudn.com/set.jpg)

   通过上图，我们可以知道子进程仅会继承父进程的环境变量，不会继承父进程的自定义变量。你可以在主控文件后面调用其它文件(类似函数功能)，用export来将你的自定义变量声明为环境变量，后面的文件或者其它程序就可以调用你自定义的变量了。

#####locale语系变量

	当我们使用man命令来查询某个数据的说明文件时，说明文件的内容可能因为语系不同而产生乱码。 
	我们可以使用locale来查看语系。这个语系的配置文件在/etc/sysconfig/i18n.
	我们还可以使用locale -a来查看linux语系所支持的所有语系。
	其中LANG=..代表的是主语言的环境。LC_ALL=代表的是整体语系的环境。一般设置第一个就可以改变你的语言。例如vim /etc/sysconfig/i18n LANG=zh_CN.UTF-8
	因为Linux主机的主端口接口环境下是无法显示中文这样的编码文字的，所以我们必须要在tty1-tty6环境下装一些中文化接口的软件才能看到中文。
	如果你是在windows主机中以远程连接服务器的软件连接到主机，命令行界面可以看到中文。
	例如putty.

#####变量的有效范围
	                  
	简单来说，环境变量=全局变量；自定义变量=局部变量。
	底层的原理基本是这样的：
	当启动一个shell,linux会分配一记忆块给shell使用，此内存的变量可让子进程取用；
	如果父进程利用export，则自定义变量的内容可写到上述的记忆块当中；
	当加载另一个shell时(即启动子进程，而离开父进程)，子shell可以将父shell的环境变量所在的记忆块导入自己环境变量的记忆块中。

![rcconf](http://linuxcourse.qiniudn.com/jincheng.jpg)

	需要注意的是，这个"环境变量"与"bash的操作环境"意思不太一样，例如PS1并不是环境变量，但是这个PS1会影响到bash的接口(提示符)。 

#####变量属性的声明，读取键盘输入的内容并声明为变量read,declare,array

	read:读取来自键盘输入的变量。能实现与用户交互。
	[root@www ~]# read [-pt] variable
	参数
	-p:后面接提示符(由你自己输入 提示用户该输入什么内容)
	-t:后面接等待的描述，如果超出你所限定的时间，则不会等待用户。

![rcconf](http://linuxcourse.qiniudn.com/read.jpg)

	第一次我们没有使用任何参数。当使用read命令时。光标会提示你输入内容。如图我输入了this is only a test.我们输出 test这个变量时，就显示了我刚才通过键盘输入的内容。
	第二次我们使用了-p参数，提示用户输入你的名字，当我们使用read命令时，下面会显示我们用来提示的内容Please keyin your name.输出named这个变量，显示了我们的名字。
	第三次我们还使用了时间限制。将时间限制在10s，如果用户在10s内未输入内容，则跳过改命令。
	当我们输出变量的时候，什么都没有显示。因为10s之后自动跳过了该命令。如果我们输入了字符没有按回车呢？效果也是一样的。如下图，我输入了d但是没有按下回车，10s之后，d被当做普通字符出现在提示符后。输出变量仍然没有内容。

![rcconf](http://linuxcourse.qiniudn.com/read2.jpg)


	declare/typeset:声明变量的类型。如果declare后面没有接任何参数，bash主动将所有的变量名称与内容调出来。
	[root@www ~]#declare [-aixr] 变量名
	参数
	-a:将变量定义为数组类型
	-i:将变量定义为整数数字(integer)
	-x:将变量设置为环境变量
	-r:将变量设置为readonly类型，不能被更改也不能被重设。
	
	让我们来看看declare的用处
	[root@www ~]# sum=100+300+50
	[root@www ~]# echo $sum 
	100+300+50   //为什么没有出计算结果？下面解释
	[root@www ~]# declare -i sum=100+30+50
	[root@www ~]# echo $sum
	450			//通过declare命令将变量声明为整数之后相加就输出结果了。	

bash在默认情况下的变量定义：

1.变量类型默认为"字符串"，如果不指定变量类型，则1+2是一个字符串不是计算式。

2.bash环境中的数值运算，默认只能达到整数类型。所以比如1/3计算后的结果是0。

这就是declare变量声明的作用。如果需要非字符串类型的变量，需要先进行变量的声明。

	#将sum声明为环境变量
	[root@www ~]# declare -x sum
	[root@www ~]# export | grep sum
	declare -ix sum="450"  //显示了i与x的声明

	#让sum变成非环境变量的自定义变量
	[root@www ~]# declare +x sum //'-'号变成'+' 就是取消的意思
	[root@www ~]# declare -p sum //-p可以单独列出变量的类型(如果变量sum没有内容则会显示-bash:h: declare: sum: not found)
	declare -i sum="450"	 //只剩下-i 没有x(环境变量)的属性了

	#让sum变成只读属性 不可改动
	[root@www ~]# declare -r sum
	[root@www ~]# sum=test
	-bash: sum: readonly variable //这个变量不能改变了。如果你将变量设置为只读，只要注销再登陆就能恢复该变量的类型了。


数组(array)变量类型

	array:将变量类型设置为数组。bash默认的是一维数组。
	数组的设置:var[index]=content.var是数组名，index是索引名，意为第几个元素。比如说，数组的里面的第一个内容是small，就可以写成var[1]="small"。明白了吗？

	#设置一个数组，数组名是name,内容分别是张三，王五，草榴
	
![rcconf](http://linuxcourse.qiniudn.com/array.jpg)

#####与文件系统以及程序的限制关系       

我们的bash是通过ulimit命令来限制用户的系统资源的。比如说用户可以打开的文件数量，可以使用的CPU时间，可以使用的内存总量等等。
	
	[root@www ~]# ulimit [-SHacdfltu] [配额]
	参数:
	-H:hard limit,严格的设置，必定不能超过这个设置的数值。
	-S:soft limit,警告的设置，可以超过这个设置值，但是若超过则有警告信息。
	   在设置中，通常soft会比hard小，举例来说,soft可设置为80，hard设置为100，那么你可以使用到90（没超过100）但是介于80~100之间时，系统会有警告信息通知你。
	-a:后面不接任何参数，可列出所有的限制额度。
	-c:当某些进程发生错误时，系统可能会将该进程在内存中的信息写成文件(排错用)。
	   这种文件就被称为内核文件(core file),此为限制每个内核文件的最大容量。
	-f:此shell可以创建的最大文件容量(一般设置为2GB)单位为KB.
	-d:进程可使用的最大断裂内存(segment)容量。
	-l:可用于锁定(lock)的内存量。
	-t:可使用的最大CPU时间(单位为秒)。
	-u:单一用户可以使用的最大进程(process)数量。


常用命令 ulimit -a 查看你目前身份(假设为root)的所有限制数据数值
	
![rcconf](http://linuxcourse.qiniudn.com/ulimita.jpg)

常用命令 ulimit -f 10240(自定义) 限制用户仅能创建10MB一下容量的文件
如果你一直无法创建一个无法创建的文件大小，查看你所在用户ulimit的信息。

	[likeog@www ~]# ulimit -a
	...
	file size		(blocks，-f) unlimited //可创建单一文件的大小
	[likeog@www ~]# ulimit -f 10240
	[likeog@www ~]# ulimit -a
	...
	file size       (blocks,-f) 10204 //这里变为可创建单一文件大小限制为10240KB 相当于10MB
	[likeog@www ~]# dd if=/dev/zero of=123 bs=1M count=20
	File size limit exceeded 尝试创建20MB的文件 结果失败。

需要注意的是，想要复原ulimit的设置最简单的方法就是注销再登陆。如果你是用一般身份用户以ulimit设置了-f文件的大小，那么他只能继续减小文件容量，不能增加文件容量。

#####变量内容的删除 替代与替换

来，让我们做个实验来说明一下。以下实验用PATH这个变量的内容作测试。

	#首先，让小写的path设置与PATH内容相同
	[root@www ~]# path=${PATH}
	[root@www ~]# echo $path
	/usr/kerberos/.....

	#然后，假如我不喜欢kerberos这个目录 将前两个目录删除掉，如何做？
	[root@www ~]# echo ${path#/*kerberos/bin:}
	/usr/local/sbin:/usr/local/bin:/sbin:...

	#这个删除'指令'到底是怎样执行的？让我们来分析一下。
	${变量名#/*kerberbos/bin:}
	1.${}:这是关键字，用在删除模式是必须存在的；
	2.变量名:就是变量名称，例如上面的path变量；
	3.#:删除操作符，代表从变量内容的最前面开始向右删除；删除掉符合的最短数据；
	4./*kerberbos/bin:  :这一段内容代表要被删除的部分，从哪儿开始删除就从哪儿开始写。例如上面，我们要删除"/usr/kerberos/sbin:/usr/kerberos/bin:"，开始的字符是'/'，结束的字符'：'，但是kerberos前面的*是什么意思呢？
	这里的*是通配符的意思。学过正则表达式的同学都明白，这里只是简单介绍一下。这个'*'代表匹配0到无穷多个任意字符。
	所以，上面要删除的两段带有kerberos的数据都被匹配了，即"usr/kerberos/sbin:/usr/"，然后被删除掉，明白？

	#现在我们想删除前面所有的目录 仅仅保留最后一个目录呢？
	[root@www ~]# echo ${path#/*:}
	/usr/kerberos/bin:/usr/local/...
	前面我们说了，由于一个#仅能删除最短的那个，因此它只删除了/usr/kerberos/sbin:

	#现在我告诉你两个"##"号是删除符合的最长数据，你现在能做到删除前面所有的目录，仅仅保留最后一个目录吗？
	[root@www ~]# echo ${path##/*:}
	/root/bin
	现在只显示这个目录了。

下面来总结一下我们的例子。

	在我们上面声明的path这个变量的内容中，每个目录都是以冒号":"隔开的，所以要从头删除掉目录就是介于斜线(/)和冒号(:)之间的数据。但是PATH中不只是一个冒号(:),所以#与##号分别代表：
	#：符合替换文字的"最短的"那一个
	##:符合替换文字的"最长的"那一个
	其实我们还有从后面向前删除变量内容的方法，使用的是%。用法完全同上，只不过这次是从后往前删除了。
	#事实上，替换也很简单。
	[root@www ~]# echo ${path/sbin/SBIN}
	/usr/kerberos/SBIN：/...
	看到没？两个斜线之间的是旧字符串，而斜线后面的是新字符串。这里的替换提示符是'/',同样的，如果是一条斜线只替换第一个字符，两条斜线则是替换该命令内所有旧字符为指定新字符。

好了，我们来整理一下。

>变量的设置方式

	${变量#关键字}					若变量内容从头开始的数据符合"关键字"，则将符合的最短数据删除
	${变量##关键字}					若变量内容从头开始的数据符合"关键字"，则将符合的最长数据删除
	${变量%关键字}					若变量内容从尾向前的数据符合"关键字"，则将符合的最短数据删除
	${变量%%关键字}					若变量内容从尾向前的数据符合"关键字"，则将符合的最长数据删除
	${变量/旧字符/新字符串}			若变量内容符合旧字符串，则第一个旧字符串会被新字符串替换
	${变量//旧字符/新字符串}			若变量内容符合旧字符串，则所有旧字符串会被新字符串替换	  

什么？变量还需要测试？没错。下面我们做下变量的测试。

>在某些时候我们需要"判断"某变量是否存在，若存在则使用原有的设置，若不存在则使用你的设置。

	[root@www ~]# echo $username
					//出现空白 所以username变量不存在，也可能是空字符串
	[root@www ~]# username=${username-root}
	[root@www ~]# echo $username
	root			//因为username没有设置，所以主动给予名称为root的内容
	[root@www ~]# username="likeog" //这次我们先设置好username的内容为likeog
	[root@www ~]# username=${username-root}
	[root@www ~]# echo $username
	likeog			//显示的是我们开始设置好的内容 并没有用root替代。

	#我们来分析一下上面的例子。
	username=${username-root}(这里的新旧变量名称是一样的)
	新变量=${旧变量-内容}
	新变量:用来替换旧的变量；
	${}:关键字部分必须存在；
	旧变量:旧的变量，也可以说是被测试的选项；
	内容:变量的内容，这里是给予未设置变量的内容；

	#上面的例子有点问题。我们来看看当username是空字符串的时候：
	[root@www ~]# username=""
	[root@www ~]# username=${username-root}
	[root@www ~]# echo $username
					//因为username本来就被设置为空字符串 所以还是保留为空字符串
	[root@www ~]# username=${username:-root}
	[root@www ~]# echo username
	root			//加上:号以后 若变量内容为空或者是未设置 都能以后面的内容替换。	

上面的例子中，我们其实是设置的同一个'变量'（改变了变量的值）。而实际情况中，我们操作的是两个名称不同的变量，姑且称之为旧变量，新变量。
>事实上，变量的设置方式有- + ？ =有以下四种方式。我们知道，当旧变量未被设置和没有旧变量的时候，变量设置的情况是不一样的。 以下用列表方式来帮大家归纳这些情况。

>我们是针对旧变量str的是否有设置来决定新变量var的值。

>注：str:代表str:没设置或为空的字符串时，str则仅表示没有该变量。str=旧变量，var=新变量。expr=变量内容。

	变量设置方式     		str没有设置			str为空字符串		str已设置（非空字符串)
	var=${str-expr}     var=expr            var=                var=$str
	var=${str:-expr}    var=expr            var=expr            var=$str
	var=${str+expr}		var=				var=expr			var=expr
	var=${str:+expr}	var=				var=				var=expr
	var=${str= expr}	str=expr			str不变				str不变
						var=expr			var=				var=$str
	var=${str:=expr}	str=expr			str=expr			str不变
						var=expr			var=expr			var=$str
	var=${str? expr}	expr输出至strderr    var=				var=str
	var=${str:?expr}	expr输出至strderr    expr输出至strderr    var=str

让我们用实例来验证一下上面的例子。  
	
	# '-'判断变量是否存在，（1）如果变量不存在 则给变量一个常用设置 （2）如果变量内容未设置或设置为空字符串  则给变量一个新的设置
	# 现在是str（旧变量）不存在的情况
	[root@www ~]# unset str；var=${str-new}  //设置新变量内容为new
	[root@www ~]# echo var="$var"，str="$str"
	var=new，str=				//因为str不存在，所以var为newvar
	# 现在是str(旧变量)已经存在设置的情况
	[root@www ~]# str="old"；var=${str-new} //旧变量内容未old 新变量内容未new
	[root@www ~]# echo var="$var"，str="$str"
	var=old,str=old				//因为str已经存在 所以var=str 可以验证上表为正确

	# '='则会将旧变量的内容一起替换掉。上面的例子可以看出'-'号不会影响到旧变量的内容，
	# 现在是str（旧变量）不存在的情况
	[root@www ~]# unset str；var=${str=new}
	[root@www ~]# echo var="$var"，str="$str"
	var=new，str=new				//因为旧变量str不存在，所以新旧变量都为new
	# 现在是str(旧变量)已经存在设置的情况
	[root@www ~]# str="old"；var=${str=new}
	[root@www ~]# echo var="$var"，str="$str"
	var=old，str=old				//因为str已经存在，所以新旧变量都为old 上表正确
	 
	# 我只是想判断一下变量是否存在，如果不存在则告知错误。这时我们使用"?".
	[root@www ~]# unset str；var=${str?无此变量}
	-bash：str：无此变量 因为str不存在，所以输出错误信息
	#如果str存在的呢？
	[root@www ~]# str="old"；var=${str?no var}
	[root@www ~]# echo var="$var"，str="$str"
	var=old,str=old				//因为str存在，var=str                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             