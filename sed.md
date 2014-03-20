sed
===

###详细参数

	sed [-nefri] 动作

		-n   #只显示被处理的那一行
		-e   #命令行处理进行sed
		-f   #sed -f filename 执行filename中的sed
		-r   #基础正则表达式
		-i   #直接修改内容不输出

	#动作参数

		a #新增
		c #替换 这个是n,m行 替换成后边的字符串
		d #删除
		i #插入
		p #打印
		s #替换



