## 设计模式--单例模式--饿汉模式

单例模式顾名思义，保证一个类仅可以有一个实例化对象，并且提供一个可以访问它的全局接口。实现单例模式必须注意一下几点：
- 单例类只能由一个实例化对象。
- 单例类必须自己提供一个实例化对象。
- 单例类必须提供一个可以访问唯一实例化对象的接口。

单例模式分为懒汉和饿汉两种实现方式。

### 饿汉单例模式

饿汉式是直接创建出类的实例化，比较简单粗暴。

简单来说就是空间换时间，因为上来就实例化一个对象，占用了内存，（也不管你用还是不用）

	#include <iostream>
	using namespace std;
	
	class Singleton
	{
	public:
	    static Singleton* getInstance();
	private:
	    Singleton(){}
	    static Singleton* p_singleton;
	};
	
	Singleton* Singleton::p_singleton = new Singleton();
	Singleton* Singleton::getInstance()
	{
	    return p_singleton;
	}
	
	int main()
	{
	    Singleton *s1 = Singleton::getInstance();
	    Singleton *s2 = Singleton::getInstance();
	    cout << (s1 == s2) << endl;
	    return 0;
	}

上述代码实现了一个饿汉的单例模式，执行结果是1，即s1 和 s2是相等的。