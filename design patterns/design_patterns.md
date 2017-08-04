# Design Patterns #
---
## Creational Patterns（创建型模式） ##
---
创建型模式(Creational Pattern)对类的实例化过程进行了抽象，能够将软件模块中对象的创建和对象的使用分离。为了使软件的结构更加清晰，外界对于这些对象只需要知道它们共同的接口，而不清楚其具体的实现细节，使整个系统的设计更加符合单一职责原则。创建型模式隐藏了类的实例的创建细节，通过隐藏对象如何被创建和组合在一起达到使整个系统独立的目的。
*	Simple Factory Pattern（简单工厂模式）
	1.	Definition.
		简单工厂模式(Simple Factory Pattern)，又称为静态工厂方法(Static Factory Method)模式，它属于类创建型模式。在简单工厂模式中，可以根据参数的不同返回不同类的实例。简单工厂模式专门定义一个类来负责创建其他类的实例，被创建的实例通常都具有共同的父类。
	2.	//todo...
*	Factory Method Pattern（工厂方法模式）
*	Abstract Factory Pattern（抽象工厂模式）
*	Builder Pattern（建造者模式）
*	Singleton（单例模式）

## Structural Patterns（结构型模式） ##
---
结构型模式(Structural Pattern)描述如何将类或者对 象结合在一起形成更大的结构，就像搭积木，可以通过 简单积木的组合形成复杂的、功能更为强大的结构。
*	Adapter Pattern（适配器模式）
*	Bridge Pattern（桥接模式）
*	Decorator Pattern（装饰模式）
*	Facade Pattern（外观模式）
*	Flyweight Pattern（享元模式）
*	Proxy Pattern（代理模式）
## Behavioral Patterns（行为型模式） ##
---
行为型模式(Behavioral Pattern)是对在不同的对象之间划分责任和算法的抽象化。行为型模式不仅仅关注类和对象的结构，而且重点关注它们之间的相互作用。通过行为型模式，可以更加清晰地划分类与对象的职责，并研究系统在运行时实例对象之间的交互。在系统运行时，对象并不是孤立的，它们可以通过相互通信与协作完成某些复杂功能，一个对象在运行时也将影响到其他对象的运行。
*	Command Pattern（命令模式）
*	Mediator Pattern（中介者模式）
*	Observer Pattern（观察者模式）
*	State Pattern（状态模式）
*	Strategy Pattern（策略模式）