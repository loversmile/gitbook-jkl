## 设计模式--策略模式--传统策略模式

策略模式（Strategy）它定义了算法家族，分别封装起来，让它们之间可以互相替换，此模式让算法的变化，不会影响到使用算法的客户。

相比于使用大量的if...else，使用策略模式可以降低复杂度，使得代码更容易维护。

缺点：可能需要定义大量的策略类，并且这些策略类都要提供给客户端。

### 传统策略模式

	#include <iostream>
	
	using namespace std;
	
	class Strategy
	{
	public:
	    virtual const void type() = 0;
	};
	
	class StrategyA: public Strategy
	{
	public:
	    const void type()
	    {
	        cout << "Strategy A" << endl;
	    }
	};
	
	class StrategyB: public Strategy
	{
	public:
	    const void type()
	    {
	        cout << "Strategy B" << endl;
	    }
	};
	
	typedef enum
	{
	    TYPE_A,
	    TYPE_B,
	    TYPE_NUM
	}StrategyType;
	
	class ContextA
	{
	public:
	    ContextA(StrategyType type)
	    {
	        switch(type)
	        {
	        case TYPE_A:
	            strategy = new StrategyA();
	            break;
	        case TYPE_B:
	            strategy = new StrategyB();
	            break;
	        default:
	            break;
	        }
	    }
	    ~ContextA()
	    {
	        delete strategy;
	        strategy = NULL;
	    }
	    void test()
	    {
	        strategy->type();
	    }
	private:
	    Strategy *strategy;
	};
	
	/* template for strategy */
	template <typename T>
	class Context
	{
	public:
	    void test()
	    {
	        test_t.type();
	    }
	private:
	    T test_t;
	};
	
	int main()
	{
	    Context<StrategyA> *sa = new Context<StrategyA>;
	    sa->test();
	
	    ContextA *ca = new ContextA(TYPE_B);
	    ca->test();
	
	    delete sa;
		delete ca;
	    sa = NULL;
		ca = NULL;
	    return 0;
	}

上面代码从模板类和传参的方式来实现策略设计模式，Context为持有一个策略类的引用，最终给客户端调用，如果策略有变化，由于客户端使用的是Context，并不会影响客户端的使用。