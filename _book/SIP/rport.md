## rport机制讲解
### 1 方案描述

  获得IP地址是在Via头中带上received参数。为了得到端口信息，也参考了这种方式，即在Via头中带上rport属性来指明端口信息。
当在客户端和服务器之间是NAT的时候，请求可能会在NAT中创建（或刷新）一个绑定，为了让客户端收到响应信息，在事务处理的过程中这个绑定必须保持存在。大多数的NAT绑定有超过1分钟的超时时间，这超过了non-INVITE事务的持续时间，因而对non-INVITE事务的请求的响应只能在绑定存在的时候存在。INVITE事务倒是不存在这个问题。  
为了保持这个绑定，客户端应该在每隔20s左右重发INVITE请求，这种重发机制需要发生在收到一个临时的响应后。
当然刚才所说的大概1分钟的超时时间也不是确定的，有时候会比这长，此时重发机制可以发慢一点，否则，可以发快一点。这些问题可参考RFC3489。
如果是支持rport机制的服务器，它需要在接收到的请求中检查Via头是否包含一个没有值的rport参数。如果有，它需要在回应中带上rport的值，这与received的处理类似。  
为了穿越对称性的对称性的NAT，响应需要发送到相同的IP地址和端口。当服务器在多端口或接口的请求上监听请求时，它必须记住请求是从何处发的。对一个稳定的Proxy，在一个传输的持续时间中，记住这些东西是没有问题的。但是对于不稳定的Proxy，它不存储请求和响应中的状态信息，为了达到本规范的要求，它需要将地址和端口信息加密到Via头字段中，在响应信息到达的时候，它能提取加密的信息并将它放到响应中。
rport机制需要终端支持该种机制，因此应用情况比较受限。但是在笔者的应用场景（呼叫中心）中，主要要解决的问题是坐席能在NAT环境中穿越，给服务器发送信息。因为坐席所使用的SIP软电话是本公司开发的，所以可以保证是支持rport和received的。

### 2 实例
下面举一个发送REGISTER信息的实例，在请求信息的Via头中包含了没有值的rport参数，如下所示：

	REGISTER sip:124.40.120.188:5060 SIP/2.0
	Via:SIP/2.0/UDP 124.42.4.203:15500;branch=z9hG4bK-d8754z-1049ed261d2e643d-1---d8754z-;rport
	Max-Forwards:70
	Contact:<sip:19988888888@192.168.2.65:12344;rinstance=7cd1c532e92fdb0e>;expires =
	To:"19988888888"<sip:19988888888@124.40.120.188:5060>
	From:"19988888888"<sip:19988888888@124.40.120.188:5060>;tag=203ba359
	Call-ID:Yzc4N2IwMzY5OWU4MTdkMzY0NWY4OWU3NjMzNmJiM2U.
	CSeq:1 REGISTER
	Allow:INVITE,ACK,CANCEL,OPTIONS,BYE,REFER,NOTIFY,MESSAGE,SUBSCRIBE,INFO
	User-Agent:eyeBeam release 1105a stamp 56793
	Content-Length: 

发送到的服务器支持rport机制，它看到请求中的rport后，将通过分析UDP包信息得到的的NAT的公网地址（124.42.4.203）和端口信息（15500）分别作为received和rport属性带给客户端：

	SIP/2.0 200 OK
	Via:SIP/2.0/UDP 124.42.4.203:15500;branch=z9hG4bK-d8754z-1049ed261d2e643d-1---d8754z-;rport=15500;received=124.42.4.203 
	From:"19988888888"<sip:19988888888@124.40.120.188:5060>;tag=203ba359
	To:"19988888888"<sip:19988888888@124.40.120.188:5060>;tag=0005-058-7d6dc90516ae2e21
	Call-ID:Yzc4N2IwMzY5OWU4MTdkMzY0NWY4OWU3NjMzNmJiM2U.
	CSeq:4 REGISTER
	Allow:INVITE,ACK,OPTIONS,BYE,CANCEL,REGISTER,INFO,UPDATE,PRACK,REFER,SUBSCRIBE,NOTIFY,MESSAGE
	Contact:<sip:124.40.120.188:5060> 
	Content-Length: 

客户端在得到响应信息后，知道了所使用的公网地址和端口，在而后定期重发的REGISTER信息中，Contact变换成124.42.4.203: 15500，例如新发的REGISTER信息变为：

	REGISTER sip:124.40.120.188:5060 SIP/2.0
	Via:SIP/2.0/UDP 124.42.4.203:15500;branch=z9hG4bK-d8754z-1049ed261d2e643d-1---d8754z-;rport
	Max-Forwards:70
	Contact:<sip:19988888888@124.42.4.203:15500;rinstance=7cd1c532e92fdb0e>;expires= 
	To:"19988888888"<sip:19988888888@124.40.120.188:5060> 
	From:"19988888888"<sip:19988888888@124.40.120.188:5060>;tag=203ba359
	Call-ID:Yzc4N2IwMzY5OWU4MTdkMzY0NWY4OWU3NjMzNmJiM2U.
	CSeq:2 REGISTER
	Allow:INVITE,ACK,CANCEL,OPTIONS,BYE,REFER,NOTIFY,MESSAGE,SUBSCRIBE,INFO
	User-Agent:eyeBeam release 1105a stamp 56793 
	Content-Length: 

**rport**方式主要是对sip信令中**Via**字头的扩展，不过同时也要求SIP Proxy支持该功能。  
NAT之后的sip client在发送请求的时候在via字头中添加rport字段，该消息经发出后路由到**SIPProxy**，SIP Proxy通过检查消息的源地址和Via字段中的地址，得知该client处于NAT之后，并且基于已有的rport，将消息的真实地址即公网上的地址通过received和rport字段返回给client端，这样client就知道自己真实的公网地址，可以解决信令穿越的问题。