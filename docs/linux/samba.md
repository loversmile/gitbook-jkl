## ubuntu samba 配置

打开配置文件编辑

	vi /etc/samba/smb.conf 
	[Todo]  ； 共享名
	    path = /home/jklou/Todo   ； 共享目录
	    writeable = yes
	;   browseable = yes
	    valid users = jklou 

	root@ubuntu:~# useradd jklou
	root@ubuntu:~# ./smbpasswd -a jklou
	New SMB password:
	Retype new SMB password:
	Added user jklou.


smbpasswd命令的常用方法
	
	smbpasswd -a 增加用户（要增加的用户必须以是系统用户）
	smbpasswd -d 冻结用户，就是这个用户不能在登录了
	smbpasswd -e 恢复用户，解冻用户，让冻结的用户可以在使用
	smbpasswd -n 把用户的密码设置成空.
	             要在global中写入 null passwords -true
	smbpasswd -x  删除用户