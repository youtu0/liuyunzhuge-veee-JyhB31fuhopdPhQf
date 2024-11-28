
## 原型模式（Prototype Pattern）


**原型模式的核心思想是通过复制（克隆）现有对象来创建新对象**。


原型模式通常涉及两个角色：原型对象和具体原型对象。原型对象是需要被复制的对象，而具体原型对象是实现了克隆方法的原型对象。


在Java中，原型模式通常通过实现`Cloneable`接口和重写`clone()`方法来实现。当需要创建新对象时，可以直接调用原型对象的`clone()`方法来得到一个新的对象副本，而无需调用构造函数或者暴露对象创建的具体细节。


原型模式在一些场景中非常有用，例如：


* 简化对象创建：在对象的构造过程比较复杂或时，使用原型模式可以很容易地复制整个对象结构，而不需要关心对象的具体组成和组装方式。
* 隐藏具体实现：客户端可以针对抽象接口进行编程，而不需要知道具体的实现细节。
* 通过为每个线程创建 **独立的对象实例** ，来避免不同线程对该对象的操作可能会产生数据冲突或者不一致。
* 在某些情况下，对象的创建过程可能是受限的，而原型模式可以绕过这些限制。


## 简单案例


**案例概述**


简单实现原型模式，观察原型创建的对象是不是同一个对象，内容是否相同



```
// 原型对象和具体原型对象
public class ConcretePrototype implements Cloneable {
    private String name;

    public ConcretePrototype(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }

    @Override
    public ConcretePrototype clone() {
        try {
            return (ConcretePrototype) super.clone();
        } catch (CloneNotSupportedException e) {
            e.printStackTrace();
            return null;
        }
    }

}

// 测试代码
class PrototypePatternDemo {
    public static void main(String[] args) {
        ConcretePrototype original = new ConcretePrototype("Prototype1");
        // 克隆出新对象
        ConcretePrototype cloned = original.clone();
        // 观察输出的地址和内容
        System.out.println(original+"|"+"Original: " + original.getName());
        System.out.println(cloned+"|"+"Cloned: " + cloned.getName());
    }
}

```

测试结果：


![image](https://img2024.cnblogs.com/blog/1209017/202411/1209017-20241127224641140-693764274.jpg)


上面的简单案例是属于原型模式中的`浅拷贝`，除此之外原型模式还有`深拷贝`。


## 浅拷贝


**浅拷贝**：创建的新对象和原始对象的基本数据类型属性会被直接复制，而对于引用类型的属性，原始对象和克隆对象会共享相同的引用。所以，如果克隆对象和原始对象共享了某些引用类型的属性，对其中一个对象的修改会影响到另一个对象。


### 浅拷贝示例



```
public class ShallowCopy {

    public static void main(String[] args) throws CloneNotSupportedException {
        Address addr = new Address("New York");
        Person p1 = new Person("John", 25, addr);
        System.out.println("复制修改前--地址："+ p1 + "|" + p1.name + " - " + p1.address.city); // John - New York

        Person p2 = (Person) p1.clone();
        p2.name = "Denny";
        p2.address.city = "Los Angeles";  // 修改了引用类型的属性
        // 引用对象的值被修改后原始对象的值也发生了改变
        System.out.println("原始对象地址："+ p1 + "|" + p1.name + " - " + p1.address.city); // John - Los Angeles
        System.out.println("复制对象地址："+ p2 + "|" + p2.name + " - " + p2.address.city); // Denny - Los Angeles
    }
}

class Person implements Cloneable {
    String name;
    int age;
    Address address; // 引用类型

    public Person(String name, int age, Address address) {
        this.name = name;
        this.age = age;
        this.address = address;
    }

    @Override
    protected Object clone() throws CloneNotSupportedException {
        return super.clone(); // 浅拷贝
    }
}

class Address {
    String city;

    public Address(String city) {
        this.city = city;
    }
}

```

测试结果：


在这个例子中，`p1` 和 `p2` 是两个不同的 `Person` 对象，但是它们共享同一个 `Address` 对象的引用。修改 `p2` 的 `address` 属性时，也会影响到 `p1`。


![image](https://img2024.cnblogs.com/blog/1209017/202411/1209017-20241127224627688-806420399.jpg)


## 深拷贝


**深拷贝**：创建的新对象不仅会复制原对象的基本数据类型属性，还会递归地复制对象中所有引用类型的属性。深拷贝确保原始对象和克隆对象之间完全独立，修改一个对象不会影响另一个对象。


### 深拷贝方式1\-\-一个个手动克隆


对应字段为引用对象的进行一个个实例化新对象并拷贝对象数据，或者层层往下clone()。适用于，引用对象少的情况。



```

public class DeepCopy {
    public static void main(String[] args) throws CloneNotSupportedException {
        Address addr = new Address("New York");
        Person p1 = new Person("John", 25, addr);
        System.out.println("复制修改前--地址："+ p1 + "|" + p1.name + " - " + p1.address.city); // John - New York

        Person p2 = (Person) p1.clone();
        p2.name = "Denny";
        p2.address.city = "Los Angeles";  // 修改了引用类型的属性
        // 引用对象的值被修改后原始对象的值--不受影响
        System.out.println("原始对象地址："+ p1 + "|" + p1.name + " - " + p1.address.city); // John - Los Angeles
        System.out.println("复制对象地址："+ p2 + "|" + p2.name + " - " + p2.address.city); // Denny - Los Angeles
    }
}
class Person implements Cloneable {
    String name;
    int age;
    Address address; // 引用类型

    public Person(String name, int age, Address address) {
        this.name = name;
        this.age = age;
        this.address = address;
    }

    @Override
    protected Object clone() throws CloneNotSupportedException {
        Person cloned = (Person) super.clone();
        cloned.address = new Address(this.address.city); // 手动克隆引用类型的属性
        return cloned;
    }
}

class Address {
    String city;

    public Address(String city) {
        this.city = city;
    }
}


```

测试结果：


在这个例子中，`p1` 和 `p2` 是两个独立的 `Person` 对象，且它们的 `address` 属性是独立的。修改 `p2` 的 `address` 不会影响 `p1`。


![image](https://img2024.cnblogs.com/blog/1209017/202411/1209017-20241127224610107-1938081632.jpg)


### 深拷贝方式2\-\-序列化


序列化是将对象转换为字节流的过程，而反序列化则是将字节流转换回对象。通过序列化和反序列化，可以实现在不依赖于类构造器的情况下创建一个完全独立的对象副本。这种方式适用于对象较为`复杂且具有多个引用`的情况。



```
package org.example.prototype;

import java.io.*;

public class DeepCopySerial {

    public static void main(String[] args) {
        Address addr = new Address("New York");
        Person p1 = new Person("Jack", 25, addr);
        System.out.println("原始对象地址："+ p1 + "|" + p1.name + " - " + p1.address.city); // Jack - New York

        // 使用深拷贝方法复制对象
        Person p2 = p1.deepClone();
        p2.name = "Denny";
        p2.address.city = "Los Angeles";  // 修改引用类型的属性

        // 打印两个对象的状态
        System.out.println("原始对象地址："+ p1 + "|" + p1.name + " - " + p1.address.city); // Jack - New York
        System.out.println("复制对象地址："+ p2 + "|" + p2.name + " - " + p2.address.city); // Denny - Los Angeles
    }
}

class Address implements Serializable {
    String city;

    public Address(String city) {
        this.city = city;
    }
}

class Person implements Serializable {
    String name;
    int age;
    Address address;

    public Person(String name, int age, Address address) {
        this.name = name;
        this.age = age;
        this.address = address;
    }

    // 序列化深拷贝方法
    public Person deepClone() {
        try {
            // 将当前对象序列化到字节数组流
            ByteArrayOutputStream byteOutStream = new ByteArrayOutputStream();
            ObjectOutputStream out = new ObjectOutputStream(byteOutStream);
            out.writeObject(this);

            // 从字节数组流反序列化为新对象
            ByteArrayInputStream byteInStream = new ByteArrayInputStream(byteOutStream.toByteArray());
            ObjectInputStream in = new ObjectInputStream(byteInStream);
            return (Person) in.readObject();
        } catch (IOException | ClassNotFoundException e) {
            e.printStackTrace();
            return null;
        }
    }
}


```

测试结果：


![image](https://img2024.cnblogs.com/blog/1209017/202411/1209017-20241127224543126-1880139760.jpg)


**优点：**


* **简便和通用**：无论对象的深度如何复杂，序列化和反序列化都可以正确地复制对象。
* **无需手动处理每个字段**：不需要像手动实现深拷贝那样去关心对象的字段及其引用关系。


**缺点：**


* **性能开销**：序列化和反序列化过程相对较慢，尤其是对于大型对象。
* **依赖Serializable**：对象必须实现 `Serializable` 接口，如果类没有实现这个接口，无法进行序列化。


### 深拷贝方式3\-\-第三方库


使用第三方库进行拷贝，比如`Apache Commons Lang` 提供了 `SerializationUtils.clone()` 方法，这个方法通过序列化来实现深拷贝，使用起来非常简便。



```
import org.apache.commons.lang3.SerializationUtils;
import java.io.Serializable;

public class DeepCopyWithLibrary {
    public static void main(String[] args) {
        Address addr = new Address("New York");
        Person p1 = new Person("Jack", 25, addr);
        System.out.println("原始对象地址："+ p1 + "|" + p1.name + " - " + p1.address.city); // Jack - New York

        // 使用 Apache Commons 库进行深拷贝
        Person p2 = SerializationUtils.clone(p1);

        p2.name = "Denny";
        p2.address.city = "Los Angeles";  // 修改引用类型的属性

        // 打印两个对象的状态
        System.out.println("原始对象地址："+ p1 + "|" + p1.name + " - " + p1.address.city); // Jack - New York
        System.out.println("复制对象地址："+ p2 + "|" + p2.name + " - " + p2.address.city); // Denny - Los Angeles
    }
}


class Address implements Serializable {
    String city;

    public Address(String city) {
        this.city = city;
    }
}

class Person implements Serializable {
    String name;
    int age;
    Address address;

    public Person(String name, int age, Address address) {
        this.name = name;
        this.age = age;
        this.address = address;
    }
}

```

测试结果和序列化类似。


## 看看JDK源码怎么写的


java.util.Calendar类的拷贝源码，这里说的就是对应方式1，一个个手动克隆，通过new的方式和层层调用clone()方法`other.zone = (TimeZone) zone.clone();`来实现。



```
public Object clone()
    {
        try {
            Calendar other = (Calendar) super.clone();

            other.fields = new int[FIELD_COUNT];
            other.isSet = new boolean[FIELD_COUNT];
            other.stamp = new int[FIELD_COUNT];
            for (int i = 0; i < FIELD_COUNT; i++) {
                other.fields[i] = fields[i];
                other.stamp[i] = stamp[i];
                other.isSet[i] = isSet[i];
            }
            other.zone = (TimeZone) zone.clone();
            return other;
        }
        catch (CloneNotSupportedException e) {
            // this shouldn't happen, since we are Cloneable
            throw new InternalError(e);
        }
    }

```

java.util.Date类也类似



```
public Object clone() {
 Date d = null;
 try {
  d = (Date)super.clone();
  if (cdate != null) {
   d.cdate = (BaseCalendar.Date) cdate.clone();
  }
 } catch (CloneNotSupportedException e) {} // Won't happen
 return d;
}

```

java.util.concurrent.ConcurrentSkipListSet类，通过new的方式来实现



```
public ConcurrentSkipListSet clone() {
 try {
  @SuppressWarnings("unchecked")
  ConcurrentSkipListSet clone =
   (ConcurrentSkipListSet) super.clone();
  clone.setMap(new ConcurrentSkipListMapObject>(m));
  return clone;
 } catch (CloneNotSupportedException e) {
  throw new InternalError();
 }
}

```

还有很多很多，可以自行去查看学习。


那Spring框架怎么使用原型模式的呢？


## 在Spring框架的应用？？


在Spring容器中，当应用程序需要获取一个`原型作用域的Bean`时，Spring容器会调用相应的BeanFactory来创建该Bean的实例。在这种情况下，实际上是使用了`工厂模式`来创建Bean的实例，而`不是原型设计模式`。


具体来说，Spring框架中的`BeanFactory`是一种工厂模式的实现，它负责根据配置信息和需求来创建和管理Bean实例。当我们调用`getBean()`方法来获取Bean时，实际上是委托给了Spring容器中的BeanFactory来创建一个新的实例对象。每次调用`getBean()`方法都会导致BeanFactory创建一个新的实例对象，这与工厂模式的特征相符。


在Spring中，原型作用域的Bean确实是在每次调用`getBean()`方法时都会创建一个新的实例对象，这与原型设计模式的概念相似。但是在Spring框架中，并没有直接使用原型设计模式来实现原型作用域的Bean，而是通过工厂模式来实现的。



```
@Bean
@Scope("prototype") // 这里的原型作用域其实是通过工厂模式创建新实例
public MyBean myPrototypeBean() {
    return new MyBean();
}

```

虽然不是使用原型模式，但同样可以实现原型模式编程的核心思想，并且更容易与 Spring 容器的其他功能结合。比如深拷贝方式2（序列化）并没有实现`Cloneable`接口下的`clone()`方法，但同样也可以实现原型模式一样。


## 总结


我可以实现你提倡的思想，但不一定要走你所说的道路。


还是这句话：**设计模式不是简单地将一个固定的代码框架套用到项目中，而是一种`严谨的编程思想`，旨在提供解决特定问题的经验和指导。**


![](https://mmbiz.qpic.cn/sz_mmbiz_gif/7Lm51TENjPvf3H8pfO0H2PZIbnf9wlqswianUspot3NicoZ5yGk9MOFGOfEtvtDMVCRm3tmhL5SRYHBP7ofoQw7g/640?wx_fmt=gif&wxfrom=5&wx_lazy=1&wx_co=1&tp=webp)


 [超实用的SpringAOP实战之日志记录](https://github.com)


[软考中级\-\-软件设计师毫无保留的备考分享](https://github.com)


[单例模式及其思想](https://github.com)


[2023年下半年软考考试重磅消息](https://github.com)


[通过软考后却领取不到实体证书？](https://github.com):[樱花宇宙官网](https://yzygzn.com)


[计算机算法设计与分析（第5版）](https://github.com)


[Java全栈学习路线、学习资源和面试题一条龙](https://github.com)


[软考证书\=职称证书？](https://github.com)


[什么是设计模式？](https://github.com)



