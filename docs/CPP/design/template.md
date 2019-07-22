## 设计模式--模板模式

### 模式模式
**模板模式**：定义一个操作中的算法的骨架，而将一些步骤延迟到子类中。模板方法使得子类可以不改变一个算法的结构即可重定义该算法的某些特定步骤。

当多个类有相同的方法，并且逻辑相同，只是细节上有差异时，可以考虑使用模板模式。具体的实现上可以将相同的核心算法设计为模板方法，具体的实现细节有子类实现。

缺点:每一个不同的实现都需要一个子类来实现，导致类的个数增加，使得系统更加庞大。

以手机为例，一般人看到手机，都要看一下一些配置参数，基本每个手机都是看一样的东西。

	#include <iostream>
	
	using namespace std;
	
	class Phone
	{
	public:
	    void show()
	    {
	        showGPU();
	        showCamera();
	        showRAM();
	    }
	protected:
	    virtual void showGPU() = 0;
	    virtual void showCamera() = 0;
	    virtual void showRAM() = 0;
	};
	
	class PhoneA: public Phone
	{
	protected:
	    void showGPU()
	    {
	        cout << "Phone A GPU is 970" << endl;
	    }
	    void showCamera()
	    {
	        cout << "Phone A Camera is carl" << endl;
	    }
	    void showRAM()
	    {
	        cout << "Phone A RAM is 8G" << endl;
	    }
	};
	
	class PhoneB: public Phone
	{
	protected:
	    void showGPU()
	    {
	        cout << "Phone B GPU is 930" << endl;
	    }
	    void showCamera()
	    {
	        cout << "Phone B Camera is SONY" << endl;
	    }
	    void showRAM()
	    {
	        cout << "Phone B RAM is 6G" << endl;
	    }
	};
	
	int main()
	{
	    PhoneA *pa = new PhoneA();
	    pa->show();
	    delete pa;
	    pa = NULL;
	    return 0;
	}

上面的代码中，因为查看手机参数都是一个动作，所以放到了父类中，然后各个子类中实现不同的参数。