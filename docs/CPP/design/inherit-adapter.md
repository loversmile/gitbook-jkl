## 设计模式--适配器模式--继承实现适配器模式

适配器模式可以将一个类的接口转换成客户端希望的另一个接口，使得原来由于接口不兼容而不能在一起工作的那些类可以在一起工作。通俗的讲就是当我们已经有了一些类，而这些类不能满足新的需求，此时就可以考虑是否能将现有的类适配成可以满足新需求的类。适配器类需要继承或依赖已有的类，实现想要的目标接口。

缺点：过多地使用适配器，会让系统非常零乱，不易整体进行把握。比如，明明看到调用的是 A 接口，其实内部被适配成了 B 接口的实现，一个系统如果太多出现这种情况，无异于一场灾难。因此如果不是很有必要，可以不使用适配器，而是直接对系统进行重构。

### 使用继承实现适配器模式

	#include <iostream>
	
	using namespace std;
	
	class Sequence
	{
	public:
	    virtual void push(int x) = 0;
	    virtual void pop() = 0;
	};
	
	/* to be adatper */
	class Dqueue
	{
	public:
	    void push_back(int x)
	    {
	        cout << "Dqueue push back " << x << endl;
	    }
	    void push_front(int x)
	    {
	        cout << "Dqueue push front " << x << endl;
	    }
	    void pop_back()
	    {
	        cout << "Dqueue pop back" << endl;
	    }
	    void pop_front()
	    {
	        cout << "Dqueue pop front" << endl;
	    }
	};
	
	/* LIFO */
	class Stack: public Sequence,private Dqueue
	{
	public:
	    void push(int x)
	    {
	        push_front(x);
	    }
	    void pop()
	    {
	        pop_front();
	    }
	};
	
	/* FIFO */
	class Queue: public Sequence, private Dqueue
	{
	public:
	    void push(int x)
	    {
	        push_back(x);
	    }
	    void pop()
	    {
	        pop_front();
	    }
	};
	
	int main()
	{
	    Sequence *st = new Stack();
	    Sequence *qe = new Queue();
	    st->push(1);
	    st->pop();
	    qe->push(2);
	    qe->pop();
	    delete st;
	    delete qe;
	    st = NULL;
	    qe = NULL;
	    return 0;
	}

我们有一个双端队列，要把它适配成栈和队列的形式，虽然使用双端队列也可以实现数据，但是并不是那么好使用，所以就把它适配（包装）一下，成为栈和队列的形式对外使用。这里和使用复用实现的适配器模式不一样的地方是这里的栈和队列私有继承了Dqueue，这样就可以直接使用Dqueue中的方法，不用定义私有对象。