https://blog.csdn.net/alexander_phper/article/details/52242474


找到http的配置文件，增加要添加用户密码的目录

	/etc/apache2/apache2.conf
	<Directory "/var/www/html/pwd">                                                                                                                                                                     
	     Options Indexes          
	    AllowOverride AuthConfig 
	    Order allow,deny         
	    Allow from all           
	</Directory>   


打开

	/var/www/html/pwd/.htaccess
配置

	AuthName "提示信息"                                                                                                                                                                                 
	AuthType basic   
	AuthUserFile  /var/www/html/pwd/.htpasswd
	require user user

	说明：.htaccess文件中常用的配置选项有以下几个：
	　　1) AuthName：定义提示信息，用户访问时提示信息会出现在认证的对话框中
	　　2) AuthType：定义认证类型，在HTTP1.0中，只有一种认证类型：basic。在HTTP1.1中有几种认证类型，如：MD5
	　　3) AuthUserFile：定义包含用户名和密码的文本文件，每行一对
	　　4) AuthGroupFile：定义包含用户组和组成员的文本文件。组成员之间用空格分开，如：group1:user1 user2
	　　5) require命令：定义哪些用户或组才能被授权访问。如：
	　　require user user1 user2 (只有用户user1和user2可以访问)
	　　requires groups group1 (只有group1中的成员可以访问)
	　　require valid-user (在AuthUserFile指定的文件中的所有用户都可以访问)


htpasswd -bc /var/www/html/pwd/.htpasswd user password  #第一次添加用户时.htpasswd文件不存在，需要用-c选项创建文件 


Digest 认证

	AuthName "digestRealm"                                                                                                                                                                                 
	AuthType digest   
	AuthUserFile  /var/www/html/pwd/.htpasswd
	require user user

htdigest -c /var/www/html/digest/.htpasswd "digestRealm" userd                   