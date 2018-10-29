## gdb 调试vector等


在GDB进行调试的时候，查看STL容器是个比较痛苦的事情，比如查看vector, map 这些容器，根本看不到里面的数据是什么。  
现在介绍一个gdb 查看STL容器神器。  
文件：

	http://www.yolinux.com/TUTORIALS/src/dbinit_stl_views-1.03.txt

下载上述文件，按照文件要求吧上述文件存成".gdbinit"文件放到你的根目录下。  
或者在gdb的时候输入 “source 脚本文件名”.  
就可以把脚本load 到当前环境中。  
这个脚本支持一下命令：

	pvector
	plist
	pmap
	pmap_member
	pset
	pdequeue
	pstack
	pqueue
	ppqueue
	pbitset
	pstring 
	pwstring