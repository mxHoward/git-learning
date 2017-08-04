# Creational Patterns #
  
　　创建型模式(Creational Pattern)对类的实例化过程进行了抽象，能够将软件模块中对象的创建和对象的使用分离。为了使软件的结构更加清晰，外界对于这些对象只需要知道它们共同的接口，而不清楚其具体的实现细节，使整个系统的设计更加符合单一职责原则。创建型模式隐藏了类的实例的创建细节，通过隐藏对象如何被创建和组合在一起达到使整个系统独立的目的。  

## Simple Factory Pattern（简单工厂模式） ##
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

## Factory Method Pattern（工厂方法模式） ##
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


## Abstract Factory Pattern（抽象工厂模式） ##
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


## Builder Pattern（建造者模式） ##
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


## Singleton（单例模式） ##
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

