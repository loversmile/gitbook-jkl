## ubuntu系统编辑器要怎么更换默认为vim

###　一、直接在终端输入：

	echo export EDITOR=/usr/bin/vim >> ~/.bashrc

###　二、使用系统管理工具update-alternatives

在终端输入：

	update-alternatives --config editor

如下：

	jklou@jklou-loversmile:~$ sudo update-alternatives --config editor
	有 4 个候选项可用于替换 editor (提供 /usr/bin/editor)。
	
	  选择       路径              优先级  状态
	------------------------------------------------------------
	* 0            /bin/nano            40        自动模式
	  1            /bin/ed             -100       手动模式
	  2            /bin/nano            40        手动模式
	  3            /usr/bin/vim.basic   30        手动模式
	  4            /usr/bin/vim.tiny    10        手动模式

	要维持当前值[*]请按<回车键>，或者键入选择的编号：3
	update-alternatives: 使用 /usr/bin/vim.basic 来在手动模式中提供 /usr/bin/editor (editor)

然后选择vim.basic这项即可

###　三、完全删除nano

如果系统中只剩下nano和vim两个编辑器，完全删除nano，那么系统会默认选择vim为默认编辑器
在终端输入删除nano编辑器的命令：
sudo apt-get remove nano
修改完成之后我们输入：
crontab -e
久违的vim编辑界面闪亮登场了。