## 设计模式--单例模式--懒汉模式

单例模式顾名思义，保证一个类仅可以有一个实例化对象，并且提供一个可以访问它的全局接口。实现单例模式必须注意一下几点：
- 单例类只能由一个实例化对象。
- 单例类必须自己提供一个实例化对象。
- 单例类必须提供一个可以访问唯一实例化对象的接口。

单例模式分为懒汉和饿汉两种实现方式。

### 懒汉单例模式

对于懒汉式，就是在getInstance方法中增加一个if判断，判断当前对象是否为null，如果为null，则创建实例，比饿汉式多了一个判断。

简单的来说就是时间换空间，与饿汉式正好相反

#### 非线程安全的懒汉单例模式

	#include <iostream>
	using namespace std;
	
	class Singleton
	{
	public:
	    static Singleton* getInstance();
	private:
	    Singleton(){} //make construct private
	
	    static Singleton* p_singleton;
	};
	
	Singleton* Singleton::p_singleton = NULL;
	Singleton* Singleton::getInstance()
	{
	    if (p_singleton == NULL)
	    {
	        p_singleton = new Singleton();
	    }
	    return p_singleton;
	}
	
	
	int main()
	{
	    Singleton *s1 = Singleton::getInstance();
	    Singleton *s2 = Singleton::getInstance();
	    cout << (s1 == s2) << endl;
	    return 0;
	}

上述例子的输出，是1，证明s1和s2是相等的，但是如果多线程情况下，p_singleton在被创建之前，另外一个线程也进行了if判断，这样就会出现问题，所以需要有线程安全的单例模式。

#### 线程安全的懒汉单例模式
	
	#include <iostream>
	#include <mutex>
	using namespace std;
	
	std::mutex mt;
	
	class Singleton
	{
	public:
	    static Singleton* getInstance();
	private:
	    Singleton(){} //make construct private
	
	    static Singleton* p_singleton;
	};
	
	Singleton* Singleton::p_singleton = NULL;
	Singleton* Singleton::getInstance()
	{
	    mt.lock();
	    if (p_singleton == NULL)
	    {
	        p_singleton = new Singleton();
	    }
	    return p_singleton;
	    mt.unlock();
	}
	
	
	int main()
	{
	    Singleton *s1 = Singleton::getInstance();
	    Singleton *s2 = Singleton::getInstance();
	    cout << (s1 == s2) << endl;
	    return 0;
	}

所谓的线程安全，就是在判断之前增加了锁的机制，确保当前无人使用。

### 总结

至于懒汉和饿汉哪个更好，就看使用的时候需要的时间还是空间。