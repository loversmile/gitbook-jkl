## 设计模式--原型模式

### 原型模式

**原型模式**：用原型实例指定创建对象的种类，并且通过拷贝这些原型创建新的对象。通俗的讲就是当需要创建一个新的实例化对象时，我们刚好有一个实例化对象，但是已经存在的实例化对象又不能直接使用。这种情况下拷贝一个现有的实例化对象来用，可能会更方便。

以下情形可以考虑使用原型模式：

- 当new一个对象，非常繁琐复杂时，可以使用原型模式来进行复制一个对象。比如创建对象时，构造函数的参数很多，而自己又不完全的知道每个参数的意义，就可以使用原型模式来创建一个新的对象，不必去理会创建的过程。
- 当需要new一个新的对象，这个对象和现有的对象区别不大，我们就可以直接复制一个已有的对象，然后稍加修改。
- 当需要一个对象副本时，比如需要提供对象的数据，同时又需要避免外部对数据对象进行修改，那就拷贝一个对象副本供外部使用。


	#include <iostream>
	
	using namespace std;
	
	class Clone
	{
	public:
	    virtual Clone *clone() = 0;
	    virtual void show() = 0;
	};
	
	class Child: public Clone
	{
	public:
	    Child(int age, string name):Clone(),s_age(age),s_name(name)
	    {
	        cout << "Child age : " << &s_age << endl;
	        cout << "Child name : " << &s_name << endl;
	    }
	    ~Child(){}
	    Child(const Child& obj)
	    {
	        this->s_age = obj.s_age;
	        this->s_name = obj.s_name;
	        cout << "Child(copy) age : " << &s_age << endl;
	        cout << "Child(copy) name : " << &s_name << endl;
	    }
	    Clone* clone()
	    {
	        return new Child(*this);
	    }
	    void show()
	    {
	        cout << "age : " << &s_age << endl;
	        cout << "name : " << &s_name << endl;
	    }
	private:
	    int s_age;
	    string s_name;
	};
	
	int main()
	{
	    Clone* c1 = new Child(18, "xiaoxiao");
	    c1->show();
	    Clone* c2 = c1->clone();
	    c2->show();
	    delete c1;
	    delete c2;
	    c1 = NULL;
	    c2 = NULL;
	    return 0; 
	}


其实说的简单一点，就是，原型模式就是使用已有对象来生成新对象的一种做法，如上就是使用对象c1来创建了一个c2对象。