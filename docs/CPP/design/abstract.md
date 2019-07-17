## 设计模式--工厂模式--抽象工厂模式

工厂模式，顾名思义就是通过工厂来创建对象，这样我们在创建对象时不会对客户端暴露创建逻辑，并且是通过使用一个共同的接口来指向新创建的对象。

工厂模式作为一种创建模式，一般在创建复杂对象时，考虑使用；在创建简单对象时，建议直接new完成一个实例对象的创建。

### 抽象工厂模式

**主要特点**：抽象工厂模式提供创建一系列相关或相互依赖对象的接口，而无需指定它们具体的类。
当存在多个产品系列，而客户端只使用一个系列的产品时，可以考虑使用抽象工厂模式。

**缺点**：当增加一个新系列的产品时，不仅需要现实具体的产品类，还需要增加一个新的创建接口，扩展相对困难。

	#include <iostream>
	
	using namespace std;
	
	/* 抽象SUV */
	class SUV
	{
	public:
	    virtual const string& type() = 0;
	};
	
	class WhiteSUV: public SUV
	{
	public:
	    WhiteSUV(): SUV(), str_type("White SUV"){}
	    const string& type()
	    {
	        cout << str_type.data() << endl;
	        return str_type;
	    }
	private:
	    string str_type;
	};
	
	class BlackSUV: public SUV
	{
	public:
	    BlackSUV(): SUV(), str_type("Black SUV"){}
	    const string& type()
	    {
	        cout << str_type.data() << endl;
	        return str_type;
	    }
	private:
	    string str_type;
	};
	
	/* 抽象MPV */
	class MPV
	{
	public:
	    virtual const string& type() = 0;
	};
	
	class WhiteMPV: public MPV
	{
	public:
	    WhiteMPV(): MPV(), str_type("White MPV"){}
	    const string& type()
	    {
	        cout << str_type.data() << endl;
	        return str_type;
	    }
	private:
	    string str_type;
	};
	
	class BlackMPV: public MPV
	{
	public:
	    BlackMPV(): MPV(), str_type("Black MPV"){}
	    const string& type()
	    {
	        cout << str_type.data() << endl;
	        return str_type;
	    }
	private:
	    string str_type;
	};
	
	/* 抽象工厂 */
	class Factory
	{
	public:
	    virtual SUV* createSUV() = 0;
	    virtual MPV* createMPV() = 0;
	};
	
	/* 黑车工厂，创建各种黑车 */
	class WhiteFactory: public Factory
	{
	public:
	    SUV* createSUV()
	    {
	        return new WhiteSUV();
	    }
	    MPV* createMPV()
	    {
	        return new WhiteMPV();
	    }
	};
	
	/* 白车工厂，创建各种白车 */
	class BlackFactory: public Factory
	{
	public:
	    SUV* createSUV()
	    {
	        return new BlackSUV();
	    }
	    MPV* createMPV()
	    {
	        return new BlackMPV();
	    }
	};
	
	int main()
	{
	    Factory* black_factory = new BlackFactory();
	    Factory* white_factory = new WhiteFactory();
	    SUV* black_suv = black_factory->createSUV();
	    black_suv->type();
	    MPV* white_mpv = white_factory->createMPV();
	    white_mpv->type();
	    
	    delete black_factory;
	    delete white_factory;
	    black_factory = NULL;
	    white_factory = NULL;
	    delete black_suv;
	    delete white_mpv;
	    black_suv = NULL;
	    white_mpv = NULL;
	    return 0;
	}

如上代码，车子分为SUV和MPV两种类型，但是工厂将白色和黑色作为两种系列，如果想要黑色的SUV，就使用黑色工厂BlackFactory对象去createSUV。所以抽象工厂模式主要用在多个产品多个系列上面。