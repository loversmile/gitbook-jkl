## 员工老板问题来讲callback

看了一些 java 的回调函数的例子，觉得下面这个例子讲的比较好

老板想让一个员工帮他打印一份文件，打印完之后告诉一下老板

首先我们要有一个老板类，和一个员工类，以及一个接口让员工来告诉老板

JAVA中的回调一般使用接口实现，接口的好处就不赘述了

	// 定义一个接口，用来回调
	interface CallB {
	    public void back();
	}

	// 定义员工类
	class Emp {
	    public void doPrint(CallB b) {  // 传入接口， 员工干完活要调用 callback 接口
	        System.out.println("I am print ...... ");
			Thread.sleep(1000);
			System.out.println("I am print OK...... ");

	        b.back(); // 员工干完活了，通知老板 ------这里就是回调
	    }
	}
	
	// 定义老板类
	class Boss implements CallB { 		//为了员工干活接口能联系我，提供一个接口
	    Emp e = new Emp();				// new一个员工
	    public void call() { 			// 找人干活的方法
	        this.e.doPrint(this); 		// 找员工print，然后传入this(自己)，告诉员工结束了联系自己（boss）
	    }
	    public void back() { 			// 接口的方法
	        System.out.println("Print finish ......");
	    }
	}
	
	class CallbackTest {
	    public static void main(String[] args) {
	        Boss boss = new Boss();

	        boss.call(); 				// 老板找个人干活
	    }
	}


这样就实现了员工doPrint();  
print好后，回调Boss的电话通知Boss，这就是回调