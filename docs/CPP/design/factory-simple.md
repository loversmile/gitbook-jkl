## 设计模式--工厂模式--简单工厂模式

工厂模式，顾名思义就是通过工厂来创建对象，这样我们在创建对象时不会对客户端暴露创建逻辑，并且是通过使用一个共同的接口来指向新创建的对象。

工厂模式作为一种创建模式，一般在创建复杂对象时，考虑使用；在创建简单对象时，建议直接new完成一个实例对象的创建。

### 简单工厂模式

**主要特点**：需要在工厂类中做判断，从而创造相应的产品，当增加新产品时（新的类），需要修改工厂类。使用简单工厂模式，我们只需要知道具体的产品型号就可以创建一个产品。

**缺点**：工厂类集中了所有产品类的创建逻辑，如果产品量较大，会使得工厂类变的非常臃肿。

	#include <iostream>
	using namespace std;
	
	typedef enum {
	    PhoneHuawei,
	    PhoneXiaomi,
	    PhoneAll
	} PhoneType;
	
	/* 抽象手机类 */
	class Phone {
	public:
	    virtual const string &type() = 0;
	};
	
	/* 华为手机类，继承手机类 */
	class HuaweiPhone : public Phone{
	public:
	    HuaweiPhone(): Phone(),str_type("Huawei"){}
	    const string &type() 
	    {     
	        cout << str_type.data() << endl;
	        return str_type;
	    }    
	private:
	    string str_type;
	};           
	
	/* 小米手机类，继承手机类 */
	class XiaomiPhone : public Phone { 
	public:
	    XiaomiPhone(): Phone(),str_type("Xiaomi"){}
	    const string &type()
	    {
	        cout << str_type.data() << endl;
	        return str_type;
	    }
	private:
	    string str_type;
	};
	
	/* 工厂类 */
	class PhoneFactory {
	public:
	    Phone *createPhone(PhoneType type)
	    {
	        switch(type)
	        {
	        case PhoneHuawei:
	            return new HuaweiPhone();
	        case PhoneXiaomi:
	            return new XiaomiPhone();
	        default:
	            return NULL;
	        }
	    }
	};
	
	
	int main()
	{
	    PhoneFactory *factory = new PhoneFactory();
	    Phone *huawei = factory->createPhone(PhoneHuawei);
	    Phone *xiaomi = factory->createPhone(PhoneXiaomi);
	
	    huawei->type();
	    xiaomi->type();
	
	    delete huawei;
	    delete xiaomi;
	    delete factory;
	    huawei = NULL;
	    xiaomi = NULL;
	    factory = NULL;
	    return 0;
	}

上面这段代码使用简单工厂类实现了对小米手机对象和华为手机对象的创建，先实现PhoneFactory这个类的对象，然后调用factory对象中的方法 createPhone根据不同类型来创建不同的手机对象。