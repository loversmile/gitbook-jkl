## 设计模式--建造者模式

### 建造者模式

创建者模式又叫建造者模式，是将一个复杂的对象的构建与它的表示分离，使
得同样的构建过程可以创建不同的表示。创建者模式隐藏了复杂对象的创建过程，它把复杂对象的创建过程加以抽象，通过子类继承或者重载的方式，动态的创建具有复合属性的对象。

#### 适用场景：

- 隔离复杂对象的创建和使用，相同的方法，不同执行顺序，产生不同事件结果
- 多个部件都可以装配到一个对象中，但产生的运行结果不相同
- 产品类非常复杂或者产品类因为调用顺序不同而产生不同作用
- 初始化一个对象时，参数过多，或者很多参数具有默认值
- Builder模式不适合创建差异性很大的产品类，产品内部变化复杂，会导致需要定义很多具体建造者类实现变化，增加项目中类的数量，增加系统的理解难度和运行成本
- 需要生成的产品对象有复杂的内部结构，这些产品对象具备共性；


#### 优点：

- 使用建造者模式可以使客户端不必知道产品内部组成的细节。
- 具体的建造者类之间是相互独立的，这有利于系统的扩展。
- 具体的建造者相互独立，因此可以对建造的过程逐步细化，而不会对其他模块产生任何影响。

#### 缺点：

- 建造者模式所创建的产品一般具有较多的共同点，其组成部分相似；如果产品之间的差异性很大，则不适合使用建造者模式，因此其使用范围受到一定的限制。
- 如果产品的内部变化复杂，可能会导致需要定义很多具体建造者类来实现这种变化，导致系统变得很庞大。
####

	#include <iostream>
	
	using namespace std;
	
	class Car{
	public:
	    void setId(string id)
	    {
	        cout << "set id : " << id << endl;
	        this->id = id;
	    }
	    string& getId()
	    {
	        return this->id;
	    }
	    void setColor(string color)
	    {
	        cout << "set color : " << color << endl;
	        this->color = color;
	    }
	    string& getColor()
	    {
	        return this->color;
	    }
	    void setDriver(string driver)
	    {
	        cout << "set driver : " << driver << endl;
	        this->driver = driver;
	    }
	    string& getDriver()
	    {
	        return this->driver;
	    }
	private:
	    string id;
	    string color;
	    string driver;
	};
	
	class Builder
	{
	public:
	    virtual void buildId(string id) = 0;
	    virtual void buildColor(string color) = 0;
	    virtual void buildDriver(string driver) = 0;
	    virtual Car* createCar() = 0;
	};
	
	class JiliCarBuilder: public Builder
	{
	public:
	    JiliCarBuilder()
	    {
	        mcar = new Car();
	    }
	    ~JiliCarBuilder()
	    {
	        delete mcar;
	        mcar = NULL;
	    }
	    void buildId(string id)
	    {
	        mcar->setId(id);
	    }
	    void buildColor(string color)
	    {
	        mcar->setColor(color);
	    }
	    void buildDriver(string driver)
	    {
	        mcar->setDriver(driver);
	    }
	    Car* createCar()
	    {
	        return mcar;
	    }
	private:
	    Car* mcar;
	};
	
	class Director
	{
	public:
	    Director(Builder* builder): m_builder(builder){}
	    void contruct()
	    {
	        m_builder->buildId("jili");
	        m_builder->buildColor("red");
	        m_builder->buildDriver("man");
	    }
	private:
	    Builder* m_builder;
	};
	
	int main()
	{
	    Builder* builder = new JiliCarBuilder(); //初始化一个 builder 创造者
	    Director* director = new Director(builder);//init Director
	
	    director->contruct(); //指挥创建jilicar
	
	    Car* car = builder->createCar(); //创建好jilicar的各个部分后，付给car对象
	    cout << "car color: " << car->getColor() << endl;
	
	    return 0;
	}

简单分析一下建造者模式的几个部分，产品、制造商、指挥者，实现的过程就是创建一个具体的产品制造商（JiliCarBuilder），具体的需求者（指挥者Director），通过Director对象中的实现去制造产品，最后将director指挥制造好的产品给最后的Car对象，完成建造。