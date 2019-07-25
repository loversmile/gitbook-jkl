## 设计模式--组合模式

### 组合模式

**组合模式**：允许你将对象组合成树形结构来表现”部分-整体“的层次结构，使得客户以一致的方式处理单个对象以及对象的组合。
组合模式实现的最关键的地方是——简单对象和复合对象必须实现相同的接口。这就是组合模式能够将组合对象和简单对象进行一致处理的原因。

在以下情况下应该考虑使用组合模式：

- 当想表达对象的部分-整体的层次结构时。
- 希望用户忽略组合对象与单个对象的不同，用户将统一地使用组合结构中的所有对象时。

### 组合模式的组成部分

- 组合部件（Component）：它是一个抽象角色，为要组合的对象提供统一的接口。
- 叶子（Leaf）：在组合中表示子节点对象，叶子节点不能有子节点。
- 合成部件（Composite）：定义有枝节点的行为，用来存储部件，实现在Component接口中的有关操作，如增加（Add）和删除（Remove）。

### 代码实现

	#include <iostream>
	#include<list>
	
	using namespace std;
	
	//抽象类，提供组合和单个对象的一致接口
	class Component
	{
	public:
	    Component(const string& name): m_name(name){}
	    virtual ~Component()
	    {
	        cout << "~Component()" << endl;
	    }
	    virtual void add(Component* ) = 0;
	    virtual void remove(const string&) = 0;
	    virtual void display(int depth) = 0;
	
	    virtual const string& name()
	    {
	        return m_name;
	    }
	private:
	    string m_name;
	};
	
	class Composite: public Component
	{
	public:
	    Composite(const string& name): Component(name){}
	    virtual ~Composite()
	    {
	        cout << "~Composite()" << endl;
	    }
	    
	    void add(Component* component )
	    {
	        // Component temp(component);
	        m_ComponentList.push_back(component);
	    }
	    
	    void remove(const string& str)
	    {
	        list<Component*>::iterator iter = m_ComponentList.begin();
	        for(; iter != m_ComponentList.end(); iter++)
	        {
	            if ((*iter)->name() == str)
	            {
	                m_ComponentList.erase(iter);
	                //不应该在此处使用list<T>.erase(list<T>::iterator iter),会导致iter++错误，这里删除目标元素之后，必须return。
	                return;
	            }
	        }
	    }
	
	    void display(int depth)
	    {
	        for(int i = 0; i < depth; i++)
	        {
	            cout << "-";
	        }
	        cout << this->name().data() << endl;
	        list<Component*>::iterator iter = m_ComponentList.begin();
	        for(; iter != m_ComponentList.end(); iter++)
	        {
	            (*iter)->display(depth + 1);
	        }
	    }
	private:
	    list<Component*> m_ComponentList;
	};
	
	class Leaf: public Component
	{
	public:
	    Leaf(const string& name): Component(name){}
	    ~Leaf()
	    {
	        cout << "~Leaf()" << endl;
	    }
	    void add(Component* )
	    {
	
	    }
	    void remove(const string& )
	    {
	
	    }
	    void display(int depth)
	    {
	        for(int i = 0; i < depth; i++)
	        {
	            cout << "-";
	        }
	        cout << this->name().data() << endl;
	    }
	};
	
	int main()
	{
	    Composite* root = new Composite("ROOT");
	    Composite* child1 = new Composite("Child1");
	    Composite* child2 = new Composite("Child2");
	
	    Leaf *a1 = new Leaf("a1");
	    Leaf *b1 = new Leaf("b1");
	
	    Leaf *a2 = new Leaf("a2");
	    Leaf *b2 = new Leaf("b2");
	
	    child1->add(a1);
	    child1->add(b1);
	
	    child2->add(a2);
	    child2->add(b2);
	
	    root->add(child1);
	    root->add(child2);
	
	    root->display(1);
	
	    cout << "After remove ===" << endl;
	    child1->remove("b1");
	
	    root->display(1);
	
	    delete root;
	    root = NULL;
	
	    return 0;
	}

### 组合模式的优缺点

**优点**：组合模式使得客户端代码可以一致地处理对象和对象容器，无需关心处理的单个对象，还是组合的对象容器。
将“客户代码与复杂的对象容器结构”解耦。可以更容易地往组合对象中加入新的构件。

**缺点**：使得设计更加复杂。客户端需要花更多时间理清类之间的层次关系。（这个是几乎所有设计模式所面临的问题）。

### 注意的问题：

有时候系统需要遍历一个树枝结构的子构件很多次，这时候可以考虑把遍历子构件的结构存储在父构件里面作为缓存。

客户端尽量不要直接调用树叶类中的方法，而是借用其父类（Graphics）的多态性完成调用，这样可以增加代码的复用性。