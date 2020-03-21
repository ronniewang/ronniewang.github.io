# 关于 finalize 和 Scanner 的两个小知识点

闲来无事，偶然翻起了早已束之高阁的《Java 编程思想》，浏览了一遍目录，找了几个感兴趣的章节看了一下，果然是开卷有益啊，还是发现了几个之前没有了解或者没有很好理解的知识点

在此简单记录一下

## 一、finalize 方法

只记得《Effictive Java》中告诉我们忘记这个方法的存在就好，因为只在 Java 语言这个层面编程的程序员基本上用不到这个方法

这个方法的用途一般是回收本地方法调用中分配的内存，因为这部分内存不在垃圾收集的范围之内，如果不能合适的处理，就会导致内存泄漏，finalize 方法就是实现这种回收的一个地方

《Java 编程思想》中又提出了一种新的使用场景，就是对象终结条件的验证，用于发现 bug，如果你有业务规则，规定程序的对象在回收之前一定要做一些事情，那你可以通过 finalize 进行验证，当该做的事情没做时可以在 finalize 中进行提示，以便发现问题，比如打日志

虽然 finalize 方法不保证被调用，但是如果程序运行时间够长，内存使用够多还是会触发的，这样也就能发现问题，书中给了一个例子

这个例子假设，每本借出去的书在回收之前都应该 check in，在 finalize 中判断了一下，如果没有 check in，则打印一条错误消息，以便可以发现错误

```java
package thinkinjava.chapter5;

public class Book {
    boolean checkedOut = false;

    public Book(boolean checkedOut) {
        this.checkedOut = checkedOut;
    }

    public void checkIn() {
        checkedOut = false;
    }

    @Override
    protected void finalize() {
        if (checkedOut) {
            System.out.println("Error: checked out");
        }
    }

    public static void main(String[] args) {
        Book novel = new Book(true);
        novel.checkIn();
        // Drop the reference, forget to clean up:
        new Book(true);
        // Force gc & finalization
        System.gc();
    }
}
/*Output:
Error: checked out
*/
```

## 二、Scanner 类支持正则表达式分割

这个比较简单，直接上代码吧

```
package thinkinjava.chapter13;

import java.util.Scanner;
import java.util.regex.MatchResult;

public class ThreatAnalyzer {
    static String threatData = "" +
            "10.10.10.10@02/10/2005\n" +
            "10.10.10.11@02/10/2005\n" +
            "10.10.10.12@02/11/2005\n" +
            "10.10.10.13@02/11/2005\n" +
            "[Next log section with different data format]";

    public static void main(String[] args) {
        Scanner scanner = new Scanner(threatData);
        String pattern = "(\\d+[.]\\d+[.]\\d+[.]\\d+)@" + "(\\d{2}/\\d{2}/\\d{4})";
        while (scanner.hasNext(pattern)) {
            scanner.next(pattern);
            MatchResult matchResult = scanner.match();
            String ip = matchResult.group(1);
            String date = matchResult.group(2);
            System.out.format("Threat on %s from %s\n", date, ip);

        }
    }
}
/* output:
Threat on 02/10/2005 from 10.10.10.10
Threat on 02/10/2005 from 10.10.10.11
Threat on 02/11/2005 from 10.10.10.12
Threat on 02/11/2005 from 10.10.10.13
 */
 ```
