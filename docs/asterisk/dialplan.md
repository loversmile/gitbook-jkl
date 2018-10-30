## 简单学习DialPlan

开始学习 Dialplan !  

Asterisk 的拔号方案配置文件在：**/etc/asterisk/extensions.conf**

Dialplan 语法大概可分为如下几部分：

### context (上下文)：

它把拨号方案的不同部分进行分离，免得彼此交织在一起。简单来说，就是 extensions.conf配置文件中[incoming]，就是中括号这部分，用来区分不同的拔号方案，从[incoming]开始直到下个中括号开始那么这块就是一个拔号方案。

###Extension ：

在每一个context 内，可以定义一个或者多个extension，extension 是Asterisk 要执行的指令，由来电或者通道上所拨数字来触发。

一个完整的extension 由三部分组成：

* Extension 的名字或者号码
* Priority（每个extension 可以有多个步骤，步骤的编号称作Priority）
* 应用(或者命令)，针对呼叫完成一些动作

这三个部分用英文逗号分开，如：

	exten => name,priority,application()

### Priority：

每个extension 都可以有多个步骤，称作priorities。 如：

	exten => 123,1,Answer( )
	exten => 123,2,Hangup( )

注：看下例子：

	exten => 123,1,Answer( )
	exten => 123,n,do something
	exten => 123,n,do something else
	exten => 123,n,do one last thing
	exten => 123,n,Hangup( )


其中 Priority 是 n ，表示“下一个”的意思。  
每次Asterisk 遇n 这个priority 的时候，就取出前一个priority的编号加上1

### Application：

它是拔号方案中所要执行的动作，如上面例子中的 Answer()、Hangup()

了解上面基本的 Dialplan 语法后，看了几个 Dialplan 实例：

	exten=>_X.,1,Dial(SIP/${EXTEN})       ;_X.模式匹配，匹配任何电话号码;${EXTEN}通道变量，${EXTEN}为所拨的数字
	exten=>_X.,2,Playback(vm-nobodyavail) ;被叫无应答，Dial()会继续到该 extension 的下一个priority
	exten=>_X.,3,Hangup()                 ;挂断
	exten=>_X.,102,Playback(tt-allbusy)   ;如果被叫通道忙，Dial()将转到priority n+101，（其中的 n 是 Dial( )被调用的priority）
	exten=>_X.,103,Hangup()               ;挂断

例二：

	[incoming]
	exten => s,1,Answer( )
	exten => s,2,Playback(hello-world)
	exten => s,3,Hangup( )

现在就遇到了问题，在第一个例子中创建了两个 SIP 分机，互拔响铃通话 ok没啥问题，但第二个实例中 Extension 不是一个分机，这个用分机互拔不通 失败！这个究竟要怎么样发起呢？

**Extension等于电话号码吗?**是的,通常情况下这是对的. 这里有三种类似的extension: 文字的,预定义的,模式匹配的. 
Litteral分机名字可以包含:数字[0-9], 字母A,B,C,D(一些硬电话有这些字母)或者所有字母(a-z).这些分机的名字是区分大小写的吗?这是分情况的.当Asterisk在一个被定义的 context里必须匹配一个用户拨打的分机号时,分机名字是区分大小写的. 

这里有一些预定义的分机名字: 

	-i -Invalid(无效的输入) 
	-s -Start(开始) 
	-h -Hangup(挂断) 
	-t -Timeout(超时) 
	-T -Absolute Timeout(精确的超时) 
	-o - Operator(操作员) 

现在有点头续了，如下图：

	exten => _X.,1,Dial(SIP/${EXTEN},12)
	exten => t,1,Playback(vm-goodbye)
	exten => t,2,Hangup()


当呼叫的分机超过12秒后(也就是呼叫超时)，就会自动执行该上下文中 Extension 名字为 t 的 Extension

还有个疑问就是 预定义的 s 是怎么用的呢？ 网上有很多这个例子，如下：

	[demo]
	exten => s,1,Answer()
	exten => s,2,Background(../ch/welcome)

这个是要怎么发起呢？ 还是这个只能用来被调用的，像Goto() 这样来跳转！



### 宏指令

宏指令和 上下文 基本一样，宏指令的定义如下：

	[macro-voicemail]
	exten =>s, 1, Dial(${ARG1}, 10, r)
	exten =>s, 2, voicemail (u${MACRO_EXTEN}@default)
	exten =>s, 102, voicemail (b${MACRO_EXTEN}@default)

说明：

1. 宏指令名字的定义：macro-name 必须要以 macro- 开头。
2. 宏指令中的 extension 只能用 s 表示，这下应该就能明白上篇例子中的s了

既然用到了宏，那么其中的像 分机 等就都需要用变量来表示了，宏中预定义变量：

	${MACRO_CONTEXT} ;这个被调用的宏中，初始的上下文
	${MACRO_EXTEN} ;这个被调用的宏中，初始的extension
	${MACRO_PRIORITY} ;这个被调用的宏中，初始的优先级
	${ARGn};传递到宏指令的第n个变量。如第一个变量 ${ARG1}

然后就是在 Dialplan 中调用我们的宏：
	
	exten =>101,1,Macro(voicemail, ${JOHN})
	exten =>102,1, Macro(voicemail, ${JANE})
	exten =>103,1, Macro(voicemail, ${JACK})

使用 Macro() 应用 ：

1. 其中第一个参数就是 宏的名字但不需要macro-前缀，
2. 然后后面一个 ${JOHN} 这个参数就是宏中的 ${ARG1} 
3. 宏中的 ${MACRO_EXTEN} 那就是 exten =>101,1,Macro(voicemail, ${JOHN}) 这个里面的101 


### AstDB

Asterisk数据库使用的是 Berkeley 数据库。

在数据库中存储数据，使用 set() 应用和 DB()函数，例：
	
	exten => 801,1,set(${DB(test/count) = 1})

说明，里面test相当于表名，count相当于test表中的字段名。

数据库中数据的删除，使用DBdel()应用;DBdeltree()应用删除键值分组（删除表）。例：

	Exten =>500, 1, DBdel(test/count)
	
	Exten =>457, 1, DBdeltree(test) ;删除 test 表

在CLI中操作数据库，可用 database 命令。 


### 几个例子
#### 语音邮箱的配置文件：

/etc/asterisk/voicemail.conf 格式如下：

	[test]
	edwin=>1234,edwin chen, edwin@testmail.com 
	;edwin:用户名，1234:密码，edwinchen :全名 ，最后一个mail地址?

然后要使用语音邮箱就要用到 Voicemail() 应用，格式如下：

Voicemail(mailbox@context)   mailbox就是用户名，context就是voicemail.conf中的上下文，那么要使用上面例子中的语音邮箱在 Dialplan 中这样写：

	exten => 802,1,Dial(SIP/802,12)
	exten => t,1,Voicemail(edwin@vedemo)
	 

当拔打 802 分机超时，就会转入到 edwin 语音邮箱中了。

	我用的是 asterisk 1.6 的，记得重载 Dialplan 和 voicemail.conf ：
	
	在CLI下重载 Dialplan：dialplan reload
	
	在CLI下重载 voicemail：voicemail reload 

参考：http://blog.csdn.net/huichengongzi/article/details/4834451 


#### 电话会议
电话会议就需要使用到 Meetme() 应用。 Asterisk电话会议的特点：

1. 可创建密码保护会议
2. 会议管理（静音、断开、只允许一人讲话）
3. 动态 或 静态会议

创建一个会议需要在 meetme.conf 配置文件中设置[Asterisk 1.6版本]：

	conf => confno[,pin][,adminpin]  ;后面依次为 会议室号、进入会议室的密码、管理员密码。

然后在 Dialplan 中使用该会议室：

	exten => 500,1,MeetMe([confno][,[options][,pin]]) ;如:
	exten => 500,1,MeetMe(600,i,12345) 其中参数依次为：会议室号、i表示通知用户进入或退出会议室、密码

