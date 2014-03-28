字符处理-sort，uniq，wc，join
===
###sort

	sort	[options] [file(s)]

	-b 	#忽略空白开头
	-c	#检查输入是否正确排序
	-d 	#字典顺序，只在数字和空白有效
	-g	#一般数值：以浮点数字类型比较字段
	-f	#将混用字母都看作相同大小写
	-i 	#忽略无法打印的字符
	-k	#定义排序键值字段
	-m	#将以排序的输入文件
	-n	#整形比较字段
	-o	#输出到制定文件
	-r	#倒置排序
	-t	#使用单个字符作为默认的分隔符
	-u	#只有唯一的记录，丢弃相同键值的记录

####举例

	#譬如我有个文件word，里边是这样排序的
	delete  word
	block   widuuweb
	create  website
	as      widuu
	
	>sort word        #默认按照第一个字段排序了
![sort1](http://widuu.u.qiniudn.com/linux/str/sort1.png)

	#这个时候我们在word中添加一个字段 空白 a
	>sort -b word     #忽略空白 一直找到字符位置，默认情况下就是-b 和sort word 效果是一样的
![sort2](http://widuu.u.qiniudn.com/linux/str/sort2.png)

	>sort -k2.2 word  #选取第2个字段的第2个字符作为排序
	
![sort3](http://widuu.u.qiniudn.com/linux/str/sort3.png)	
	
	>sort -t : -k1,1 /etc/passwd | head -n 5  #-t 分隔符 :切割 第一个字段1 用户名 排序 取出前5行
	
![sort4](http://widuu.u.qiniudn.com/linux/str/sort4.png)

	> sort -r -t : -k1,1 /etc/passwd | head -n 5 #与上边不同的是 -r 倒序排序

![sort5](http://widuu.u.qiniudn.com/linux/str/sort5.png)

	#现在譬如我有这个文件1，里边内容如下，我想让他俺后边的顺序排序怎么办？
	xiaowei 2
	widuu   3
	widuuweb 1
	nihao    4

	>sort -t ' ' -k2 -d 1    #-d是按照数字排序
	
![sort6](http://widuu.u.qiniudn.com/linux/str/sort6.png)

	>sort -t ' ' -k2 -d 1 -o sort  #-o 输出到文件

![sort7](http://widuu.u.qiniudn.com/linux/str/sort7.png)

	>sort -t: -k3nr /etc/passwd |head -n 5 #这个是：分割，取第三个字段，数字比较，然后取反，最后取出5条
	
![sort8](http://widuu.u.qiniudn.com/linux/str/sort8.png)

###uniq

	uniq [options]

		– c 显示输出中，在每行行首加上本行在文件中出现的次数。它可取代- u和- d选项。
	
		– d 只显示重复行。
		
		– u 只显示文件中不重复的各行。
		
		– n 前n个字段与每个字段前的空白一起被忽略。一个字段是一个非空格、非制表符的字符串，彼此由制表符和空格隔开(字段从0开始编号)。
		
		+n 前n个字符被忽略，之前的字符被跳过(字符从0开始编号)。
		
		– f n 与- n相同，这里n是字段数。
		
		– s n 与+n相同，这里n是字符数。

	#测试文件word1
	ccccc
	aaaaaaaaa
	aaaaaaaaa
	aaaaaaaaa
	ccccc
	dddddd
	dddddd

	>cat word1|uniq -c  #显示文本+重复次数
![uniq -c](http://widuu.u.qiniudn.com/linux/str/uniqc.png)
	
	>cat word1|uniq -d  #只显示重复的行
	>cat word1|uniq -u  #只显示不重复的行

###join

>这个的用法就跟sql中的关联查询是一样的

	join [options] file1 file2
     
	   注：这两个文件必须在已经在此列上是按照相同的规则进行了排序。

	   -a FILENUM：除了显示匹配好的行另外将指定序号（1或2）文件中部匹配的行显示出来
	   -e EMPTY：将需要显示但是文件中不存在的域用此选项指定的字符代替
	   -i :忽略大小写
	   -j FIELD ：等同于 -1 FIELD -2 FIELD,-j指定一个域作为匹配字段
	   -o FORMAT：以指定格式输出
	   -t CHAR ：以指定字符作为输入输出的分隔符
	          join 默认以空白字符做分隔符（空格和\t）,可以使用 join -t $'\t'来指定使用tab做分隔符
	   -v FILENUM：与-a相似 但值显示文件中没匹配上的行
	   -1 FIELD：以file1中FIELD字段进行匹配
	   -2 FIELD：以file2中FIELD字段进行匹配
	   --help ：打印命令帮助文件


####举例说明

	#以下是我两个文件join1和join2，都是sort -n 排序好的，相同的
	1   username				1  widuu
	2   password				2  widuupassword
	3   home					3  /root/widuu
	4   description				4  widuuuser
	5   shell					5  /sbin/bash
	10  join1					join2

	>join join1 join2           #根据双方排序对应的字段显示
	
![join](http://widuu.u.qiniudn.com/linux/str/join.png)

	>join -a1 join1 join2       #左关联 其中a1 a2指定关联文件

![join1](http://widuu.u.qiniudn.com/linux/str/join1.png)

	>join -a2 join1 join2       #右关联

![join2](http://widuu.u.qiniudn.com/linux/str/join2.png)
	
	>join -1 1 -2 1  join1 join2 #指定了以两个文件中第一列做匹配字段，效果和join join1 join2相同
	
	>join -v 1 -v 2 join1 join2  #-v 显示没有匹配的行，显示join1和join2没有匹配的行

![join4](http://widuu.u.qiniudn.com/linux/str/join4.png)

	>join -t: /etc/passwd /etc/shadow   #-t 分隔符，其实就是join -t: -1 1 -2 1 /etc/passwd /etc/shadow
	
![join5](http://widuu.u.qiniudn.com/linux/str/join5.png)

###wc 
	
	wc [options]

		-c 统计字节数。
		-l 统计行数。
		-m 统计字符数。这个标志不能与 -c 标志一起使用。
		-w 统计字数。一个字被定义为由空白、跳格或换行字符分隔的字符串。
		-L 打印最长行的长度。
		-help 显示帮助信息
		--version 显示版本信息

	
	>free -m | wc -c #输出230 就是230字节
	>free -m | wc -l #输出4   就是行数4
	>free -m | wc -m #跟-c效果一样
	>free -m | wc -w #去除了 空白分隔符等等
	

	
	




