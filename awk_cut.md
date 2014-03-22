字符处理
===

###cut选字段

>我个人感觉cut是比较简单的，而我推荐使用的是awk因为awk的功能感觉才是最强大的，尤其awk配合sed

####cut语法

	cut -c list [file...]
	cut -f list [-d delim] [file ..]

	-c list 执行剪操作字符级别的，例如1，3
	
	-d delim 定界符 

	-f 以字段为主做剪切的操作，这个是字段级别的

####用法举例

	#从ls -l 截取字符串10个
	>ls -l | cut -c 1-10
	
![cut1]( http://widuu.u.qiniudn.com/linux/str/cut1.png)

	#从passwd读取数据，-d 以：分割，-f 取 第1和第3个字段
	>cat /etc/passwd | cut -d : -f 1,3
	
![cut2]( http://widuu.u.qiniudn.com/linux/str/cut2.png)
	

###AWK

>所有的unix和unix-like里边，当然我这只是介绍简单的使用，如果你向深入我推荐看《sed和awk第二版》

####awk语法
	
	#语法两种形式
	awk [options] 'script' var=value file(s)
	awk [options] -f scriptfile var=value file(s)

	#命令
	-F fs         											#制定文件的分隔符，fs是一个字符串或者正则表达式
	-v var=value  											#赋值一个用户定义变量
	-f scriptfile 											#从awk脚本文件中读取
	-mf nnn 		 										    #对nnn值设置内在限制，标准awk中不适用
	-W 			  											#在兼容模式下运行awk
	-W compact or --compat, -W traditional or --traditional #兼容模式下运行awk
	-W copyleft or --copyleft, -W copyright or --copyright  #打印版权信息
	-W help 													#打印awk的帮助
	-W posix 												#兼容模式
	-W lint  												#移植警告
	-W version 												#版本信息

	#变量
	$n			#当前记录的第n个字段，字段间由FS分隔。
	$0			#完整的输入记录。
	ARGC		#命令行参数的数目。
	ARGIND		#命令行中当前文件的位置(从0开始算)。
	ARGV		#包含命令行参数的数组。
	CONVFMT		#数字转换格式(默认值为%.6g)
	ENVIRON		#环境变量关联数组。
	ERRNO		#最后一个系统错误的描述。
	FIELDWIDTHS	#字段宽度列表(用空格键分隔)。
	FILENAME	#当前文件名。
	FNR			#同NR，但相对于当前文件。
	FS			#字段分隔符(默认是任何空格)。
	IGNORECASE	#如果为真，则进行忽略大小写的匹配。
	NF			#当前记录中的字段数。
	NR			#当前记录数。
	OFMT		#数字的输出格式(默认值是%.6g)。
	OFS			#输出字段分隔符(默认值是一个空格)。
	ORS			#输出记录分隔符(默认值是一个换行符)。
	RLENGTH		#由match函数所匹配的字符串的长度。
	RS			#记录分隔符(默认是一个换行符)。
	RSTART		#由match函数所匹配的字符串的第一个位置。
	SUBSEP		#数组下标分隔符(默认值是\034)。

	#运算符
	= += -= *= /= %= ^= **=	 #赋值
	?:						 #C条件表达式
	||					 	 #逻辑或
	&&						 #逻辑与
	~ ~!					 #匹配正则表达式和不匹配正则表达式
	< <= > >= != ==			 #关系运算符
	空格						 #连接
	+ -						 #加，减
	* / &					 #乘，除与求余
	+ - !				     #一元加，减和逻辑非
	^ ***					 #求幂
	++ --					 #增加或减少，作为前缀或后缀
	$						 #字段引用
	in						 #数组成员

####举例讲解

	>awk -W version #查看版本
	>awk -W lint    #兼容信息

	#awk -W 同志们可以自己去实验哈

	#我们继续实现上边那个取出/etc/passwd第1和3个字段
	# -F 制定分隔符 $1,$3取出1，3字段 print打印
	>cat /etc/passwd | awk -F ':' '{print $1,$3}'
	
![awk1](http://widuu.u.qiniudn.com/linux/str/awk1.png)

	#上边结果跟上次不一样呢，这次的数据中间是空格，这是因为默认的输出字段
	#分隔符OFS是空格 所以我们需要自己指定 OFS=":"
	>cat /etc/passwd | awk -F ':' '{OFS=":";print $1,$3}'

![awk2](http://widuu.u.qiniudn.com/linux/str/awk2.png)

	#我想输出行数，怎么搞？前边的NR当前记录数从1开始我们可以试下
	>cat /etc/passwd | awk -F ':' '{OFS=":";print $1,$3,NR}'

![awk3](http://widuu.u.qiniudn.com/linux/str/awk3.png)

	#我想输出以前原本的字段数，怎么搞？NF来帮助你
	>cat /etc/passwd | awk -F ':' '{OFS=":";print $1,$3,NR,NF}'
	
![awk4](http://widuu.u.qiniudn.com/linux/str/awk4.png)

	#我们默认的记录分割符是换行，我们修改会是什么效果?这里我换成@
	> cat /etc/passwd | awk -F ':' '{OFS=":";ORS="@";print $1,$3,NR,NF}'
	
![awk5](http://widuu.u.qiniudn.com/linux/str/awk5.png)

	# 少写了一个 ‘~’匹配操作符，这个东西很重要，匹配表达式正则,我们还拿前边的
	#grep里边的test.txt数据如下
		widuu http://www.baidu.com
	
		NIHAO
		
		the linux is the best
		
		Widuu Linux.
	>awk '$0 ~/^widuu/' test.txt  #匹配以widuu开头的
	
![awk6](http://widuu.u.qiniudn.com/linux/str/awk6.png)

	# 支持逻辑表达式 我们拿!逻辑非做测试
	>cat /etc/passwd | awk -F ':' '$1 !~/[0-9]/{print $1}'

![awk7](http://widuu.u.qiniudn.com/linux/str/awk7.png)

	# awk有BEGIN和END模块，我们可以善加利用，就是执行动作之前和之后
	> awk 'BEGIN{print "start"}$0 ~/^widuu/{print $0}END{print "end"}' test.txt
	
![awk8](http://widuu.u.qiniudn.com/linux/str/awk8.png)

	# 条件语句 if else,举个简单的例子，我们想知道free -m 中空闲的内存 
	>free -m | awk '{if(FNR==2){print $4}}'

	# 再来个逻辑运算符 三元运算符
	> free -m | awk '{if(FNR==2){print ($4>10?"full memory":"no")}}'

![awk10](http://widuu.u.qiniudn.com/linux/str/awk10.png)

	# 文件重定向 重定向的文件用""包括，这个语句就是如果空闲内存小于10就清除cache
	> free -m | awk '{if(FNR==2){if($4>10){print "full memory"}else{ print 3 >"/proc/sys/vm/drop_caches"}}}'	

![awk11](http://widuu.u.qiniudn.com/linux/str/awk11.png)

	# 管道符|的处理 将输出的内容发送到getline 并且赋值给变量d 再打印
	>awk '{"echo yourusername:"|getline d;print d,$0;}'
	
![awk12](http://widuu.u.qiniudn.com/linux/str/awk12.png)

	# 支持for 循环
	> free -m | awk '{for(i=2;i<=FNR;i++){print $4}}'

![awk13](http://widuu.u.qiniudn.com/linux/str/awk13.png)

	# 还支持while do for in等等 循环，写就可以了

####内建函数

	#sub 匹配并且替换
	> awk '{ sub(/widuu/, "widuuweb"); print }' test.txt

![awk14](http://widuu.u.qiniudn.com/linux/str/awk14.png)

	#全文匹配用gsub用法一样
	> awk '{ gsub(/widuu/, "widuuweb"); print }' test.txt

	# 搜索字符串的位置 index(字符串，被搜索的字符串)
	>awk '{print index($0,"linux") }' test.txt

![awk16](http://widuu.u.qiniudn.com/linux/str/awk16.png)

	# length 返回字符串的长度
	> awk '{print length}' test.txt
	
![awk18](http://widuu.u.qiniudn.com/linux/str/awk18.png)

	#substr(字符串，开始位置，终止位置) 截取字符串长度
	> awk '{print substr($0,1,10)}' test.txt
	
![awk17](http://widuu.u.qiniudn.com/linux/str/awk17.png)

	#toupper和tolower 是大小写转换的 
	> awk '{ print toupper("test"), tolower("TEST") }' 1.html #输出TEST test
	
	#split 切割字符串为数组的 split( string, array, field separator )
	>awk '{split("wi,duu,web",web,",");print web[1]}' 1.html

![awk19](http://widuu.u.qiniudn.com/linux/str/awk19.png)


	

	




	


	