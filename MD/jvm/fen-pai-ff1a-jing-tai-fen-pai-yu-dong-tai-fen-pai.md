要理解分派，我们先来理解Java中的两种类型：

* 静态类型：变量声明时的类型
* 实际类型：变量实例化时采用的类型 举例的Java代码如下：

```java
class Car {}  
class Bus extends Car {}  
public class Main {  
    public static void main(String[] args) throws Exception {  
        // Car 为静态类型，Bus 为实际类型  
        Car car = new Bus();  
    }  
}
```

**静态分派**

所有依赖静态类型来定位方法执行版本的分派动作称为静态分派，其典型应用是方法重载（重载是通过参数的静态类型而不是实际类型来选择重载的版本的）。

举例Java代码如下：

```java
class Car {}  
class Bus extends Car {}  
class Jeep extends Car {}  
public class Main {  
    public static void main(String[] args) throws Exception {  
        // Car 为静态类型，Car 为实际类型  
        Car car1 = new Car();  
        // Car 为静态类型，Bus 为实际类型  
        Car car2 = new Bus();  
        // Car 为静态类型，Jeep 为实际类型  
        Car car3 = new Jeep();  

        showCar(car1);  
        showCar(car2);  
        showCar(car3);  
    }  
    private static void showCar(Car car) {  
        System.out.println("I have a Car !");  
    }  
    private static void showCar(Bus bus) {  
        System.out.println("I have a Bus !");  
    }  
    private static void showCar(Jeep jeep) {  
        System.out.println("I have a Jeep !");  
    }  
}
```

代码输出如下：

```
I have a Car !
I have a Car !
I have a Car !
```

从上面的例子我们可以看出重载调用的具体方法版本是由静态类型来决定的。

**动态分派**

与静态分派类似，动态分派指在在运行期根据实际类型确定方法执行版本，其典型应用是方法重写（即多态）。

举例Java代码如下：

```java
class Car {  
    public void showCar() {  
        System.out.println("I have a Car !");  
    }  
}  
class Bus extends Car {  
    public void showCar() {  
        System.out.println("I have a Bus !");  
    }  
}  
class Jeep extends Car {  
    public void showCar() {  
        System.out.println("I have a Jeep !");  
    }  
}  
public class Main {  
    public static void main(String[] args) throws Exception {  
        // Car 为静态类型，Car 为实际类型  
        Car car1 = new Car();  
        // Car 为静态类型，Bus 为实际类型  
        Car car2 = new Bus();  
        // Car 为静态类型，Jeep 为实际类型  
        Car car3 = new Jeep();  

        car1.showCar();  
        car2.showCar();  
        car3.showCar();  
    }  
}
```

运行结果如下：

```java
I have a Car !
I have a Bus !
I have a Jeep !
```

可以看出来重写是一个根据实际类型决定方法版本的动态分派过程。

