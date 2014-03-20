grep正则表达式
===

>介绍一些grep，我们学习其它语言中都含有正则，在linux里边叫做grep，以前的时候有egrep(支持扩展)和fgrep（快速的正则）到最后都合并成了grep 

###grep参数

	grep
	
		-E 使用正则表达式扩展，也就是以前我们所说的egrep

		-F 使用固定字符串匹配，相当于以前的fgrep,当你的grep没有参数的时候，默认使用的是grep -F

		-e 指定参数的形式
		
		--color 显示颜色

		-f 从文件读取模式做匹配

		-i 忽略大小写
		
		-l 列出匹配模式的文件名称

		-s 不显示错误信息

		-q 静默如果匹配grep成功离开，不输出

		-v 显示不匹配模式的行

		-n 显示行数

>兼容POSIX的标准

	[:alnum:]    #代表英文大小写字符及数字 a-z,A-Z,0-9
	[:alpha:]	 #代表英文大小字符A-Z,a-z
	[:blank:]	 #表示空格
    [:cntrl:]	 #控制键CR LF Tab Del
	[:digit:] 	 #代表数字0-9
	[:graph:]	 #除了空格符之外的所有字符
	[:lower:]	 #小写字符
	[:print:]	 #打印出来的字符串
	[:punct:]	 #代表?!;等标点符号
	[:upper:]	 #大写字母
	[:space:]	 #空白字符
	[:xdigit:]   #代表16进制

>特殊字符

>我感觉特殊字符，只要你会一种语言你就应该知道有这些字符

	\		#转义字符
	.		#除了null之外的字符
	*       #匹配前边的任意个数 .* 匹配任意字符
	^       #起始位置 在[]里边是除了什么 [^g]除了g
	$		#结束字符
	[..]	#写表达式
	\{n,m\} #区间表达式，前边的字符出现n-m次，\{n\}出现n次\{n,\}至少出现n次，\{,m\}最多出现m次
	\(\)    #子模式匹配
	\n 		#重复\(\)子模式的此时 n的值1-9
	+       #至少出现一次
	？      #出现0到一次
	\w		#字母

###使用讲解

	#测试数据

	widuu http://www.baidu.com

	NIHAO
	
	the linux is the best
	
	Widuu Linux.

---
	
	# -i 忽略大小写 n显示行数 --color颜色
	# 这个就是显示忽略大小写匹配widuu并且输出行数
	>grep -in 'widuu' --color=auto test.txt 

![grep1](http://widuu.u.qiniudn.com/linux/grep/grep1.png)

	# -v 显示不匹配的，就是显示不匹配widuu的行
	>grep -vn 'widuu' --color=auto test.txt
	
![grep2](http://widuu.u.qiniudn.com/linux/grep/grep2.png)

	# -E使用扩展正则表达式 匹配网址
	>grep -nE 'http\:\/\/(www|ftp)\.([^\.]+)\.(com|net|org)' --color=auto test.txt

![grep3](http://widuu.u.qiniudn.com/linux/grep/grep3.png)

	# -E 正则扩展匹配至少三个w
	>grep -nE 'w{3,}' --color=auto test.txt

![grep4](http://widuu.u.qiniudn.com/linux/grep/grep4.png)

	# \{n,m\}做到跟上边一样的效果
	>grep -n 'w\{3,\}' --color=auto test.txt
	
![grep5](http://widuu.u.qiniudn.com/linux/grep/grep5.png)
	
	# \(\) 子模式匹配 \1出现次数 匹配从i到i也就是一行里出现两个i
	>grep -n '\(i\).*\1' --color=auto test.txt

![grep6](http://widuu.u.qiniudn.com/linux/grep/grep6.png)

	# 匹配以.结尾的
	>grep -n '\.$' --color=auto test.txt

![grep7](http://widuu.u.qiniudn.com/linux/grep/grep7.png)

	# 匹配wi和bai后的所有字符
	> grep -nE '((wi|bai).*)' --color=auto test.txt

![grep8](http://widuu.u.qiniudn.com/linux/grep/grep8.png)

	# 匹配所有字符
	>grep -in '\w' --color=auto test.txt
	#grep -n '[[:alnum:]]' --color=auto test.txt 和上边效果相通
![grep9](http://widuu.u.qiniudn.com/linux/grep/grep9.png)

	# 匹配所有数字
	>grep -nE '^([0-9]+)$' --color=auto test.txt
	#grep -n '[[:digit:]]' --color=auto test.txt 和上边效果相同

![grep10](http://widuu.u.qiniudn.com/linux/grep/grep10.png)

	# 匹配三个大写字母
	> grep -n '[[:upper:]]\{3\}' --color=auto test.txt

![grep11](http://widuu.u.qiniudn.com/linux/grep/grep11.png)

**这里只是grep后边的sed awk等等都会用到，所以正则一定要学会**

东西可能不全，希望大家一块补充，这里widuu感谢大家了~~~ 不对的地方希望大家及时发现解决
	

	


	


	


