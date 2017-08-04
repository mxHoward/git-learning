# Design Patterns #
---
## Creational Patterns（创建型模式） ##

　　创建型模式(Creational Pattern)对类的实例化过程进行了抽象，能够将软件模块中对象的创建和对象的使用分离。为了使软件的结构更加清晰，外界对于这些对象只需要知道它们共同的接口，而不清楚其具体的实现细节，使整个系统的设计更加符合单一职责原则。创建型模式隐藏了类的实例的创建细节，通过隐藏对象如何被创建和组合在一起达到使整个系统独立的目的。  
### Simple Factory Pattern（简单工厂模式） ###
1.	Definition  
	简单工厂模式(Simple Factory Pattern)，又称为静态工厂方法(Static Factory Method)模式，它属于类创建型模式。在简单工厂模式中，可以根据参数的不同返回不同类的实例。简单工厂模式专门定义一个类来负责创建其他类的实例，被创建的实例通常都具有共同的父类。  

2.	Struct  
	![Simple Factory](https://github.com/mxHoward/learning/blob/img/img/design_patterns/SimpleFactory.jpg)
	*	Factory: 工厂角色 - 负责实现创建所有实例的内部逻辑。
	*	Product: 抽象产品角色 - 是所创建的所有对象的父类，负责描述所有实例所共有的公共接口。
	*	ConcreteProdcut: 具体产品角色 - 是创建目标，所有创建的对象都充当这个角色的某个具体类的实例。  

3.	Sequence Diagram  
	![Simple Factory SD](https://github.com/mxHoward/learning/blob/img/img/design_patterns/seq_SimpleFactory.jpg)  

4.	Code
	```c++
	//  simple_factory.cpp
	//  Implementation of the Class simple_factory
	#include "simple_factory.h"
	#include "concrete_productA.h"
	#include "concrete_productB.h"

	product* simple_factory::create_product(string proname)
	{
		if ( "A" == proname )
		{
			return new concrete_productA();
		}
		else if("B" == proname)
		{
			return new concrete_productB();
		}
		return  NULL;
	}
	```

5.	Summary  
	*	简单工厂模式的要点在于：当你需要什么，只需要传入一个正确的参数，就可以获取你所需要的对象，而无须知道其创建细节。
	*	简单工厂模式最大的优点在于实现对象的创建和对象的使用分离，将对象的创建交给专门的工厂类负责，但是其最大的缺点在于工厂类不够灵活	，增加新的具体产品需要修改工厂类的判断逻辑代码，而且产品较多时，工厂方法代码将会非常复杂。
	*	简单工厂模式适用情况包括：工厂类负责创建的对象比较少；客户端只知道传入工厂类的参数，对于如何创建对象不关心。  

### Factory Method Pattern（工厂方法模式） ###
1.	Definition  
	工厂方法模式(Factory Method Pattern)又称为工厂模式，也叫虚拟构造器(Virtual Constructor)模式或者多态工厂(Polymorphic Factory)模式，它属于类创建型模式。在工厂方法模式中，工厂父类负责定义创建产品对象的公共接口，而工厂子类则负责生成具体的产品对象，这样做的目的是将产品类的实例化操作延迟到工厂子类中完成，即通过工厂子类来确定究竟应该实例化哪一个具体产品类。  
2.	Struct  
	![Factory Method](https://github.com/mxHoward/learning/blob/img/img/design_patterns/FactoryMethod.jpg)
	*	Product：抽象产品
	*	ConcreteProduct：具体产品
	*	Factory：抽象工厂
	*	ConcreteFactory：具体工厂  

3.	Sequence Diagram  
	![Factory Method SD](https://github.com/mxHoward/learning/blob/img/img/design_patterns/seq_FactoryMethod.jpg)  

4.	Code
	```c++
	//  concrete_factory.cpp
	//  Implementation of the Class concrete_factory
	#include "concrete_factory.h"
	#include "concrete_product.h"

	product* concrete_factory::factory_method()
	{
		return  new concrete_product();
	}

	// main.cpp
	#include "factory.h"
	#include "concrete_factory.h"
	#include "product.h"
	#include <iostream>
	using namespace std;

	int main(int argc, char *argv[])
	{
		factory * fc = new concrete_factory();
		product * prod = fc->factory_method();
		prod->use();
		
		delete fc;
		delete prod;
		
		return 0;
	}
	```
5.	Summary
	*	工厂方法模式是简单工厂模式的进一步抽象和推广。  
		由于使用了面向对象的多态性，工厂方法模式保持了简单工厂模式的优点，而且克服了它的缺点。在工厂方法模式中，核心的工厂类不再负责所有产品的创建，而是将具体创建工作交给子类去做。这个核心类仅仅负责给出具体工厂必须实现的接口，而不负责产品类被实例化这种细节，这使得工厂方法模式可以允许系统在不修改工厂角色的情况下引进新产品。
	*	工厂方法模式的主要优点是增加新的产品类时无须修改现有系统，并封装了产品对象的创建细节，系统具有良好的灵活性和可扩展性；  
		其缺点在于增加新产品的同时需要增加新的工厂，导致系统类的个数成对增加，在一定程度上增加了系统的复杂性。
	*	工厂方法模式适用情况包括：  
		一个类不知道它所需要的对象的类；  
		一个类通过其子类来指定创建哪个对象；  
		将创建对象的任务委托给多个工厂子类中的某一个，客户端在使用时可以无须关心是哪一个工厂子类创建产品子类，需要时再动态指定。  


### Abstract Factory Pattern（抽象工厂模式） ###
1.	Definition  
	抽象工厂模式(Abstract Factory Pattern): 提供一个创建一系列相关或相互依赖对象的接口，而无须指定它们具体的类。抽象工厂模式又称为Kit模式，属于对象创建型模式。  
2.	Struct  
	![Abstract Factory](https://github.com/mxHoward/learning/blob/master/img/design_patterns/AbstractFactory.jpg)
	*	AbstractFactory：抽象工厂
	*	ConcreteFactory：具体工厂
	*	AbstractProduct：抽象产品
	*	Product：具体产品  

3.	Sequence Diagram  
	![Abstract Factory SD](https://github.com/mxHoward/learning/blob/master/img/design_patterns/seq_AbstractFactory.jpg)  

4.	Code
	```c++
	//  concrete_factory1.cpp
	//  Implementation of the Class concrete_factory1
	#include "concrete_factory1.h"
	#include "productA1.h"
	#include "productB1.h"

	abstract_productA * concrete_factory1::create_productA()
	{
		return new productA1();
	}


	abstract_productB * concrete_factory1::create_productB()
	{
		return new productB1();
	}



	//  productA1.cpp
	//  Implementation of the Class productA1
	#include "productA1.h"
	#include <iostream>

	using namespace std;

	void productA1::use()
	{
		cout << "use Product A1" << endl;
	}


	// main.cpp
	#include "abstract_factory.h"
	#include "abstract_productA.h"
	#include "abstract_productB.h"
	#include "concrete_factory1.h"
	#include "concrete_factory2.h"
	#include <iostream>

	using namespace std;

	int main(int argc, char *argv[])
	{
		abstract_factory * fc = new concrete_factory1();
		abstract_productA * pa =  fc->create_productA();
		abstract_productB * pb = fc->create_productB();
		pa->use();
		pb->use();
		
		abstract_factory * fc2 = new concrete_factory2();
		abstract_productA * pa2 =  fc2->create_productA();
		abstract_productB * pb2 = fc2->create_productB();
		pa2->use();
		pb2->use();

		delete pa;
		delete pb;
		delete fc;
		delete pa2;
		delete pb2;
		delete fc2;

		return 0;
	}
	```
5.	Summary  
	*	抽象工厂模式是所有形式的工厂模式中最为抽象和最具一般性的一种形态。  
		抽象工厂模式与工厂方法模式最大的区别在于，工厂方法模式针对的是一个产品等级结构，而抽象工厂模式则需要面对多个产品等级结构。
	*	抽象工厂模式的主要优点是隔离了具体类的生成，使得客户并不需要知道什么被创建，  
		而且每次可以通过具体工厂类创建一个产品族中的多个对象，增加或者替换产品族比较方便，增加新的具体工厂和产品族很方便；  
		主要缺点在于增加新的产品等级结构很复杂，需要修改抽象工厂和所有的具体工厂类，对“开闭原则”的支持呈现倾斜性。
	*	抽象工厂模式适用情况包括：  
		一个系统不应当依赖于产品类实例如何被创建、组合和表达的细节；  
		系统中有多于一个的产品族，而每次只使用其中某一产品族；  
		属于同一个产品族的产品将在一起使用；  
		系统提供一个产品类的库，所有的产品以同样的接口出现，从而使客户端不依赖于具体实现。  


### Builder Pattern（建造者模式） ###
1.	Definition  
	造者模式(Builder Pattern)：将一个复杂对象的构建与它的表示分离，使得同样的构建过程可以创建不同的表示。
	建造者模式是一步一步创建一个复杂的对象，它允许用户只通过指定复杂对象的类型和内容就可以构建它们，用户不需要知道内部的具体构建细节。建造者模式属于对象创建型模式。根据中文翻译的不同，建造者模式又可以称为生成器模式。  

2.	Struct  
	![Builder](https://github.com/mxHoward/learning/blob/img/img/design_patterns/Builder.jpg)
	*	Builder：抽象建造者
	*	ConcreteBuilder：具体建造者
	*	Director：指挥者
	*	Product：产品角色  

3.	Sequence Diagram  
	![Builder SD](https://github.com/mxHoward/learning/blob/img/img/design_patterns/seq_Builder.jpg)  

4.	Code
	```c++
	//  concrete_builder.cpp
	//  Implementation of the Class concrete_builder
	#include "concrete_builder.h"

	concrete_builder::concrete_builder() {}

	concrete_builder::~concrete_builder() {}

	void concrete_builder::build_partA()
	{
		m_prod->setA("A Style "); //不同的建造者，可以实现不同产品的建造  
	}

	void concrete_builder::build_partB()
	{
		m_prod->setB("B Style ");
	}


	void concrete_builder::build_partC(){
		m_prod->setC("C style ");
	}


	//  director.cpp
	//  Implementation of the Class director

	#include "director.h"

	director::director() {}

	director::~director() {}

	product* director::constuct()
	{
		m_pbuilder->build_partA();
		m_pbuilder->build_partB();
		m_pbuilder->build_partC();
		
		return m_pbuilder->get_result();
	}


	void director::set_builder(builder* buider)
	{
		m_pbuilder = buider;
	}

	// main.cpp
	#include <iostream>
	#include "concrete_builder.h"
	#include "director.h"
	#include "builder.h"
	#include "product.h"

	using namespace std;

	int main(int argc, char *argv[])
	{
		concrete_builder * builder = new concrete_builder();
		director dir;
		dir.set_builder(builder);
		product * pd =  dir.constuct();
		pd->show();
		
		delete builder;
		delete pd;
		return 0;
	}
	```

5.	Example  
	![Builder Example](https://github.com/mxHoward/learning/blob/img/img/design_patterns/KFCBuilder.jpg)  

6.	Summary  
	*	在建造者模式的结构中引入了一个指挥者类，该类的作用主要有两个：一方面它隔离了客户与生产过程；另一方面它负责控制产品的生成过程。  
		指挥者针对抽象建造者编程，客户端只需要知道具体建造者的类型，即可通过指挥者类调用建造者的相关方法，返回一个完整的产品对象。
	*	建造者模式的主要优点在于客户端不必知道产品内部组成的细节，将产品本身与产品的创建过程解耦，
		使得相同的创建过程可以创建不同的产品对象，每一个具体建造者都相对独立，而与其他的具体建造者无关，因此可以很方便地替换具体建造者或增加新的具体建造者，符合“开闭原则”，还可以更加精细地控制产品的创建过程；  
		其主要缺点在于由于建造者模式所创建的产品一般具有较多的共同点，其组成部分相似，因此其使用范围受到一定的限制，如果产品的内部变化复杂，可能会导致需要定义很多具体建造者类来实现这种变化，导致系统变得很庞大。
	*	建造者模式适用情况包括：  
		需要生成的产品对象有复杂的内部结构，这些产品对象通常包含多个成员属性；  
		需要生成的产品对象的属性相互依赖，需要指定其生成顺序；  
		对象的创建过程独立于创建该对象的类；  
		隔离复杂对象的创建和使用，并使得相同的创建过程可以创建不同类型的产品。  


### Singleton（单例模式） ###
1.	Definition  
	单例模式(Singleton Pattern): 单例模式确保某一个类只有一个实例，而且自行实例化并向整个系统提供这个实例，这个类称为单例类，它提供全局访问的方法。  

2.	Struct  
	![Singleton](https://github.com/mxHoward/learning/blob/img/img/design_patterns/Singleton.jpg)  

3.	Sequence Diagram  
	![Singleton SD](https://github.com/mxHoward/learning/blob/img/img/design_patterns/seq_Singleton.jpg)  

4.	Code
	```c++
	//  singleton.cpp
	//  Implementation of the Class singleton
	#include "singleton.h"
	#include <iostream>
	using namespace std;

	singleton * singleton::instance = NULL;
	singleton::singleton() {}

	singleton::~singleton()
	{
		delete instance;
	}

	singleton* singleton::get_instance()
	{
		if (instance == NULL)
		{
			instance = new singleton();
		}
		return  instance;
	}


	void singleton::singleton_operation()
	{
		cout << "singleton operation" << endl;
	}

	// main.cpp
	#include <iostream>
	#include "singleton.h"
	using namespace std;

	int main(int argc, char *argv[])
	{
		singleton * sg = singleton::get_instance();
		sg->singleton_operation();
		
		return 0;
	}
	```
5.	Summary  
	*	单例模式的目的是保证一个类仅有一个实例，并提供一个访问它的全局访问点。  
		单例类拥有一个私有构造函数，确保用户无法通过new关键字直接实例化它。  
		除此之外，该模式中包含一个静态私有成员变量与静态公有的工厂方法。  
		该工厂方法负责检验实例的存在性并实例化自己，然后存储在静态成员变量中，以确保只有一个实例被创建。
	*	单例模式的主要优点在于提供了对唯一实例的受控访问并可以节约系统资源；  
		其主要缺点在于因为缺少抽象层而难以扩展，且单例类职责过重。
	*	单例模式适用情况包括：  
		系统只需要一个实例对象；  
		客户调用类的单个实例只允许使用一个公共访问点。  


## Structural Patterns（结构型模式） ##

　　结构型模式(Structural Pattern)描述如何将类或者对象结合在一起形成更大的结构，就像搭积木，可以通过简单积木的组合形成复杂的、功能更为强大的结构。
---
### Adapter Pattern（适配器模式） ###
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


### Bridge Pattern（桥接模式） ###
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


### Decorator Pattern（装饰模式） ###
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


### Facade Pattern（外观模式） ###
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

### Flyweight Pattern（享元模式） ###
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



### Proxy Pattern（代理模式） ###
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



## Behavioral Patterns（行为型模式） ##

行为型模式(Behavioral Pattern)是对在不同的对象之间划分责任和算法的抽象化。行为型模式不仅仅关注类和对象的结构，而且重点关注它们之间的相互作用。通过行为型模式，可以更加清晰地划分类与对象的职责，并研究系统在运行时实例对象之间的交互。在系统运行时，对象并不是孤立的，它们可以通过相互通信与协作完成某些复杂功能，一个对象在运行时也将影响到其他对象的运行。
---
### Command Pattern（命令模式） ###
1.	Definition  
	命令模式(Command Pattern)：将一个请求封装为一个对象，从而使我们可用不同的请求对客户进行参数化；对请求排队或者记录请求日志，以及支持可撤销的操作。命令模式是一种对象行为型模式，其别名为动作(Action)模式或事务(Transaction)模式。  

2.	Struct  
	![Command](https://github.com/mxHoward/learning/blob/img/img/design_patterns/Command.jpg)  
	Command: 抽象命令类
	*	ConcreteCommand: 具体命令类
	*	Invoker: 调用者
	*	Receiver: 接收者
	*	Client:客户类 

3.	Sequence Diagram  
	![Command SD](https://github.com/mxHoward/learning/blob/img/img/design_patterns/seq_Command.jpg)  

4.	Code
	```c++
	//  receiver.h
	//  Definition of the Class receiver
	#ifndef _RECEIVER_H_
	#define _RECEIVER_H_

	class receiver
	{
	public:
		receiver();
		virtual ~receiver();
		void action();

	};
	#endif // ifndef _RECEIVER_H_





	//  receiver.cpp
	//  Implementation of the Class receiver
	#include "receiver.h"
	#include <iostream>
	using namespace std;

	receiver::receiver() {}

	receiver::~receiver() {}

	void receiver::action()
	{
		cout << "receiver action." << endl;
	}






	//  concrete_command.h
	//  Definition of the Class concrete_command
	#ifndef _CONCRETE_COMMAND_H_
	#define _CONCRETE_COMMAND_H_
	#include "Command.h"
	#include "Receiver.h"

	class concrete_command : public command
	{
	public:
		concrete_command(receiver * p_receiver);
		virtual ~concrete_command();
		virtual void execute();
	private:
		receiver *mp_receiver;

	};
	#endif // ifndef _CONCRETE_COMMAND_H_







	//  concrete_command.cpp
	//  Implementation of the Class concrete_command
	#include "concrete_command.h"
	#include <iostream>
	using namespace std;

	concrete_command::concrete_command(receiver *p_receiver)
	{
		mp_receiver = p_receiver;
	}

	concrete_command::~concrete_command() {}

	void concrete_command::execute()
	{
		cout << "concrete_command::execute"  << endl;
		mp_receiver->action();
	}






	//  invoker.h
	//  Definition of the Class invoker
	#ifndef _INVOKER_H_
	#define _INVOKER_H_
	#include "Command.h"

	class invoker
	{
	public:
		invoker(command * p_command);
		virtual ~invoker();
		void call();

	private:
		command *mp_command;

	};
	#endif // ifndef _INVOKER_H_







	//  invoker.cpp
	//  Implementation of the Class invoker
	#include "invoker.h"
	#include <iostream>
	using namespace std;

	invoker::invoker(command * p_command)
	{
		mp_command = p_command;
	}

	invoker::~invoker() {}

	void invoker::call()
	{
		cout << "invoker calling" << endl;
		mp_command->execute();
	}





	//main.cpp
	#include <iostream>
	#include "concrete_command.h"
	#include "invoker.h"
	#include "receiver.h"

	using namespace std;

	int main(int argc, char *argv[])
	{
		receiver * p_receiver = new receiver();
		concrete_command * p_command = new concrete_command(p_receiver);
		invoker * p_invoker = new invoker(p_command);
		p_invoker->call();
		
		delete p_receiver;
		delete p_command;
		delete p_invoker;

		return 0;
	}
	```  

5.	Summary  
	*	命令模式的本质是对命令进行封装，将发出命令的责任和执行命令的责任分割开。
		命令模式使请求本身成为一个对象，这个对象和其他对象一样可以被存储和传递。
	*	命令模式的主要优点在于降低系统的耦合度，增加新的命令很方便，
		而且可以比较容易地设计一个命令队列和宏命令，并方便地实现对请求的撤销和恢复；  
		其主要缺点在于可能会导致某些系统有过多的具体命令类。  
	*	命令模式适用情况包括：  
		需要将请求调用者和请求接收者解耦，使得调用者和接收者不直接交互；  
		需要在不同的时间指定请求、将请求排队和执行请求；  
		需要支持命令的撤销操作和恢复操作，需要将一组操作组合在一起，即支持宏命令。  


### Mediator Pattern（中介者模式） ###
1.	Definition  
	中介者模式(Mediator Pattern)：用一个中介对象来封装一系列的对象交互，中介者使各对象不需要显式地相互引用，从而使其耦合松散，而且可以独立地改变它们之间的交互。中介者模式又称为调停者模式，它是一种对象行为型模式。
2.	Struct  
	![Mediator](https://github.com/mxHoward/learning/blob/img/img/design_patterns/Mediator.jpg)  
	Mediator: 抽象中介者
	*	ConcreteMediator: 具体中介者
	*	Colleague: 抽象同事类
	*	ConcreteColleague: 具体同事类  

3.	Sequence Diagram  
	![Mediator SD](https://github.com/mxHoward/learning/blob/img/img/design_patterns/Mediator.jpg)  

4.	Code
	```c++
	//  concrete_mediator.h
	//  Definition of the Class concrete_mediator
	#ifndef _CONCRETE_MEDIATOR_H_
	#define _CONCRETE_MEDIATOR_H_
	#include "concrete_colleagueA.h"
	#include "concrete_colleagueB.h"
	#include "mediator.h"
	#include <map>

	using namespace std;

	class concrete_mediator : public mediator
	{
	public:
		concrete_mediator();
		virtual ~concrete_mediator();

		virtual void operation(int n_who,string str);
		virtual void registered(int n_who, colleague * a_colleague);

	private:
		map<int,Colleague*> mmp_colleague;
	};
	#endif //ifndef _CONCRETE_MEDIATOR_H_






	//  concrete_mediator.cpp
	//  Implementation of the Class concrete_mediator
	#include "concrete_mediator.h"
	#include <map>
	#include <iostream>

	using namespace std;

	concrete_mediator::concrete_mediator() {}

	concrete_mediator::~concrete_mediator() {}

	void concrete_mediator::operation(int n_who,string str)
	{
		map<int,colleague*>::const_iterator itr = mmp_colleague.find(n_who);
		if(itr == mmp_colleague.end())
		{
			cout << "not found this colleague!" << endl;
			return;
		}
		
		colleague* pc = itr->second;
		pc->receive_msg(str);
	}

	void concrete_mediator::registered(int n_who,colleague * a_colleague)
	{
		map<int,colleague*>::const_iterator itr = mmp_colleague.find(n_who);
		if(itr == mmp_colleague.end())
		{
			mmp_colleague.insert(make_pair(n_who,a_colleague));
			a_colleague->set_mediator(this);
		}
	}





	//  concrete_colleagueA.h
	//  Definition of the Class concrete_colleagueA
	#ifndef _CONCRETE_COLLEAGUEA_H_
	#define _CONCRETE_COLLEAGUEA_H_
	#include "Colleague.h"

	class concrete_colleagueA : public colleague
	{
	public:
		concrete_colleagueA();
		virtual ~concrete_colleagueA();

		virtual void send_msg(int to_who,string str);
		virtual void receive_msg(string str);
	};
	#endif //ifndef _CONCRETE_COLLEAGUEA_H_






	//  concrete_colleagueA.cpp
	//  Implementation of the Class concrete_colleagueA
	#include "concrete_colleagueA.h"
	#include <iostream>
	using namespace std;

	concrete_colleagueA::concrete_colleagueA() {}

	concrete_colleagueA::~concrete_colleagueA() {}

	void concrete_colleagueA::send_msg(int to_who,string str)
	{
		cout << "send msg from colleagueA,to:" << to_who << endl;
		mp_mediator->operation(to_who,str);
	}

	void concrete_colleagueA::receive_msg(string str)
	{
		cout << "concrete_colleagueA reveive msg:" << str <<endl;
	}




	// main.cpp
	#include <iostream>
	#include "concrete_colleagueA.h"
	#include "concrete_colleagueB.h"
	#include "concrete_mediator.h"

	using namespace std;

	int main(int argc, char *argv[])
	{
		concrete_colleagueA * pa = new concrete_colleagueA();
		concrete_colleagueB * pb = new concrete_colleagueB();
		concrete_mediator * pm = new concrete_mediator();
		pm->registered(1,pa);
		pm->registered(2,pb);
		
		// sendmsg from a to b
		pa->send_msg(2,"hello,i am a");
		// sendmsg from b to a
		pb->send_msg(1,"hello,i am b");
		
		delete pa,pb,pm;
		return 0;
	}
	```  

5.	Summary  
	*	通过引入中介者对象，可以将系统的网状结构变成以中介者为中心的星形结构，中介者承担了中转作用和协调作用。
		中介者类是中介者模式的核心，它对整个系统进行控制和协调，简化了对象之间的交互，还可以对对象间的交互进行进一步的控制。
	*	中介者模式的主要优点在于简化了对象之间的交互，将各同事解耦，还可以减少子类生成，
		对于复杂的对象之间的交互，通过引入中介者，可以简化各同事类的设计和实现；  
		中介者模式主要缺点在于具体中介者类中包含了同事之间的交互细节，可能会导致具体中介者类非常复杂，使得系统难以维护。  
	*	中介者模式适用情况包括：  
		系统中对象之间存在复杂的引用关系，产生的相互依赖关系结构混乱且难以理解；  
		一个对象由于引用了其他很多对象并且直接和这些对象通信，导致难以复用该对象；  
		想通过一个中间类来封装多个类中的行为，而又不想生成太多的子类。  



### Observer Pattern（观察者模式） ###
1.	Definition  
	观察者模式(Observer Pattern)：定义对象间的一种一对多依赖关系，使得每当一个对象状态发生改变时，其相关依赖对象皆得到通知并被自动更新。观察者模式又叫做发布-订阅（Publish/Subscribe）模式、模型-视图（Model/View）模式、源-监听器（Source/Listener）模式或从属者（Dependents）模式。  

2.	Struct  
	![Observer](https://github.com/mxHoward/learning/blob/img/img/design_patterns/Observer.jpg)  
	*	Subject: 目标
	*	ConcreteSubject: 具体目标
	*	Observer: 观察者
	*	ConcreteObserver: 具体观察者  

3.	Sequence Diagram  
	![Observer SD](https://github.com/mxHoward/learning/blob/img/img/design_patterns/seq_Observer.jpg)  
4.	Code  
	```c++
	//  subject.h
	//  Definition of the Class subject
	#ifndef _SUBJECT_H_
	#define _SUBJECT_H_
	#include "obeserver.h"
	#include <vector>
	using namespace std;

	class subject
	{
	public:
		subject();
		virtual ~subject();
		observer *m_observer;

		void attach(obeserver * p_observer_);
		void detach(obeserver * p_observer_);
		void notify();
			
		virtual int get_state() = 0;
		virtual void set_state(int i)= 0;
	private:
		vector<obeserver*> mvt_obj;
	};
	#endif // ifndef _SUBJECT_H_





	//  subject.cpp
	//  Implementation of the Class subject
	#include "subject.h"

	subject::subject() {}

	subject::~subject() {}

	void subject::attach(observer * p_observer_)
	{
		mvt_obj.push_back(p_observer_);
	}

	void subject::detach(observer * p_observer_)
	{
		for(vector<observer*>::iterator itr = mvt_obj.begin();
			itr != mvt_obj.end(); itr++)
		{
			if(*itr == p_observer_)
			{
				mvt_obj.erase(itr);
				return;
			}			
		}
	}

	void subject::notify()
	{
		for(vector<observer*>::iterator itr = m_vtObj.begin();
			itr != m_vtObj.end();
		 	itr++)
		{	
			(*itr)->update(this);		
		}
	}






	//  concrete_observer.h
	//  Definition of the Class concrete_observer
	#iindef _CONCRETE_OBSERVER_H_
	#define _CONCRETE_OBSERVER_H_
	#include "observer.h"
	#include <string>
	using namespace std;

	class concrete_observer : public observer
	{
	public:
		concrete_observer(string name);
		virtual ~concrete_observer();
		virtual void update(subject * sub);

	private:
		string m_obj_name;
		int m_observer_state;
	};
	#endif // ifndef _CONCRETE_OBSERVER_H_






	//  concrete_observer.cpp
	//  Implementation of the Class concrete_observer

	#include "concrete_observer.h"
	#include <iostream>
	#include <vector>
	#include "subject.h"
	using namespace std;

	concrete_observer::concrete_observer(string name)
	{
		m_obj_name = name;
	}

	concrete_observer::~concrete_observer() {}

	void concrete_observer::update(subject * sub)
	{
		m_observer_state = sub->get_state();
		cout << "update oberserver[" << m_obj_name << "] state:" << m_observer_state << endl;
	}



	// main.cpp
	#include <iostream>
	#include "subject.h"
	#include "observer.h"
	#include "concrete_observer.h"
	#include "concrete_subject.h"

	using namespace std;

	int main(int argc, char *argv[])
	{
		subject * subject = new concrete_subject();
		observer * objA = new concrete_observer("A");
		observer * objB = new concrete_observer("B");
		subject->attach(objA);
		subject->attach(objB);
		
		subject->set_state(1);
		subject->notify();
		
		cout << "--------------------" << endl;
		subject->detach(objB);
		subject->set_state(2);
		subject->notify();
		
		delete subject;
		delete objA;
		delete objB;
			
		return 0;
	}
	```  

5.	Summary  
	*	观察者模式定义了一种一对多的依赖关系，让多个观察者对象同时监听某一个目标对象，
		当这个目标对象的状态发生变化时，会通知所有观察者对象，使它们能够自动更新。  
	*	观察者模式的主要优点在于可以实现表示层和数据逻辑层的分离，并在观察目标和观察者之间建立一个抽象的耦合，支持广播通信；  
		其主要缺点在于如果一个观察目标对象有很多直接和间接的观察者的话，将所有的观察者都通知到会花费很多时间，而且如果在观察者和观察目标之间有循环依赖的话，观察目标会触发它们之间进行循环调用，可能导致系统崩溃。  
	*	观察者模式适用情况包括：  
		一个抽象模型有两个方面，其中一个方面依赖于另一个方面；  
		一个对象的改变将导致其他一个或多个对象也发生改变，而不知道具体有多少对象将发生改变；  
		一个对象必须通知其他对象，而并不知道这些对象是谁；  
		需要在系统中创建一个触发链。  
  

### State Pattern（状态模式） ###
1.	Definition  
	状态模式(State Pattern) ：允许一个对象在其内部状态改变时改变它的行为，对象看起来似乎修改了它的类。其别名为状态对象(Objects for States)，状态模式是一种对象行为型模式。  

2.	Struct  
	![State](https://github.com/mxHoward/learning/blob/img/img/design_patterns/State.jpg)  
	*	Context: 环境类
	*	State: 抽象状态类
	*	ConcreteState: 具体状态类  

3.	Sequence Diagram  
	![State](https://github.com/mxHoward/learning/blob/img/img/design_patterns/seq_State.jpg)  

4.	Code  
	```c++
	//  context.h
	//  Definition of the Class context
	#ifndef _CONTEXT_H_
	#define _CONTEXT_H_
	#include "state.h"
	class context
	{
	public:
		context();
		virtual ~context();
		void change_state(state * st);
		void request();

	private:
		state *mp_state;
	};
	#endif // ifndef _CONTEXT_H_





	//  context.cpp
	//  Implementation of the Class context
	#include "context.h"
	#include "concrete_stateA.h"

	context::context()
	{
		mp_state = concrete_stateA::instance();
	}

	context::~context() {}

	void context::change_state(state * st)
	{
		mp_state = st;
	}

	void context::request()
	{
		mp_state->handle(this);
	}






	//  concrete_stateA.h
	//  Definition of the Class concrete_stateA
	#ifndef _CONCRETE_STATEA_H_
	#define _CONCRETE_STATEA_H_
	#include "State.h"

	class concrete_stateA : public state
	{
	public:
		virtual ~concrete_stateA();
		static state * instance();
		virtual void handle(context * c);

	private:
		concrete_stateA();
		static state * mp_state;
	};

	#endif // ifndef _CONCRETE_STATEA_H_






	//  concrete_stateA.cpp
	//  Implementation of the Class concrete_stateA
	#include "concrete_stateA.h"
	#include "concrete_stateB.h"
	#include "context.h"
	#include <iostream>
	using namespace std;

	state * concrete_stateA::mp_state = NULL;
	concrete_stateA::concrete_stateA() {}

	concrete_stateA::~concrete_stateA() {}

	state * concrete_stateA::instance()
	{
		if ( NULL == mp_state)
		{
			mp_state = new concrete_stateA();
		}
		return mp_state;
	}

	void concrete_stateA::handle(context * c)
	{
		cout << "doing something in State A.\n done,change state to B" << endl;
		c->change_state(concrete_stateB::instance());
	}





	//main.cpp
	#include <iostream>
	#include "context.h"
	#include "concrete_stateA.h"
	#include "concrete_stateB.h"

	using namespace std;

	int main(int argc, char *argv[])
	{
		char a = '0';
		if('0' == a)
			cout << "yes" << endl;
		else
			cout << "no" << endl;
		
		context * c = new context();
		c->request();
		c->request();
		c->request();

		delete c;
		return 0;
	}
	```  


5.	Summary  
	*	状态模式描述了对象状态的变化以及对象如何在每一种状态下表现出不同的行为。
	*	状态模式的主要优点在于封装了转换规则，并枚举可能的状态，它将所有与某个状态有关的行为放到一个类中，
		并且可以方便地增加新的状态，只需要改变对象状态即可改变对象的行为，还可以让多个环境对象共享一个状态对象，从而减少系统中对象的个数；  
		其缺点在于使用状态模式会增加系统类和对象的个数，且状态模式的结构与实现都较为复杂，如果使用不当将导致程序结构和代码的混乱，对于可以切换状态的状态模式不满足“开闭原则”的要求。  
	*	状态模式适用情况包括：  
		对象的行为依赖于它的状态（属性）并且可以根据它的状态改变而改变它的相关行为；  
		代码中包含大量与对象状态有关的条件语句，这些条件语句的出现，会导致代码的可维护性和灵活性变差，不能方便地增加和删除状态，使客户类与类库之间的耦合增强。  



### Strategy Pattern（策略模式） ###
1.	Definition  
	策略模式(Strategy Pattern): 定义一系列算法，将每一个算法封装起来，并让它们可以相互替换。策略模式让算法独立于使用它的客户而变化。
2.	Struct  
	![State](https://github.com/mxHoward/learning/blob/img/img/design_patterns/Strategy.jpg)  
	*	Context: 环境类
	*	Strategy: 抽象策略类
	*	ConcreteStrategy: 具体策略类  

3.	Sequence Diagram  
	![State](https://github.com/mxHoward/learning/blob/img/img/design_patterns/seq_Strategy.jpg)  

4.	Code  
	```c++
	//  context.h
	//  Definition of the Class context
	#ifndef _CONTEXT_H_
	#define _CONTEXT_H_
	#include "strategy.h"
	class context
	{

	public:
		context();
		virtual ~context();
		

		void algorithm();
		void set_strategy(strategy* st);
		
	private:
		strategy *mp_strategy;

	};
	#endif // ifndef_CONTEXT_H_






	//  context.cpp
	//  Implementation of the Class context
	#include "context.h"

	context::context() {}

	context::~context() {}

	void context::algorithm()
	{
		mp_strategy->algorithm();
	}

	void context::set_strategy(strategy* st)
	{
		mp_strategy = st;
	}







	//  concrete_strategyA.h
	//  Definition of the Class concrete_strategyA
	#ifndef _CONCRETE_STRATEGYA_H_
	#define _CONCRETE_STRATEGYA_H_
	#include "strategy.h"

	class concrete_strategyA : public strategy
	{
	public:
		concrete_strategyA();
		virtual ~concrete_strategyA();

		virtual void algorithm();

	};
	#endif // ifndef _CONCRETE_STRATEGYA_H_







	//  concrete_strategyA.cpp
	//  Implementation of the Class concrete_strategyA

	#include "concrete_strategyA.h"
	#include <iostream>
	using namespace std;

	concrete_strategyA::concrete_strategyA() {}

	concrete_strategyA::~concrete_strategyA() {}

	void concrete_strategyA::algorithm()
	{
		cout << "use algorithm A" << endl;
	}




	// main.cpp
	#include <iostream>
	#include "context.h"
	#include "concrete_strategyA.h"
	#include "concrete_strategyB.h"
	#include "strategy.h"
	#include <vector>
	using namespace std;

	int main(int argc, char *argv[])
	{
		strategy * s1 = new concrete_strategyA();
		context * cxt = new context();
		cxt->set_strategy(s1);
		cxt->algorithm();

		strategy *s2 = new concrete_strategyB();
		cxt->set_strategy(s2);
		cxt->algorithm();
		
		delete s1;
		delete s2;
		
		int rac1 = 0x1;
		int rac2 = 0x2;
		int rac3 = 0x4;
		int rac4 = 0x8;
		
		int i = 0xe;
		int j = 0x5;

		int r1 = i & rac1;
		int r2 = i & rac2;
		int r3 = i & rac3;
		int r4 = i & rac4;
		
		cout <<"res:" << r1 << "/" << r2 << "/" << r3 << "/" << r4 << endl;
		
		return 0;
	}
	```  
  

5.	Summary  
	*	策略模式是对算法的封装，它把算法的责任和算法本身分割开，委派给不同的对象管理。
		策略模式通常把一个系列的算法封装到一系列的策略类里面，作为一个抽象策略类的子类。
	*	策略模式主要优点在于对“开闭原则”的完美支持，在不修改原有系统的基础上可以更换算法或者增加新的算法，
		它很好地管理算法族，提高了代码的复用性，是一种替换继承，避免多重条件转移语句的实现方式；  
		其缺点在于客户端必须知道所有的策略类，并理解其区别，同时在一定程度上增加了系统中类的个数，可能会存在很多策略类。  
	*	策略模式适用情况包括：  
		在一个系统里面有许多类，它们之间的区别仅在于它们的行为，使用策略模式可以动态地让一个对象在许多行为中选择一种行为；  
		一个系统需要动态地在几种算法中选择一种；避免使用难以维护的多重条件选择语句；  
		希望在具体策略类中封装算法和与相关的数据结构。  


