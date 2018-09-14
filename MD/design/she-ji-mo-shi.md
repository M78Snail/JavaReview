## **设计模式分类** {#设计模式分类}

### **创建型模式** {#创建型模式}

[Abstract Factory（抽象工厂模式）：](http://blog.csdn.net/self_study/article/details/51472885)为创建一组相关或者是相互依赖的对象提供一个接口，而不需要制定它们的具体类。  
[Builder（建造者模式）：](http://blog.csdn.net/self_study/article/details/51707029)将一个复杂对象的构建和它的表示分离，使得同样的构建过程可以创建不同的表示。  
[Factory Method（工厂方法模式）：](http://blog.csdn.net/self_study/article/details/51419770)定义一个创建对象的接口，让子类决定实例化哪个类。  
[Prototype（原型模式）：](http://blog.csdn.net/self_study/article/details/51757525)用原型实例指向创建对象的种类，并通过拷贝这些原型创建新的对象。  
[Singleton（单例模式）：](http://blog.csdn.net/self_study/article/details/50835410)确保某一个类只有一个实例，而且自行实例化并向整个系统提供这个实例。

### **结构型模式** {#结构型模式}

[Adapter（适配器模式）：](http://blog.csdn.net/self_study/article/details/51585664)适配器模式把一个类的接口换成客户端所期待的另一种接口，从而使原本因接口不匹配而无法在一起工作的两个类能够在一起工作。  
[Bridge（桥接模式）：](http://blog.csdn.net/self_study/article/details/51622243)将抽象部分与实现部分分离，使他们都可以独立地进行变化。  
[Composite（组合模式）：](http://blog.csdn.net/self_study/article/details/51761709)组合模式允许你将对象组合成树形结构来表现“整体/部分”层次结构，并且能够让客户端以一致的方式处理个别对象以及组合对象。  
[Decorator（装饰者模式）：](http://blog.csdn.net/self_study/article/details/51591709)动态地将职责附加到对象上，对于扩展功能来说，装饰者提供了有别于继承的另一种选择。  
[Facade（外观模式）：](http://blog.csdn.net/self_study/article/details/51931196)外观模式提供一个统一的接口，用来访问子系统中的一群接口，外观定义了一个高层接口，让子系统更容易使用。  
[Flyweight（享元模式）：](http://blog.csdn.net/self_study/article/details/51870660)使用共享对象可有效地支持大量细粒度的对象。  
[Proxy（代理模式）：](http://blog.csdn.net/self_study/article/details/51628486)为另一个对象提供一个代理以控制对这个对象的访问。

### **行为型模式** {#行为型模式}

[Chain of responsibility（责任链模式）：](http://blog.csdn.net/self_study/article/details/52012370)使多个对象都有机会处理请求，从而避免了请求的发送者和接收者之间的耦合关系，将这些对象连成一条链，并沿着这条链传递该请求，直到有对象处理它为止。  
[Command（命令模式）：](http://blog.csdn.net/self_study/article/details/52091539)将一个请求封装成一个对象，从而让用户使用不同的请求把客户端参数化；对请求排队或者记录请求日志，以及支持可撤销的操作。  
[Interpreter（解释器模式）：](http://blog.csdn.net/self_study/article/details/52737559)给定一个语言，定义它的文法的一种表示，并定义一个解释器，该解释器使用该表示来解释语言中的句子。  
[Iterator（迭代器模式）：](http://blog.csdn.net/self_study/article/details/52502709)提供一种方法顺序访问一个聚合对象中的各个元素，而又不暴露其内部的表示。  
[Mediator（中介者模式）：](http://blog.csdn.net/self_study/article/details/52344610)包装了一系列对象相互作用的方式，使得这些对象不必相互明显作用，从而使耦合松散，而且可以独立地改变它们之间的交互。  
[Memento（备忘录模式）：](http://blog.csdn.net/self_study/article/details/52561728)在不破坏封装的前提下，捕捉一个对象的内部状态，并在该对象之外保存这个状态，这样，以后就可将该对象恢复到原先保存的状态。  
[Observer（观察者模式）：](http://blog.csdn.net/self_study/article/details/51346849)定义对象间一对多的依赖关系，使得每当一个对象改变状态，则所有依赖于它的对象都会得到通知并被自动更新。  
[State（状态模式）：](http://blog.csdn.net/self_study/article/details/52432486)当一个对象的内在状态改变时允许改变其行为，这个对象看起来像是改变了其类。  
[Strategy（策略模式）：](http://blog.csdn.net/self_study/article/details/52248437)策略模式定义了一系列的算法，并将每一个算法封装起来，而且使他们可以相互替换，让算法独立于使用它的客户而独立变化。  
[Template method（模板方法模式）：](http://blog.csdn.net/self_study/article/details/52662896)定义一个操作中的算法的框架，而将一些步骤延迟到子类中，使得子类可以不改变一个算法的结构即可重新定义该算法的某些特定步骤。  
[Visitor（访问者模式）：](http://blog.csdn.net/self_study/article/details/52778713)封装一些作用于某种数据结构中的个元素的操作，它可以在不改变这个数据结构的前提下定义作用于这些元素的新的操作。

### **其他特殊模式** {#其他特殊模式}

[Object Pool（对象池模式）：](http://blog.csdn.net/self_study/article/details/51477002)维护一定数量的对象集合，使用时直接向对象池申请，以跳过对象的 expensive initialization 。

  


