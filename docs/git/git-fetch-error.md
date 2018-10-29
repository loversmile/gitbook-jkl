## git fetch error

在使用git fetch的时候，遇到了如下错误  

	Unable to negotiate with 192.168.4.28 port 19428: no matching key exchange method found. Their offer: diffie-hellman-group1-sha1
	fatal: Could not read from remote repository.

	Please make sure you have the correct access rights
	and the repository exists.

#### 解决方法：
* 1、载入变量
 
		$export GIT_SSH_COMMAND='ssh -o KexAlgorithms=+diffie-hellman-group1-sha1'

参考网址：https://www.drupal.org/node/2552319 请自备梯子  
上面方法貌似没有解决根本问题

* 2、修改ssh config文件
 
		sudo gedit /etc/ssh/ssh_config 
		在host下加入 
		KexAlgorithms=+diffie-hellman-group1-sha1

* 3、还有一个办法 
在用户目录下的.ssh文件夹新建一个config文件 
输入

		Host *  
		    KexAlgorithms +diffie-hellman-group1-sha1

好，问题解决了。