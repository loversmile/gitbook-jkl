## 通过AMI接口远程控制Asterisk

### Manager API简介
Asterisk管理接口(AMI)允许外部客户端程连接到Asterisk实例，并且可以通过TCP/IP流发送命令和读取事件。在试图跟踪Asterisk内部电话状态进，它就显得特别有用。

在服务端和客户端之间，用的是一种简单的"key: value"格式的协议。消息中，以CR/LF为换行符，以空行(两个CR/LF)为数据包终结符。

### AMI协议
- 向Asterisk发送命令之前，必须先建立一个会话
- 会话建立之后，服务端和客户端之间，可以在任意时候发送数据包
- 客户端发给服务端的包，首行必须有一个”Action”关键字；服务端发给客户端的包，首行则必须有”Event”或”Response”关键字。
- 一个数据包里，命令行的顺序是无关紧要的，所以可以用你熟悉的顺序来组织
- 以CR/LF为换行符，CR/LF加空行(两个CR/LF)为包结束符，Asterisk收到包结束符时开始解析处理

### 包的类型

AMI的数据包类型，由首行里的关键字决定，目前有以下三种类型：

- Action：客户端发给Asterisk的包，请求执行某项特定的动作(Action)。客户端可执行的动作(可扩展的)，取决于Asterisk加载的模块。一次执行一条命令。Action包含需要执行的操作名称和完成操作所需要的参数。
- Response：Asterisk对Action的响应包。
- Event：由Asterisk核心或外围模块生成的事件信息，用于通知客户端状态变化，比如电话呼入，有人加入或退出会议室，等等。


### AMI配置

AMI的配置文件 /etc/asterisk/manager.conf。AMI缺省的监听端口为5038，主要需要配置的内容是分配个用户，并授予执行的权限，配置实例：

	[general]   
	enabled=yes
	port=7788
	bindaddr=0.0.0.0
	  
	[abcde123]
	secret=abcde123
	permit=0.0.0.0/0.0.0.0
	read=system,call,log,verbose,agent,command,user
	write=system,call,log,verbose,agent,command,user

配置项的详细内容，可参考http://www.asterisk.org/doxygen/trunk/Config_ami.html

配置完成后，可以在CLI下查看manager用信息，操作实例如下：

	jklou*CLI> manager show users
	
	username
	--------
	admin
	-------------------
	1 manager users configured.

AMI的命令列表可以通过在CLI下的manager show commands查看，我的环境里是这样的：

	jklou*CLI> manager show commands
	  Action           Privilege       Synopsis                                               
	  ------           ---------       --------                                               
	  WaitEvent        <none>          Wait for an event to occur.                            
	  QueueReset       <none>          Reset queue statistics.                                
	  QueueReload      <none>          Reload a queue, queues, or any sub-section of a queue o
	  QueueRule        <none>          Queue Rules.                                           
	  QueuePenalty     agent,all       Set the penalty for a queue member.                    
	  QueueLog         agent,all       Adds custom entry in queue_log.                        
	  QueuePause       agent,all       Makes a queue member temporarily unavailable.         
	  QueueRemove      agent,all       Remove interface from queue.                           
	  QueueAdd         agent,all       Add interface to queue.                                
	  QueueSummary     <none>          Show queue summary.                                    
	  QueueStatus      <none>          Show queue status.                                     
	  Queues           <none>          Queues.                                                
	  MuteAudio        system,all      Mute an audio stream                                   
	  MixMonitorMute   <none>          Mute / unMute a Mixmonitor recording.                  
	  VoicemailUsersL  call,reporting, List All Voicemail User Information.                   
	  PlayDTMF         call,all        Play DTMF signal on a specific channel.                
	  SKINNYshowline   system,reportin Show SKINNY line (text format).                        
	  SKINNYlines      system,reportin List SKINNY lines (text format).                       
	  SKINNYshowdevic  system,reportin Show SKINNY device (text format).                      
	  SKINNYdevices    system,reportin List SKINNY devices (text format).                     
	  IAXregistry      system,reportin Show IAX registrations.                                
	  IAXnetstats      system,reportin Show IAX Netstats.                                     
	  IAXpeerlist      system,reportin List IAX Peers.                                        
	  IAXpeers         system,reportin List IAX peers.                                        
	  AgentLogoff      agent,all       Sets an agent as no longer logged in.                  
	  Agents           agent,all       Lists agents and their status.                         
	  SIPnotify        system,all      Send a SIP notify.                                     
	  SIPshowregistry  system,reportin Show SIP registrations (text format).                  
	  SIPqualifypeer   system,reportin Qualify SIP peers.                                     
	  SIPshowpeer      system,reportin show SIP peer (text format).                           
	  SIPpeers         system,reportin List SIP peers (text format).                          
	  LocalOptimizeAw  system,call,all Optimize away a local channel when possible.           
	  AGI              agi,all         Add an AGI command to execute by Async AGI.            
	  UnpauseMonitor   call,all        Unpause monitoring of a channel.                       
	  PauseMonitor     call,all        Pause monitoring of a channel.                         
	  ChangeMonitor    call,all        Change monitoring filename of a channel.               
	  StopMonitor      call,all        Stop monitoring a channel.                             
	  Monitor          call,all        Monitor a channel.                                     
	  DBDelTree        system,all      Delete DB Tree.                                        
	  DBDel            system,all      Delete DB entry.                                       
	  DBPut            system,all      Put DB entry.                                          
	  DBGet            system,reportin Get DB Entry.                                          
	  Bridge           call,all        Bridge two channels already in the PBX.                
	  Park             call,all        Park a channel.                                        
	  ParkedCalls      <none>          List parked calls.                                     
	  ShowDialPlan     config,reportin Show dialplan contexts and extensions                  
	  AOCMessage       aoc,all         Generate an Advice of Charge message on a channel.     
	  ModuleCheck      system,all      Check if module is loaded.                             
	  ModuleLoad       system,all      Module management.                                     
	  CoreShowChannel  system,reportin List currently active channels.                        
	  Reload           system,config,a Send a reload event.                                   
	  CoreStatus       system,reportin Show PBX core status variables.                        
	  CoreSettings     system,reportin Show PBX core settings (version etc).                  
	  UserEvent        user,all        Send an arbitrary event.                               
	  UpdateConfig     config,all      Update basic configuration.                            
	  SendText         call,all        Send text message to channel.                          
	  ListCommands     <none>          List available manager commands.                       
	  MailboxCount     call,reporting, Check Mailbox Message Count.                           
	  MailboxStatus    call,reporting, Check mailbox.                                         
	  AbsoluteTimeout  system,call,all Set absolute timeout.                                  
	  ExtensionState   call,reporting, Check Extension Status.                                
	  Command          command,all     Execute Asterisk CLI Command.                          
	  Originate        originate,all   Originate a call.                                      
	  Atxfer           call,all        Attended transfer.                                     
	  Redirect         call,all        Redirect (transfer) a call.                            
	  ListCategories   config,all      List categories in configuration file.                 
	  CreateConfig     config,all      Creates an empty file in the configuration directory.  
	  Status           system,call,rep List channel status.                                   
	  GetConfigJSON    system,config,a Retrieve configuration (JSON format).                  
	  GetConfig        system,config,a Retrieve configuration.                                
	  Getvar           call,reporting, Gets a channel variable.                               
	  Setvar           call,all        Set a channel variable.                                
	  Ping             <none>          Keepalive command.                                     
	  Hangup           system,call,all Hangup channel.                                        
	  Challenge        <none>          Generate Challenge for MD5 Auth.                       
	  Login            <none>          Login Manager.                                         
	  Logoff           <none>          Logoff Manager.                                        
	  Events           <none>          Control Event Flow.                                    
	  DataGet          <none>          Retrieve the data api tree.

### 连接实例

如果我们没有AMI客户端，当配置完用户之后，可以通过telnet连接AMI监听的端口执行操作：

	jklou@ubuntu:~$# telnet 127.0.0.1 7788
	Trying 127.0.0.1...
	Connected to 127.0.0.1.
	Escape character is '^]'.
	Asterisk Call Manager/1.1

这里就开始等待用户输入了，缺省情况下，如果30秒用户不输入，连接就会断开。

### 登录

	action: login
	username: abcde123
	secret: abcde123
	
	Response: Success
	Message: Authentication accepted

注意：关键字后要加个冒号，冒号后要加个空格，输入完毕后，要按**两次回车**(两个CR/LF为命令结束符)。

### 查看可执行的AMI命令

登录后，可以查看可以使用的AMI命令

	action: listcommands
	
	Response: Success
	WaitEvent: Wait for an event to occur.  (Priv: <none>)
	QueueReset: Reset queue statistics.  (Priv: <none>)
	QueueReload: Reload a queue, queues, or any sub-section of a queue or queues. (Priv: <none>)
	QueueRule: Queue Rules.  (Priv: <none>)
	......

命令比较多，就不一一列举了

### 登出

	action: logoff
	
	Response: Goodbye
	Message: Thanks for all the fish.
	
	Connection closed by foreign host.
