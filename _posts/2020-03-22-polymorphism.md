# 多态

今天翻看了一下《Java 编程思想》多态一章，对一些事情有了新的理解，记录一下

多态分离了`做什么`和`怎么做`

实现多态需要动态绑定的能力，也就是在运行期能识别出调用对象的实际类型，Java 中通过 RTTI 实现的

Java 中除了 static 方法（构造器是 static 方法）和 final 方法（private 方法也是一种 final 方法）之外，其他方法都是动态绑定

在子类的方法中，注意不要使用基类中 private 方法的名字，容易引起混淆，虽然同名，但是不具有多态行为

用尽可能简单的方法使对象进入正常状态，如果可以的话，避免调用其他方法。在构造器内唯一能够安全调用的那些方法是基类中的 final 方法

为什么有上述那条忠告呢，看下面的代码

```java
package thinkinjava.chapter8;

class Glyph {
    void draw() {
        System.out.println("Glyph draw()");
    }

    public Glyph() {
        System.out.println("Glyph() before draw()");
        draw();
        System.out.println("Glyph() after draw()");
    }
}

class RoundGlyph extends Glyph {
    private int radius = 1;

    {
        System.out.println("set radius = 1");
        System.out.println("this.radius = " + this.radius);
    }

    public RoundGlyph(int radius) {
        this.radius = radius;
        System.out.println("RoundGlyph.RoundGlyph(), radius = " + radius);
    }

    @Override
    void draw() {
        System.out.println("RoundGlyph.draw(), radius = " + radius);
    }
}

public class PolyConstructors {
    public static void main(String[] args) {
        new RoundGlyph(5);
    }
}
```

可以在脑中推演一下，看看这段代码的输出是什么？

下面是实际的输出

```
Output:
Glyph() before draw()
RoundGlyph.draw(), radius = 0
Glyph() after draw()
set radius = 1
this.radius = 1
RoundGlyph.RoundGlyph(), radius = 5
```
