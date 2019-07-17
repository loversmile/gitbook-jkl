## 设计模式--工厂模式--工厂方法模式

工厂模式，顾名思义就是通过工厂来创建对象，这样我们在创建对象时不会对客户端暴露创建逻辑，并且是通过使用一个共同的接口来指向新创建的对象。

工厂模式作为一种创建模式，一般在创建复杂对象时，考虑使用；在创建简单对象时，建议直接new完成一个实例对象的创建。

### 工厂方法模式
**主要特点**：定义一个创建的对象的接口，其子类去具体实现这个接口以完成具体的创建工作。如果需要增加新的产品类，只需要扩展一个相应的工厂类即可。

**缺点**：产品类数据较多时，需要实现大量的工厂类，这样就增加了代码量

	#include <iostream>
	
	using namespace std;
	
    /* 抽象手机类 */
	class Phone {
	public:
	    virtual const string &type() = 0;
	};
	
	/* 华为手机类，继承手机类 */
	class HuaweiPhone: public Phone {
	public:
	    HuaweiPhone():Phone(),str_type("huawei"){}
	    const string &type()
	    {   
	        cout << str_type.data() << endl;
	        return str_type;
	    }
	private:
	    string str_type;
	};
	
    /* 小米手机类，继承手机类 */
	class XiaomiPhone: public Phone {
	public:
	    XiaomiPhone(): Phone(),str_type("xiaomi"){}
	    const string &type()
	    {   
	        cout << str_type.data() << endl;
	        return str_type;
	    }
	private:
	    string str_type;
	};
	
	/* 抽象工厂类 */
	class PhoneFactory {
	public:
	    virtual Phone* createPhone() = 0;
	};
	
	/* 华为手机工厂 */
	class HuaweiPhoneFactory: public PhoneFactory{
	public:
	    Phone *createPhone()
	    {
	        return new HuaweiPhone();
	    }
	};
	
	/* 小米手机工厂 */
	class XiaomiPhoneFactory: public PhoneFactory{
	public:
	    Phone *createPhone()
	    {
	        return new XiaomiPhone();
	    }
	};
	
	int main()
	{
	    PhoneFactory *factory_huawei = new HuaweiPhoneFactory();
	    PhoneFactory *factory_xiaomi = new XiaomiPhoneFactory();
	
	    Phone *huawei = factory_huawei->createPhone();
	    Phone *xiaomi = factory_xiaomi->createPhone();
	
	    huawei->type();
	    xiaomi->type();
	
	    delete factory_huawei;
	    delete factory_xiaomi;
	    factory_huawei = NULL;
	    factory_xiaomi = NULL;
	    delete huawei;
	    delete xiaomi;
	    huawei = NULL;
	    xiaomi = NULL;
	    return 0;
	}

上面这段代码使用工厂方法模式实现了对小米手机对象和华为手机对象的创建，先实现PhoneFactory这个类的对象，然后使用各自的子工厂类，然后调用factory对象中的方法 通过不同的工厂调用方法createPhone来创建不同的手机对象。