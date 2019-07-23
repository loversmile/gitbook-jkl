## 设计模式--外观模式

### 外观模式

**外观模式**：为子系统中的一组接口定义一个一致的界面；外观模式提供一个高层的接口，这个接口使得这一子系统更加容易被使用；对于复杂的系统，系统为客户端提供一个简单的接口，把负责的实现过程封装起来，客户端不需要连接系统内部的细节。

以下情形建议考虑外观模式：

- 设计初期阶段，应有意识的将不同层分离，层与层之间建立外观模式。
- 开发阶段，子系统越来越复杂，使用外观模式提供一个简单的调用接口。
- 一个系统可能已经非常难易维护和扩展，但又包含了非常重要的功能，可以为其开发一个外观类，使得新系统可以方便的与其交互。

**优点**：

- 实现了子系统与客户端之间的松耦合关系。
- 客户端屏蔽了子系统组件，减少了客户端所需要处理的对象数据，使得子系统使用起来更方便容易。
- 更好的划分了设计层次，对于后期维护更加的容易。


	#include <iostream>
	
	using namespace std;
	
	class Control
	{
	public:
	    virtual void start() = 0;
	    virtual void shutdown()=  0;
	};
	
	class Host: public Control
	{
	public:
	    void start()
	    {
	        cout << "Host start" << endl;
	    }
	    void shutdown()
	    {
	        cout << "Host shotdown" << endl;
	    }
	};
	
	class Display: public Control
	{
	public:
	    void start()
	    {
	        cout << "Display start" << endl;
	    }
	    void shutdown()
	    {
	        cout << "Display shutdown" << endl;
	    }
	};
	
	class Computer
	{
	public:
	    void start()
	    {
	        h_host.start();
	        d_display.start();
	        cout << "Computer Start" << endl;
	    }
	    void shutdown()
	    {
	        h_host.shutdown();
	        d_display.shutdown();
	        cout << "Computer Shutdown" << endl;
	    }
	private:
	    Host h_host;
	    Display d_display;
	};
	
	int main()
	{
	    Computer *com = new Computer();
	
	    com->start();
	    com->shutdown();
	
	    delete com;
	    com = NULL;
	}

上面是例子就是说，对于电脑来说，显示器和主机都要开和关，这个时候我们只要一个关电脑的操作就是关掉了所有设备，这个电脑就是所谓的外观。当然上面的例子并不是那么严谨因为一般关机不会连显示器一起关了，上面的例子把显示器改成CPU、显卡、硬盘等应该是更好的。