## **适配器VS装饰者VS桥接VS代理VS外观** {#适配器-vs-装饰者-vs-桥接-vs-代理-vs-外观}

### [**适配器模式**](http://blog.csdn.net/self_study/article/details/51585664)（电源插头/Adapter） {#适配器模式}

适配器模式是将两个不兼容的类融合在一起

![](/assets/importshi_pei.png)

### [**装饰者模式**](http://blog.csdn.net/self_study/article/details/51591709)（窗口横屏或竖屏/Activity） {#装饰者模式}

在结构上类似于代理模式，但是不同的是代理模式目的是提供访问控制，而装饰模式是给一个对象动态的添加一些属性，为对象添加行为。

![](/assets/import_zhuangshi.png)

### [**代理模式**](http://blog.csdn.net/self_study/article/details/51628486)\(明星与经纪人/Binder\) {#代理模式}

代理模式是为另一个对象提供代表，以便控制客户对象的访问。

![](/assets/import_daili.png)

### [**外观模式**](http://blog.csdn.net/self_study/article/details/51931196)\(第三方SDK\) {#外观模式}

外观模式是采用提供一个统一的接口，来访问一群子系统的一群接口。。适配器模式的意图是，“改变”接口符合客户的期望；而

外观模式的意图是，提供子系统的一个简化接口。

![](/assets/import_waiguan.png)

### [**桥接模式**](http://blog.csdn.net/self_study/article/details/51622243)\(汽车与轮子\) {#桥接模式}

桥接模式是为了将抽象与实现部分分离，使他们可以独立进行实现。二者为两个不同部分，没有实现同一个接口，这是与装饰模式，代理模式最大区别。

![](/assets/import_qiaojie.png)

