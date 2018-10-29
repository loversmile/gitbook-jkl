## git 配置


#### git 目录创建
在本地使用如下的命令，就可以完成你的第一次提交了：  

	mkdir git-usage（创建项目目录）  
	cd git-usage（进入项目目录） 
	git init（Git初始化）   
	touch README（创建一个README文件）    
	git add README（增加该文件到索引）    
	git commit -m 'first commit'（本地提交）    
	git remote add origin git@github.com:phoenixtoday/git-usage.git（增加远程服务器代码库地址）    
	git push -u origin master（将本地代码提交到远程服务器上）

#### 命令行配置
* 配置vim编辑器

		git config --global core.editor vim

* 配置用户名，email

		$ git config --global user.name jklou
		$ git config --global user.email jklou@email.com

* 配置commit模板

		git config --global commit.template /home/jklou/project/CommitTemplate

* alias 配置 缩写
		git config --global alias.st status

#### 配置文件配置

一般git的全局配置文件会存在 ~/.gitconfig 中  

* 我的配置文件

		[core]                                                                                                                                                                                                  
		    editor = vim 
		[commit]
		    template = /home/jklou/Todo/code/CommitTemplate
		[user]
		    email = jklou@email.com
		    name = jklou
		[alias]
		    st = status
		    d = diff
		    co = checkout
		    pl = pull
		    b = branch
		    com = commit
		    coma = commit --amend
		    lp = log -p
		    pm = push origin HEAD:refs/for/master
		[merge]
		    tool = vimdiff

          
#### 一次提交多个版本库更新
修改多个git提交的问题,即一次提交多个版本库更新  
目前是在github下新增一个新的版本库，即同步到不同的服务器上  
编辑.git 下的config

	[remote "origin"]
	        url = https://username@github.com/username/project/
	        url = http://username@192.168.89.1:8000/project
	        fetch = +refs/heads/*:refs/remotes/origin/*
上面的配置会提示输入两次密码  
在git push后会显示
  
	‘No refs in common and none specified’doing nothing

输入```git push origin master```即可解决问题


　