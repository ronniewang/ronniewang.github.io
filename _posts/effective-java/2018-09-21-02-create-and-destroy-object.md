[《Effective Java》](effective-java.html)

# 02 创建和销毁对象

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

