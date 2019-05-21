## python工具

#### 一些工具
setuptools-25.1.1  
pip-8.1.2  
PyInstaller-3.2  


## 打包成exe可执行文件
* 使用pyinstaller

##### 安装 pyinstaller
	pip install pyinstaller

##### 执行
	pyinstaller.exe  -w  --onefile --icon="my.ico" demo.py
	--icon 		生成exe文件的图标
    --onefile 	打包成单个文件
    -w    		不使用控制台输出

##### pyinstaller 目录
    一般来说，pip安装的工具会在Python安装目录的Scripts下面 
	E:\Python27\Scripts pyinstaller

##### pyinstaller 参数
	查看参数
	pyinstarller -h

#### ubuntu 

##### 安装pip
	sudo apt-get install python-pip

##### 安装mysql
	https://pypi.org/simple/mysql-connector-python/
	https://www.liaoxuefeng.com/wiki/001374738125095c955c1e6d8bb493182103fac9270762a000/001391435131816c6a377e100ec4d43b3fc9145f3bb8056000

###### Audio Fingerprinting with Python and Numpy
	https://willdrevo.com/fingerprinting-and-audio-recognition-with-python/