# Behavioral Patterns #
  
　　行为型模式(Behavioral Pattern)是对在不同的对象之间划分责任和算法的抽象化。行为型模式不仅仅关注类和对象的结构，而且重点关注它们之间的相互作用。通过行为型模式，可以更加清晰地划分类与对象的职责，并研究系统在运行时实例对象之间的交互。在系统运行时，对象并不是孤立的，它们可以通过相互通信与协作完成某些复杂功能，一个对象在运行时也将影响到其他对象的运行。

---
## Command Pattern（命令模式） ##
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


## Mediator Pattern（中介者模式） ##
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



## Observer Pattern（观察者模式） ##
1.	Definition  
	观察者模式(Observer Pattern)：定义对象间的一种一对多依赖关系，使得每当一个对象状态发生改变时，其相关依赖对象皆得到通知并被自动更新。观察者模式又叫做发布-订阅（Publish/Subscribe）模式、模型-视图（Model/View）模式、源-监听器（Source/Listener）模式或从属者（Dependents）模式。  

2.	Struct  
	![Observer](https://github.com/mxHoward/learning/blob/master/img/design_patterns/Observer.jpg)  
	*	Subject: 目标
	*	ConcreteSubject: 具体目标
	*	Observer: 观察者
	*	ConcreteObserver: 具体观察者  

3.	Sequence Diagram  
	![Observer SD](https://github.com/mxHoward/learning/blob/master/img/design_patterns/seq_Observer.jpg)  
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
  

## State Pattern（状态模式） ##
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


## Strategy Pattern（策略模式） ##
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


