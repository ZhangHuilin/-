# Java设计模式之单例模式

`java` 中有23中设计模式

单例设计模式：解决一个类在内存中只存在一个对象

步骤：

1. 将构造函数私有化
2. 在类中创建一个本类对象
3. 提供一个方法可以获取到该对象

```java
// 写法一；先初始化对象，称为：饿汉式
class Single {
    private Single() {} 
    private static Single s = new Single();
    public static Single getInstance() {
        return s;
    }
}

class SingleDemo {
    public static void main(Stringp[] args) {
        Single ss = Single.getInstance();
    }
}
```



```java
// 写法二；对象是方法被调用时才初始化，也叫作对象的延时加载，称为：懒汉式
class Single {
    private static Single s = null;
    private Single() {}
    public static Single getInstance() {
        if(s == null)
            s = new Single();
        return s;
    }
}
```



比如一个软件的设计文件就是单例设计模式。



一般使用写法一。因为如果程序A在判断 s == null 后中断了，此时程序B 也会判断  s == null。这样可能程序A、B都会创建对象。



