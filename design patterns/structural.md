# Structural Pattern #

　　描述如何将类或者对象结合在一起形成更大的结构，就像搭积木，可以通过简单积木的组合形成复杂的、功能更为强大的结构。
  
---
## Adapter Pattern（适配器模式） ##
1.	Definition  
	适配器模式(Adapter Pattern) ：将一个接口转换成客户希望的另一个接口，适配器模式使接口不兼容的那些类可以一起工作，其别名为包装器(Wrapper)。适配器模式既可以作为类结构型模式，也可以作为对象结构型模式。  

2.	Struct  
	适配器模式有对象适配器和类适配器两种实现：  
	对象适配器：  
	![Adapter](https://github.com/mxHoward/learning/blob/img/img/design_patterns/Adapter.jpg)
	类适配器：  
	![Adapter Class](https://github.com/mxHoward/learning/blob/img/img/design_patterns/Adapter_classModel.jpg)  

3.	Sequence Diagram  
	![Adapter SD](https://github.com/mxHoward/learning/blob/img/img/design_patterns/seq_Adapter.jpg)  

4.	Code
	```c++
	//  adapter.h
	//  Definition of the Class adapter
	#ifndef _ADAPTER_H_
	#define _ADAPTER_H_
	#include "target.h"
	#include "adaptee.h"

	class adapter : public target
	{
	public:
		adapter(adaptee *adaptee_);
		virtual ~adapter();
		virtual void request();
	private:
		adaptee* mp_adaptee;

	};
	#endif // ifndef _ADAPTER_H_


	//  adapter.cpp
	//  Implementation of the Class adapter
	#include "adapter.h"

	adapter::adapter(adaptee * adaptee_)
	{
		mp_adaptee =  adaptee_;
	}

	adapter::~adapter() {}

	void adapter::request()
	{
		mp_adaptee->specific_request();
	}


	//  adaptee.h
	//  Definition of the Class adaptee

	#ifnedf _ADAPTEE_H
	#define _ADAPTEE_H

	class adaptee
	{
	public:
		adaptee();
		virtual ~adaptee();
		void specific_request();
	};
	#endif //ifndef _ADAPTEE_H


	//  main.cpp
	#include <iostream>
	#include "adapter.h"
	#include "adaptee.h"
	#include "target.h"

	using namespace std;

	int main(int argc, char *argv[])
	{
		adaptee * adaptee_  = new adaptee();
		target * tar = new adapter(adaptee_);
		tar->request();
		
		return 0;
	}
	```
5.	Summary  
	*	在类适配器模式中，适配器类实现了目标抽象类接口并继承了适配者类，并在目标抽象类的实现方法中调用所继承的适配者类的方法；  
		在对象适配器模式中，适配器类继承了目标抽象类并定义了一个适配者类的对象实例，在所继承的目标抽象类方法中调用适配者类的相应业务方法。
	*	适配器模式的主要优点是将目标类和适配者类解耦，增加了类的透明性和复用性，
		同时系统的灵活性和扩展性都非常好，更换适配器或者增加新的适配器都非常方便，符合“开闭原则”；  
		类适配器模式的缺点是适配器类在很多编程语言中不能同时适配多个适配者类，对象适配器模式的缺点是很难置换适配者类的方法。
	*	适配器模式适用情况包括：  
		系统需要使用现有的类，而这些类的接口不符合系统的需要；  
		想要建立一个可以重复使用的类，用于与一些彼此之间没有太大关联的一些类一起工作。  


## Bridge Pattern（桥接模式） ##
1.	Definition  
	桥接模式(Bridge Pattern)：将抽象部分与它的实现部分分离，使它们都可以独立地变化。它是一种对象结构型模式，又称为柄体(Handle and Body)模式或接口(Interface)模式。  

2.	Struct  
	![Bridge](https://github.com/mxHoward/learning/blob/img/img/design_patterns/Bridge.jpg)
	*	Abstraction：抽象类
	*	RefinedAbstraction：扩充抽象类
	*	Implementor：实现类接口
	*	ConcreteImplementor：具体实现类  

3.	Sequence Diagram  
	![Bridge](https://github.com/mxHoward/learning/blob/img/img/design_patterns/seq_Bridge.jpg)  

4.	Code
	```c++
	//  refined_abstraction.h
	//  Definition of the Class refined_abstraction
	#ifndef _REFINED_ABSTRACTION_H_
	#define _REFINED_ABSTRACTION_H_
	#include "abstraction.h"
	class refined_abstraction : public abstraction
	{
	public:
		refined_abstraction();
		refined_abstraction(Implementor* imp);
		virtual ~refined_abstraction();

		virtual void operation();
	};
	#endif // ifndef _REFINED_ABSTRACTION_H_


	//  refined_abstraction.cpp
	//  Implementation of the Class refined_abstraction
	#include "refined_abstraction.h"
	#include <iostream>

	using namespace std;

	refined_abstraction::refined_abstraction() {}

	refined_abstraction::refined_abstraction(Implementor* imp)
		:abstraction(imp)	
	{}

	refined_abstraction::~refined_abstraction() {}

	void refined_abstraction::operation()
	{
		cout << "do something else ,and then " << endl;
		m_pImp->operation_imp();
	}

	// main.cpp
	#include <iostream>
	#include "concrete_implementorA.h"
	#include "concrete_implementorB.h"
	#include "refined_abstraction.h"
	#include "abstraction.h"

	using namespace std;

	int main(int argc, char *argv[])
	{
		implementor * pImp = new concrete_implementorA();
		abstraction * pa = new refined_abstraction(pImp);
		pa->operation();
		
		abstraction * pb = new refined_abstraction(new concrete_implementorB());
		pb->operation();		
		
		delete pa;
		delete pb;
		
		return 0;
	}
	```
5.	Summary
	*	在桥接模式中，抽象化(Abstraction)与实现化(Implementation)脱耦，它们可以沿着各自的维度独立变化。
	*	桥接模式的主要优点是分离抽象接口及其实现部分，是比多继承方案更好的解决方法。  
		桥接模式还提高了系统的可扩充性，在两个变化维度中任意扩展一个维度，都不需要修改原有系统，实现细节对客户透明，可以对用户隐藏实现细节；  
		其主要缺点是增加系统的理解与设计难度，且识别出系统中两个独立变化的维度并不是一件容易的事情。
	*	桥接模式适用情况包括：  
		需要在构件的抽象化角色和具体化角色之间增加更多的灵活性，避免在两个层次之间建立静态的继承联系；  
		抽象化角色和实现化角色可以以继承的方式独立扩展而互不影响；  
		一个类存在两个独立变化的维度，且这两个维度都需要进行扩展；  
		设计要求需要独立管理抽象化角色和具体化角色；  
		不希望使用继承或因为多层次继承导致系统类的个数急剧增加的系统。  


## Decorator Pattern（装饰模式） ##
1.	Definition
	装饰模式(Decorator Pattern) :动态地给一个对象增加一些额外的职责(Responsibility), 就增加对象功能来说，装饰模式比生成子类实现更为灵活。其别名也可以称为包装器(Wrapper),与适配器模式的别名相同，但它们适用于不同的场合。
2.	Struct  
	![Decorator](https://github.com/mxHoward/learning/blob/img/img/design_patterns/Decorator.jpg)  
	*	Component: 抽象构件
	*	ConcreteComponent: 具体构件
	*	Decorator: 抽象装饰类
	*	ConcreteDecorator: 具体装饰类  

3.	Sequence Diagram  
	![Decorator SD](https://github.com/mxHoward/learning/blob/img/img/design_patterns/seq_Decorator.jpg)  

4.	Code  
	```c++
	//  concrete_component.cpp
	//  Implementation of the Class concrete_component
	#include "concrete_component.h"
	#include <iostream>
	using namespace std;

	concrete_component::concrete_component() {}

	concrete_component::~concrete_component() {}

	void concrete_component::operation()
	{
		cout << "concrete_component's normal operation!" << endl;
	}



	//  concrete_decoratorA.h
	//  Definition of the Class concrete_decoratorA
	#ifndef _CONCRETE_DECORATOR_H_
	#define _CONCRETE_DECORATOR_H_
	#include "decorator.h"
	#include "component.h"
	class concrete_decoratorA : public decorator
	{
	public:
		concrete_decoratorA(Component* pcmp);
		virtual ~concrete_decoratorA();

		void add_behavior();
		virtual void operation();
	};
	#endif // ifndef _CONCRETE_DECORATOR_H_




	//  concrete_decoratorA.cpp
	//  Implementation of the Class concrete_decoratorA
	#include "concrete_decoratorA.h"
	#include <iostream>
	using namespace std;

	concrete_decoratorA::concrete_decoratorA(Component* pcmp)
		:Decorator(pcmp)
	{ }

	concrete_decoratorA::~concrete_decoratorA() {}

	void concrete_decoratorA::add_behavior()
	{
		cout << "add_behavior AAAA" << endl;
	}

	void concrete_decoratorA::operation()
	{
		decorator::operation();
		add_behavior();
	}
	```


5.	Summary  
	*	使用装饰模式来实现扩展比继承更加灵活，它以对客户透明的方式动态地给一个对象附加更多的责任。
		装饰模式可以在不需要创造更多子 类的情况下，将对象的功能加以扩展。
	*	装饰模式的主要优点在于可以提供比继承更多的灵活性，
		可以通过一种动态的方式来扩展一个对象的功能，并通过使用不同的具体装饰类以及这些装饰类的排列组合，可以创造出很多不同行为的组合，而且具体构件类与具体装饰类可以独立变化，用户可以根据需要增加新的具体构件类和具体装饰类；  
		其主要缺点在于使用装饰模式进行系统设计时将产生很多小对象，而且装饰模式比继承更加易于出错，排错也很困难，对于多次装饰的对象，调试时寻找错误可能需 要逐级排查，较为烦琐。
	*	装饰模式适用情况包括：  
		在不影响其他对象的情况下，以动态、透明的方式给单个对象添加职责；  
		需要动态地给一个对象增加功能，这些功能也可以动态地 被撤销；  
		当不能采用继承的方式对系统进行扩充或者采用继承不利于系统扩展和维护时。  


## Facade Pattern（外观模式） ##
1.	Definition  
	外观模式(Facade Pattern)：外部与一个子系统的通信必须通过一个统一的外观对象进行，为子系统中的一组接口提供一个一致的界面，外观模式定义了一个高层接口，这个接口使得这一子系统更加容易使用。外观模式又称为门面模式，它是一种对象结构型模式。  

2.	Struct  
	![Facade](https://github.com/mxHoward/learning/blob/img/img/design_patterns/Facade.jpg)  
	*	Facade: 外观角色  
	*	SubSystem:子系统角色  

3.	Sequence Diagram  
	![Facade SD](https://github.com/mxHoward/learning/blob/img/img/design_patterns/seq_Facade.jpg)  

4.	Code  
	```c++
	//  facade.h
	//  Definition of the Class facade
	#ifndef _FACADE_H_
	#define _FACADE_H_
	#include "systemA.h"
	#include "systemB.h"
	#include "systemC.h"
	class facade
	{
	public:
		facade();
		virtual ~facade();
		void wrap_opration();
	private:
		systemC *m_systemA;
		systemA *m_systemB;
		systemB *m_systemC;
	};
	#endif // ifndef_FACADE_H_



	//  facade.cpp
	//  Implementation of the Class facade
	#include "facade.h"
	facade::facade()
	{
		m_systemA  = new systemA();
		m_systemB = new systemB();
		m_systemC = new systemC();
	}

	facade::~facade()
	{
		delete m_systemA;
		delete m_systemB;
		delete m_systemC;
	}

	void facade::wrap_opration()
	{
		m_systemA->operationA();
		m_systemB->operationB();
		m_systemC->opeartionC();
	}



	// main.cpp
	#include <iostream>
	#include "facade.h"

	using namespace std;

	int main(int argc, char *argv[])
	{
		facade fa;
		fa.wrap_opration();
		
		return 0;
	}
	```


5.	Summary  
	*	外观模式要求一个子系统的外部与其内部的通信通过一个统一的外观对象进行，
		外观类将客户端与子系统的内部复杂性分隔开，使得客户端只需要与外观对象打交道，而不需要与子系统内部的很多对象打交道。
	*	外观模式主要优点在于对客户屏蔽子系统组件，减少了客户处理的对象数目并使得子系统使用起来更加容易，
		它实现了子系统与客户之间的松耦合关系，并降低了大型软件系统中的编译依赖性，简化了系统在不同平台之间的移植过程；  
		其缺点在于不能很好地限制客户使用子系统类，而且在不引入抽象外观类的情况下，增加新的子系统可能需要修改外观类或客户端的源代码，违背了“开闭原则”。
	*	外观模式适用情况包括：  
		要为一个复杂子系统提供一个简单接口；  
		客户程序与多个子系统之间存在很大的依赖性；  
		在层次化结构中，需要定义系统中每一层的入口，使得层与层之间不直接产生联系。  

## Flyweight Pattern（享元模式） ##
1.	Definition  
	享元模式(Flyweight Pattern)：运用共享技术有效地支持大量细粒度对象的复用。系统只使用少量的对象，而这些对象都很相似，状态变化很小，可以实现对象的多次复用。由于享元模式要求能够共享的对象必须是细粒度对象，因此它又称为轻量级模式，它是一种对象结构型模式。  
2.	Struct  
	![Flyweight](https://github.com/mxHoward/learning/blob/img/img/design_patterns/Flyweight.jpg)  
	*	Flyweight: 抽象享元类
	*	ConcreteFlyweight: 具体享元类
	*	UnsharedConcreteFlyweight: 非共享具体享元类
	*	FlyweightFactory: 享元工厂类  

3.	Sequence Diagram  
	![Flyweight SD](https://github.com/mxHoward/learning/blob/img/img/design_patterns/seq_Flyweight.jpg)  

4.	Code
	```c++
	//  flyweight_factory.cpp
	//  Implementation of the Class flyweight_factory
	#include "flyweight_factory.h"
	#include "concrete_flyweight.h"
	#include <iostream>
	using namespace std;
	flyweight_factory::flyweight_factory() {}

	flyweight_factory::~flyweight_factory() {}

	flyweight* flyweight_factory::get_flyweight(string str)
	{
		map<string,flyweight*>::iterator itr = mp_flyweight.find(str);
		if(itr == mp_flyweight.end())
		{
			flyweight * fw = new concrete_flyweight(str);
			mp_flyweight.insert(make_pair(str,fw));
			return fw;	
		}
		else
		{
			cout << "aready in the pool,use the exist one:" << endl;
			return itr->second;
		}		
	}




	//  concrete_flyweight.h
	//  Definition of the Class concrete_flyweight
	#ifndef _CONCRETE_FLYWEIGHT_H_
	#define _CONCRETE_FLYWEIGHT_H_
	#include "flyweight.h"
	#include <string>
	using namespace std;

	class concrete_flyweight : public flyweight
	{
	public:
		concrete_flyweight(string str);
		virtual ~concrete_flyweight();

		virtual void operation();

	private:
		string intrinsic_state;
	};
	#endif // ifndef _CONCRETE_FLYWEIGHT_H_





	//  concrete_flyweight.cpp
	//  Implementation of the Class concrete_flyweight
	#include "concrete_flyweight.h"
	#include <iostream>
	using namespace std;

	concrete_flyweight::concrete_flyweight(string str)
	{
		intrinsic_state = str;
	}

	concrete_flyweight::~concrete_flyweight() {}

	void concrete_flyweight::operation()
	{
		cout << "flyweight[" << intrinsic_state << "] do operation." << endl; 
	}




	// main.cpp
	#include <iostream>
	#include "concrete_flyweight.h"
	#include "flyweight_factory.h"
	#include "flyweight.h"
	using namespace std;

	int main(int argc, char *argv[])
	{
		flyweight_factory factory;
		flyweight * fw = factory.get_flyweight("one");
		fw->operation();
		
		flyweight * fw2 = factory.get_flyweight("two");
		fw2->operation();

		//aready exist in pool
		flyweight * fw3 = factory.get_flyweight("one");
		fw3->operation();


		return 0;
	}
	```  

5.	Summary  
	*	享元模式以共享的方式高效地支持大量的细粒度对象，享元对象能做到共享的关键是区分内部状态和外部状态。
		其中内部状态是存储在享元对象内部并且不会随环境改变而改变的状态，因此内部状态可以共享；外部状态是随环境改变而改变的、不可以共享的状态。
	*	享元模式主要优点在于它可以极大减少内存中对象的数量，使得相同对象或相似对象在内存中只保存一份；  
		其缺点是使得系统更加复杂，并且需要将享元对象的状态外部化，而读取外部状态使得运行时间变长。
	*	享元模式适用情况包括：  
		一个系统有大量相同或者相似的对象，由于这类对象的大量使用，造成内存的大量耗费；  
		对象的大部分状态都可以外部化，可以将这些外部状态传入对象中；多次重复使用享元对象。  



## Proxy Pattern（代理模式） ##
1.	Definition  
	代理模式(Proxy Pattern) ：给某一个对象提供一个代 理，并由代理对象控制对原对象的引用。它是一种对象结构型模式。  

2.	Struct  
	![Proxy](https://github.com/mxHoward/learning/blob/img/img/design_patterns/Proxy.jpg)  
	*	Subject: 抽象主题角色
	*	Proxy: 代理主题角色
	*	Realsubject: 真实主题角色  

3.	Sequence Diagram  
	![Proxy SD](https://github.com/mxHoward/learning/blob/img/img/design_patterns/seq_Proxy.jpg)  

4.	Code
	```c++
	//  proxy.h
	//  Definition of the Class proxy
	#ifndef _PROXY_H_
	#define _PROXY_H_
	#include "real_subject.h"
	#include "subject.h"
	class proxy : public subject
	{
	public:
		proxy();
		virtual ~proxy();

		void request();
	private:
		void afterRequest();
		void preRequest();	
		real_subject *mp_real_subject;
	};
	#endif // ifndef _PROXY_H_




	//  proxy.cpp
	//  Implementation of the Class proxy
	#include "proxy.h"
	#include <iostream>
	using namespace std;
	proxy::proxy()
	{
		mp_real_subject = new real_subject();
	}

	proxy::~proxy()
	{
		delete mp_real_subject;
	}

	void proxy::after_request()
	{
		cout << "proxy::after request" << endl;
	}

	void proxy::before_request()
	{
		cout << "proxy::before request" << endl;
	}

	void proxy::request()
	{
		before_request();
		mp_real_subject->request();
		after_request();
	}




	//main.cpp
	#include <iostream>
	#include "real_subject.h"
	#include "proxy.h"

	using namespace std;

	int main(int argc, char *argv[])
	{
		proxy proxy_;
		proxy_.request();
		
		return 0;
	}
	```  

5.	Summary  
	*	远程代理为一个位于不同的地址空间的对象提供一个本地的代表对象，
		它使得客户端可以访问在远程机器上的对象，远程机器可能具有更好的计算性能与处理速度，可以快速响应并处理客户端请求。
	*	代理模式的优点在于能够协调调用者和被调用者，在一定程度上降低了系统的耦合度；  
		其缺点在于由于在客户端和真实主题之间增加了代理对象，因此有些类型的代理模式可能会造成请求的处理速度变慢，并且实现代理模式需要额外的工作，有些代理模式的实现非常复杂。  


