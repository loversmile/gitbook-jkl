## ubuntu freeswitch 安装

看了freeswitch 权威指南，自己安装了一下 freeswitch，过程和一些问题如下

需要先通读全文，再进行安装，本文记录的是我的安装过程，期间遇到一些问题，又重新安装了些库、工具啊之类的，所以是先看完全文，把该安装的先装完，然后开始跑编译命令。

### 准备工作
* 系统环境

使用命令可以查看当前系统版本
	
		jklou@jklou-loversmile:~/jkl/fs$ sudo lsb_release -a
		No LSB modules are available.
		Distributor ID: Ubuntu
		Description:    Ubuntu 16.04.5 LTS
		Release:        16.04
		Codename:       xenial

		jklou@jklou-loversmile:~/jkl/fs$ uname -a
		Linux jklou-loversmile 4.4.0-138-generic #164-Ubuntu SMP Tue Oct 2 17:16:02 UTC 2018 x86_64 x86_64 x86_64 GNU/Linux
可以看出来，我的系统是ubuntu 16.04   64系统

* freeswitch

可以在网上下载 freeswitch 的版本，地址

		http://files.freeswitch.org/releases/freeswitch/

我选择的是 1.8.2 版本

	freeswitch-1.8.2.tar.gz	26-Sep-2018 15:24	51M	

### 安装

解压freeswitch

	tar -zxvf freeswitch-1.8.2.tar.gz 
	cd freeswitch-1.8.2

首先配置安装系统环境，以下命令是freeswitch 权威指南罗列的，照着安装就好了，书上是没有sudo的，因为用的不是root用户，所以加了sudo，注意在不是root用户的情况下，访问系统相关的目录（root 权限），都要加sudo

	sudo apt-get -y install build-essential automake autoconf git-core wget libtool
	sudo apt-get -y install libncurses5-dev libtiff-dev libjpeg-dev zlib1g-dev libssl-dev libsqlite3-dev
	sudo apt-get –y install libpcre3-dev libspeexdsp-dev libspeex-dev libcurl4-openssl-dev libopus-dev

安装书上介绍，执行

	./configure

这里可以不加sudo

执行出现如下错误

	configure: error: You need to either install libldns-dev or disable mod_enum in modules.conf

说明很明显，安装libldns-dev 或者disable mod_enum，我选择了安装 libldns-dev

	sudo apt-get install libldns-dev

这里又出错了

	configure: error: You need to either install libedit-dev (>= 2.11) or configure with --disable-core-libedit-support

继续按提示来，这次选择了加参数

	 ./configure --disable-core-libedit-support

这次成功了，然后执行install

	sudo make install

这里又报错了

		jklou@jklou-loversmile:~/jkl/fs/freeswitch-1.8.2$ sudo make install
		cd libs/libvpx && CC="gcc" CXX="g++" CFLAGS="-g -O2" CXXFLAGS="-g -O2" LDFLAGS="" ./configure --enable-pic --disable-docs --disable-examples --disable-install-bins --disable-install-srcs --disable-unit-tests --size-limit=16384x16384 --extra-cflags="-fvisibility=hidden"
		  enabling pic
		  disabling docs
		  disabling examples
		  disabling install_bins
		  disabling install_srcs
		  disabling unit_tests
		  enabling vp8_encoder
		  enabling vp8_decoder
		  enabling vp9_encoder
		  enabling vp9_decoder
		Configuring for target 'x86_64-linux-gcc'
		  enabling x86_64
		  enabling runtime_cpu_detect
		  enabling mmx
		  enabling sse
		  enabling sse2
		  enabling sse3
		  enabling ssse3
		  enabling sse4_1
		  enabling avx
		  enabling avx2
		Neither yasm nor nasm have been found. See the prerequisites section in the README for more info.
		
		Configuration failed. This could reflect a misconfiguration of your
		toolchains, improper options selected, or another problem. If you
		don't see any useful error messages above, the next step is to look
		at the configure error log file (config.log) to determine what
		configure was trying to do when it died.
		Makefile:4028: recipe for target 'libs/libvpx/Makefile' failed
		make: *** [libs/libvpx/Makefile] Error 1

安装yasm

	git clone https://github.com/yasm/yasm.git && cd yasm && ./autogen.sh && make && sudo make install

继续install

	sudo make install	

又报错了

	  CXXLD    libfreeswitch.la
	
	*** Warning: Linking the shared library libfreeswitch.la against the
	*** static library libs/libvpx/libvpx.a is not portable!
	ar: `u' modifier ignored since `D' is the default (see `U')
	 /bin/mkdir -p '/usr/local/freeswitch/lib'
	 /bin/bash /home/jklou/jkl/fs/freeswitch-1.8.2/libtool   --mode=install /usr/bin/install -c   libfreeswitch.la '/usr/local/freeswitch/lib'
	libtool: install: /usr/bin/install -c .libs/libfreeswitch.so.1.0.0 /usr/local/freeswitch/lib/libfreeswitch.so.1.0.0
	libtool: install: (cd /usr/local/freeswitch/lib && { ln -s -f libfreeswitch.so.1.0.0 libfreeswitch.so.1 || { rm -f libfreeswitch.so.1 && ln -s libfreeswitch.so.1.0.0 libfreeswitch.so.1; }; })
	libtool: install: (cd /usr/local/freeswitch/lib && { ln -s -f libfreeswitch.so.1.0.0 libfreeswitch.so || { rm -f libfreeswitch.so && ln -s libfreeswitch.so.1.0.0 libfreeswitch.so; }; })
	libtool: install: /usr/bin/install -c .libs/libfreeswitch.lai /usr/local/freeswitch/lib/libfreeswitch.la
	libtool: install: /usr/bin/install -c .libs/libfreeswitch.a /usr/local/freeswitch/lib/libfreeswitch.a
	libtool: install: chmod 644 /usr/local/freeswitch/lib/libfreeswitch.a
	libtool: install: ranlib /usr/local/freeswitch/lib/libfreeswitch.a
	libtool: finish: PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin:/sbin" ldconfig -n /usr/local/freeswitch/lib
	----------------------------------------------------------------------
	Libraries have been installed in:
	   /usr/local/freeswitch/lib
	
	If you ever happen to want to link against installed libraries
	in a given directory, LIBDIR, you must either use libtool, and
	specify the full pathname of the library, or use the '-LLIBDIR'
	flag during linking and do at least one of the following:
	   - add LIBDIR to the 'LD_LIBRARY_PATH' environment variable
	     during execution
	   - add LIBDIR to the 'LD_RUN_PATH' environment variable
	     during linking
	   - use the '-Wl,-rpath -Wl,LIBDIR' linker flag
	   - have your system administrator add LIBDIR to '/etc/ld.so.conf'
	
	See any operating system documentation about shared libraries for
	more information, such as the ld(1) and ld.so(8) manual pages.
	----------------------------------------------------------------------
	  CC       freeswitch-switch.o
	  CCLD     freeswitch
	  CC       fs_cli-esl.o
	  CC       fs_cli-esl_config.o
	  CC       fs_cli-esl_event.o
	  CC       fs_cli-esl_threadmutex.o
	  CC       fs_cli-fs_cli.o
	libs/esl/fs_cli.c: In function ‘main’:
	libs/esl/fs_cli.c:1424:6: error: variable ‘use_history_file’ set but not used [-Werror=unused-but-set-variable]
	  int use_history_file = 0;
	      ^
	cc1: all warnings being treated as errors
	Makefile:2555: recipe for target 'fs_cli-fs_cli.o' failed
	make[1]: *** [fs_cli-fs_cli.o] Error 1
	make[1]: Leaving directory '/home/jklou/jkl/fs/freeswitch-1.8.2'
	Makefile:3729: recipe for target 'install' failed
	make: *** [install] Error 2

打开 Makefile， 在1062 行把 SWITCH_AM_CFLAGS -Werror 参数去掉，继续

	sudo make install 

又报错

	making install mod_lua
	make[4]: Entering directory '/home/jklou/jkl/fs/freeswitch-1.8.2/src/mod/languages/mod_lua'
	  CXX      mod_lua_la-mod_lua.lo
	mod_lua.cpp:37:17: fatal error: lua.h: 没有那个文件或目录
	compilation terminated.
	Makefile:687: recipe for target 'mod_lua_la-mod_lua.lo' failed
	make[4]: *** [mod_lua_la-mod_lua.lo] Error 1
	make[4]: Leaving directory '/home/jklou/jkl/fs/freeswitch-1.8.2/src/mod/languages/mod_lua'
	Makefile:679: recipe for target 'mod_lua-install' failed
	make[3]: *** [mod_lua-install] Error 1
	make[3]: Leaving directory '/home/jklou/jkl/fs/freeswitch-1.8.2/src/mod'
	Makefile:587: recipe for target 'install-recursive' failed
	make[2]: *** [install-recursive] Error 1
	make[2]: Leaving directory '/home/jklou/jkl/fs/freeswitch-1.8.2/src'
	Makefile:3257: recipe for target 'install-recursive' failed
	make[1]: *** [install-recursive] Error 1
	make[1]: Leaving directory '/home/jklou/jkl/fs/freeswitch-1.8.2'
	Makefile:3729: recipe for target 'install' failed
	make: *** [install] Error 2

打开 modules.conf， 在129行，找到 #languages/mod_lua，前面加# 注释起来，因为下面还有相关的错误，所以在这个文件里面把

	42 #applications/mod_sms
	129 #languages/mod_lua

但是这种方式并不是很好，因为模块后续会用到，所有还是安装lua吧

	apt-get install libreadline-dev apt-get install lua5.2 lua5.2-doc liblua5.2-dev

	安装完将相关头文件拷贝一下
	cp /usr/include/lua5.2/*.h src/mod/languages/mod_lua/

	将so文件做个链接
	sudo ln -s /usr/lib/x86_64-linux-gnu/liblua5.2.so /usr/lib/x86_64-linux-gnu/liblua.so

继续

	./configure
	sudo make install

又错误

	Libraries have been installed in:
   	/usr/local/freeswitch/mod

	If you ever happen to want to link against installed libraries
	in a given directory, LIBDIR, you must either use libtool, and
	specify the full pathname of the library, or use the '-LLIBDIR'
	flag during linking and do at least one of the following:
	   - add LIBDIR to the 'LD_LIBRARY_PATH' environment variable
	     during execution
	   - add LIBDIR to the 'LD_RUN_PATH' environment variable
	     during linking
	   - use the '-Wl,-rpath -Wl,LIBDIR' linker flag
	   - have your system administrator add LIBDIR to '/etc/ld.so.conf'
	
	See any operating system documentation about shared libraries for
	more information, such as the ld(1) and ld.so(8) manual pages.
	----------------------------------------------------------------------
	make[5]: Leaving directory '/home/jklou/jkl/fs/freeswitch-1.8.2/src/mod/endpoints/mod_skinny'
	make[4]: Leaving directory '/home/jklou/jkl/fs/freeswitch-1.8.2/src/mod/endpoints/mod_skinny'
	
	making install mod_sndfile
	make[4]: Entering directory '/home/jklou/jkl/fs/freeswitch-1.8.2/src/mod/formats/mod_sndfile'
	Makefile:920: *** You must install libsndfile-dev to build mod_sndfile。 停止。
	make[4]: Leaving directory '/home/jklou/jkl/fs/freeswitch-1.8.2/src/mod/formats/mod_sndfile'
	Makefile:679: recipe for target 'mod_sndfile-install' failed
	make[3]: *** [mod_sndfile-install] Error 1
	make[3]: Leaving directory '/home/jklou/jkl/fs/freeswitch-1.8.2/src/mod'
	Makefile:587: recipe for target 'install-recursive' failed
	make[2]: *** [install-recursive] Error 1
	make[2]: Leaving directory '/home/jklou/jkl/fs/freeswitch-1.8.2/src'
	Makefile:3257: recipe for target 'install-recursive' failed
	make[1]: *** [install-recursive] Error 1
	make[1]: Leaving directory '/home/jklou/jkl/fs/freeswitch-1.8.2'
	Makefile:3729: recipe for target 'install' failed
	make: *** [install] Error 2

那就安装 libsndfile-dev 呗

	sudo apt-get install libsndfile-dev 

还不行，那就moduels.conf 里面注释 #formats/mod_sndfile，但是这样会使得声音模块有点问题，换另外的方式，下载libsnfile

	http://www.mega-nerd.com/libsndfile/files/libsndfile-1.0.28.tar.gz

	tar -zxcf libsndfile-1.0.28.tar.gz
	cd libsndfile-1.0.28
	./configure
	make
	sudo make install
	sudo cp /usr/local/lib/pkgconfig/sndfile.pc /usr/lib/pkgconfig/ 	

继续重新

	configure
	sudo make install

然后就成功了

这里其实我不清楚是apt-get install libsndfile-dev 有问题，还是我apt-get install 后没有重新configure 的问题，我猜应该是后者

	.=======================================================================================================.
	|       _                            _    ____ _             ____                                       |
	|      / \   _ __  _ __  _   _  __ _| |  / ___| |_   _  ___ / ___|___  _ __                             |
	|     / _ \ | '_ \| '_ \| | | |/ _` | | | |   | | | | |/ _ \ |   / _ \| '_ \                            |
	|    / ___ \| | | | | | | |_| | (_| | | | |___| | |_| |  __/ |__| (_) | | | |                           |
	|   /_/   \_\_| |_|_| |_|\__,_|\__,_|_|  \____|_|\__,_|\___|\____\___/|_| |_|                           |
	|                                                                                                       |
	|    ____ _____ ____    ____             __                                                             |
	|   |  _ \_   _/ ___|  / ___|___  _ __  / _| ___ _ __ ___ _ __   ___ ___                                |
	|   | |_) || || |     | |   / _ \| '_ \| |_ / _ \ '__/ _ \ '_ \ / __/ _ \                               |
	|   |  _ < | || |___  | |__| (_) | | | |  _|  __/ | |  __/ | | | (_|  __/                               |
	|   |_| \_\|_| \____|  \____\___/|_| |_|_|  \___|_|  \___|_| |_|\___\___|                               |
	|                                                                                                       |
	|     ____ _             ____                                                                           |
	|    / ___| |_   _  ___ / ___|___  _ __         ___ ___  _ __ ___                                       |
	|   | |   | | | | |/ _ \ |   / _ \| '_ \       / __/ _ \| '_ ` _ \                                      |
	|   | |___| | |_| |  __/ |__| (_) | | | |  _  | (_| (_) | | | | | |                                     |
	|    \____|_|\__,_|\___|\____\___/|_| |_| (_)  \___\___/|_| |_| |_|                                     |
	|                                                                                                       |
	.=======================================================================================================.

#### 安装声音文件

在后面的例子中我们需要一些声音文件。声音文件有两种，一种是提示音，用于通话期间的语音提示，如
VoiceMail的提示音，支持TTS功能的提示音等；另一种是音乐，用于在Hold状态时播放，即所谓的Music on
Hold（MOH） [7] 。

在Windows系统上，这些声音文件是默认安装的。而在Linux或Mac上安装这些声音文件也异常简单。你只需在
源代码目录中执行：

	make sounds-install
	make moh-install

安装过程中将自动从files.freeswitch.org下载相关的语音包，并解压缩到相关的安装路径中（默认安装
在/usr/local/freeswitch/sounds下）。

另外，FreeSWITCH支持8kHz、16kHz、32kHz及48kHz的语音 [8] 。与上面的声音文件相对应的高清声音文件可
以选择安装。如以下命令安装16kHz的声音文件：

	make cd-sounds-install
	make cd-moh-install