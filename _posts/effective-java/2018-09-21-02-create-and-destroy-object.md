[《Effective Java》](effective-java.html)

# 02 创建和销毁对象

## 01 考虑用静态工厂方法替代构造器

* 优势
   * 他们有名称
   * 不必在每次调用的时候都创建一个新对象
   * 可以返回任何原返回类型的子类型实例
   * 使用参数化类型时代码更简洁，下面是例子，不过 Java7 之后就不用了
   
```java
public static <K, V> HashMap<K, V> newInstance() {
    return new HashMap<K, V>();
}

Map<String, List<String>> m = HashMap.newInstance();
```

* 缺点
  * 如果不含公有或者受保护的构造器，就不能被子类化
  * 与其他静态方法没有任何区别，可以用特定的命名规范予以区分
  
## 02 遇到多个构造器参数时要考虑使用构建器

* 多个参数，并且一些参数是可选的时，考虑使用构建器，有如下优势
  * 相比定义多个构造器函数，可读性更好
  * 相比使用 Setter，可以构建不可变对象
  * 可用来实现抽象工厂，相比使用 Class 对象的方式，无需处理 newInstance() 抛出的异常，而且 newInstance() 没有编译器检查
* 缺点
  * 代码更多些，不过一般都是自动生成了
  * 生成 Builder 对象有一定开销，不过一般不用在意
* 注意，最好一开始就决定是否使用 Builder，不然构造器，工厂方法，Builder 等会混在一起

## 03 用私有构造器或者枚举类型强化 Singleton 属性

* 静态 Final 域或者静态工厂方法都可以实现，但是在序列化时要注意，提供一个 readResolve 方法，并将所有实例域标记为 transient 的
* 1.5 之后，还可以用单实例枚举实现
  * 更简洁
  * 无偿提供了序列化机制
  * 绝对防止多次序列化，即使在面对复杂的序列化或者反射攻击的时候

```java

public enum Elvis {
  INSTANCE;
  
  public void leaveTheBuilding() {...}
}
```

## 04 通过私有构造器强化不可以实例化的能力

* 这样的类不能被实例化，也不能被继承

## 05 避免创建不必要的对象

* 不要这样做 `String s = new String("abc")`
* 优先使用静态方法而不是构造器，每次使用构造器都会创建一个对象
* 原来 `Calendar` 实例创建的代价是比较昂贵的
* 避免不必要的创建多个对象实例
* 注意自动装箱和自动拆箱
* 过犹不及，除非对象创建非常昂贵，否则不要自己实现对象池

## 06 消除过期的对象引用

```java
public Object pop() {
    if (size == 0)
        throw new EmptyStackException();
    Object result = elements[--size];
    elements[size] = null; // 这行代码消除过期的对象引用，防止内存泄漏
    return result;
}
```

* 内存泄漏的几种常见情况
  * 自己管理内存的对象都要小心内存泄漏问题
  * 缓存
    * 如果缓存外部存在某个对象的引用，这个项就有意义，可以使用 [WeakHashMap](weak-hash-map.html) 代表缓存
    * 复杂的缓存，使用 `java.lang.ref`
  * 监听器和其他回调，注册回调却没有显示的取消回调
    * 使用弱引用可以确保回调利济北当做垃圾进行回收
    
## 07 避免使用终结方法

* finalize 方法不能保证被及时执行，各个虚拟机的实现方式不相同
* 性能差
* 关闭资源应该使用 try...finally 块
* 两种可以使用 finalize 方法的情况
  * 如果客户端忘记调用显示的 close 方法关闭资源，finalize 可以充当安全网，注意打日志，让客户端注意到这是一个问题
  * 本地对等体（没看懂，不知道啥意思，跟本地方法有关）
* 注意，子类必须显示调用父类的 finalize 方法
