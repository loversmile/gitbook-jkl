## Git email error

* 问题现象：

		remote: ERROR:  In commit 024c0bd88da87274f956a10dfa6b41e8c69ebd04
		remote: ERROR:  author email address xxxxx@xx.com
		remote: ERROR:  does not match your user account.

* 解决方法：

  * （1）git  config user.email (email地址)
  * （2）git checkout -b aaa（如果已经被占，随便再换一个）
           git branch -D master
           git checkout --track origin/master
  * （3）git push xxx此时就成功了


