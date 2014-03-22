sed
===

###详细参数

	sed [-nefri] 动作

		-n   #只显示被处理的那一行
		-e   #命令行处理进行sed
		-f   #sed -f script-file 执行script-file 中的sed
		-r   #基础正则表达式
		-i   #直接修改内容不输出

	#动作参数

		a #新增
		c #替换 这个是n,m行 替换成后边的字符串
		d #删除
		i #插入
		p #打印
		s #替换

###详细使用

	#sed -e 命令行处理 -a 新增
	> sed -e '$ a widuu' test.txt  #在test.txt的结尾处添加widuu
	
![sed1](http://widuu.u.qiniudn.com/linux/sed/sed1.png)

	# 把-e换成-n 
	> sed -n '$ a widuu' test.txt  # 只显示被处理的一行
	
![sed2](http://widuu.u.qiniudn.com/linux/sed/sed2.png)

	# -c 替换多少行 为你制定的字符串,这里我的test.txt内容如图
	>  sed -e '2,5c xiaowei' test.txt #把2-5行替换成字符串xiaowei
![sed3](http://widuu.u.qiniudn.com/linux/sed/sed3.png)

	# 多命令执行 -e
	> sed -e '2,5c xiaowei' -e 's/cccc/widuu/' test.txt
	
![sed4](http://widuu.u.qiniudn.com/linux/sed/sed4.png)

	# -i 不输出 -s 替换
	>sed -i 's/111111/widuu/g' test.txt

![sed5](http://widuu.u.qiniudn.com/linux/sed/sed5.png)


	# i 插入 自定义的字符串 在test.txt的第二行插入xiaowei
	> sed -e '2i xiaowei' test.txt

![sed6](http://widuu.u.qiniudn.com/linux/sed/sed6.png)

	# d 删除输入任何字符串
	>  sed -e '2d' test.txt #删除第二行

![sed7](http://widuu.u.qiniudn.com/linux/sed/sed7.png)
	

###举个例子

>假设我要写一个七牛云存储备份的脚本，也可以供大家使用，里边有个配置文件，我希望手动输入来配置这个配置文件

	{
	    "access_key": "Please apply your access key here",
	    "secret_key": "Dont send your secret key to anyone",
	    "bucket": "Bucket name on qiniu resource storage",
	    "sync_dir": "Local directory to upload",
	    "async_ops": "fop1;fop2;fopN",
	    "debug_level": 1
	}

>代码如上，很简单做个简单的测试给大家，譬如我在linux上有一个qbox.conf,如下

![sed8](http://widuu.u.qiniudn.com/linux/sed/sed8.png)

	#做个简单的联系
	>sed -e 's/\"access\_key\"\:.*\,$/"access_key":"xiaowei"/g' qbox.conf

![sed9](http://widuu.u.qiniudn.com/linux/sed/sed9.png)

	#我们可以这样写，当然是做练习用，不能实际用哈，当我以后将shell编程的时候，会完善这个
	#!/bin/bash
	QPATH=/root/qbox.conf
	read -p "write your access_key:" access
	eval sed -i 's/\"access\_key\"\:.*\,$/\"access_key\":\"${access}\",/g' $QPATH
	read -p "write your secret_key:" secret
	eval sed -i 's/\"secret\_key\"\:.*\,$/\"secret_key\":\"${secret}\",/g' $QPATH
	read -p "write your bucket:" bucket
	eval sed -i 's/\"bucket\"\:.*\,$/\"bucket\":\"${bucket}\",/g' $QPATH
	read -p "write your sync_dir:" dir
	eval sed -i 's/\"sync_dir\"\:.*\,$/\"sync_dir\":\"${dir}\",/g' $QPATH
	read -p "write your async_ops:" async
	eval sed -i 's/\"async_ops\"\:.*\,$/\"async_ops\":\"${async}\",/g' $QPATH
	read -p "write your debug_level:" debug
	eval sed -i 's/\"debug_level\"\:.*\,$/\"debug_level\":\"${debug}\",/g' $QPATH

	#执行完效果如下图

![sed qbox_conf](http://widuu.u.qiniudn.com/linux/sed/sed10.png)

希望有兴趣的朋友一起加入widuu，然后分享开源，技术共享，我相信只有知识共享才能创造出高度的产品~ 有不足的请给予补充，有错误的请大家直接指出

Github地址：[http://github.com/widuu/linux_course](http://github.com/widuu/linux_course )

Gitcafe地址:[http://gitcafe.com/widuu/linux_course](http://gitcafe.com/widuu/linux_course )

	



	

	

	



	


	



