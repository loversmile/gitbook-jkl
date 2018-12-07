## java Logger 简单入门

### 日志记录器

应用程序可以使用多个日志记录器，它们使用类似包名的这种具有层次结构的名字，例如：com.mycompany.myapp。

可以调用 getLogger 方法创建或获取记录器：

	private static final Logger myLogger = Logger.getLogger("com.mycompany.myapp");

**提示**：未被任何变量引用的日志记录器可能会被垃圾回收。为了防止这种情况发生，要
像上面的例子中一样, 用一个静态变量存储日志记录器的一个引用 。

与包名类似，日志记录器名也具有层次结构。事实上，与包名相比，日志记录器的层次性更强。对于包来说，一个包的名字与其父包的名字之间没有语义关系，但是日志记录器的父与子之间将共享某些属性。例如，如果对 com.mycompany 日志记录器设置了日志级别,它的子记录器也会继承这个级别，通常，有以下 7 个日志记录器级别 ：

- SEVERE
- WARNING
- INFO
- CONFIG
- FINE
- FINER
- FINEST
 
在默认情况下只记录前三个级别，也可以设置其他的级別。例如：

	logger.setLevel(Level.FINE);

现在，FINE 和更高级别的记录都可以记录下来 。

另外，还可以使用 Level.ALL 开启所有级别的记录，或者使用 Level.OFF 关闭所有级别的记录。

对于所有的级别有下面几种记录方法：

	logger.warning(message);
	logger.fine(message);

同时，还可以使用 log 方法指定级别，例如 ：

	logger.log(Level.FINE, message) ;

**提示**： 默认的日志配置记录了 INFO 或更高级别的所有记录, 因此,应该使用 CONFIG, FINE, FINER 和 FINEST 级别来记录那些有助于诊断,但对于程序员又没有太大意义的调试信息。

##### 日志记录器的作用

个人理解，日志记录器是用来区分不同日志类型的，日志记录器返回一个对象，对对象设置日志文件地址，输入格式，等级等，这样使用不同的日志记录器就有不同的输出。

### 输出到文件

在默认情况下日志记录器将记录发送到 ConsoleHandler 中，并由它输出到 System.err流中。特别是日志记录器还会将记录发送到父处理器中 ， 而最终的处理器（命名为 “ ”）有一个 ConsoleHandler。

与日志记录器一样，处理器也有日志记录级别。对于一个要被记录的日志记录，它的日志记录级别必须高于日志记录器和处理器的阈值。日志管理器配置文件设置的默认控制台处理器的日志记录级别为

	java.util.logging.ConsoleHandler.level = INFO

要想记录 FINE 级别的日志，就必须修改配置文件中的默认日志记录级别和处理器级别 。
另外， 还可以绕过配置文件， 安装自己的处理器 。

	Logger logger = Logger.getLogger("com.mycompany.myapp");
	logger.setLevel (Level.FINE);
	logger.setUseParentHandlers(false);
	Handler handler = new ConsoleHandler( );
	handler.setLevel(Level.FINE);
	logger.addHandler(handler);

Logger记录的日志消息会被转发到已注册的Handler对象，上面提到了，默认是ConsoleHandler。

handler对象可以将消息发送到：控制台，文件，网络等等。

Handler类(抽象类)：主要用于转发日志消息

　　Hanlder类下有2个子类：MemoryHandler、StreamHandler。

　　StreamHandler下有3个子类：ConsoleHandler(将日志消息打印到控制台)、FileHandler(将日志消息输出到文件)、SocketHandler(将日志发送到网络中的某个主机)

我们可以使用Formatter 类来指定日志输出的格式。

Formatter类(抽象类)：主要用于格式化日志记录消息。

　　有2个子类：SimpleFormatter(纯文本形式), XmlFormatter(XML形式)

我们使用 FileHandler 和 Formatter类的子类对象就可以写日志到本地了，如下面的例子：

	import java.util.logging.*;
	import java.io.IOException;
	
	
	public class TestLogger  {
	    public static void main(String[] args) throws IOException {
			/* 创建两个日志记录器 */
	        Logger logger = Logger.getLogger("com.mycompany.myapp");
	        Logger logger2 = Logger.getLogger("com.mycompany.mylog");

			/* 创建两个 FileHandler 对象，指定输出文件 */
	        FileHandler fileHandler = new FileHandler("E:\\java\\javatest\\log.txt", true);
	        FileHandler fileHandler2 = new FileHandler("E:\\java\\javatest\\log.xml", true);
	
			/* 创建纯文本格式对象和xml格式对象的 Formatter */
	        SimpleFormatter sf = new SimpleFormatter();
	        XMLFormatter xlf = new XMLFormatter();
	
			/* 设定输出到fileHandler 的为纯文本格式，输出到 fileHandler2 的为 xml 格式 */
	        fileHandler.setFormatter(sf);
	        fileHandler2.setFormatter(xlf);
	
			/* 日志记录器 logger 使用 fileHandler, logger2 使用 fileHandler2 */
	        logger.addHandler(fileHandler);
	        logger2.addHandler(fileHandler2);

			/* 下面的日志以纯文本格式输出到 log.txt，FINE以上的级别都会输出 */	
	        logger.setLevel(Level.FINE);
	        logger.warning("this is warning");
	        logger.config("this is config");
	        logger.info("this is info");

			/* 下面的日志以 XML 格式输出到 log.xml，因为没有设定日志登记，默认INFO，所以config不会输出 */		
	        logger2.info("this is info");
	        logger2.config("this is config");
	        logger2.warning("this is warning");
	    }
	}

另外在控制台执行代码的时候，**logger.warning("this is warning");logger.info("this is info");logger2.info("this is info");logger2.warning("this is warning");**还是会在控制台输出，因为没有清理掉ConsoleHandler。

关于SocketHandler，以下是构造方法：

	public SocketHandler(String host,
	                     int port)
	              throws IOException使用指定的主机和端口构造一个SocketHandler 。 所述SocketHandler是基于不同的是给定的目标主机和端口参数用于LogManager个属性（或它们的默认值），被配置。 如果host参数为空，而不是空String，则使用localhost。 
	参数 
	host - 目标主机。 
	port - 目标端口。 
	异常 
	IllegalArgumentException - 如果主机或端口无效。 
	IOException - 如果我们无法连接到目标主机和端口。 

可以自行测试。
