# 为什么要多线程

计算机的原始时代，连操作系统都没有，就是一串指令从头执行到尾，这段代码可以任意访问系统资源

在裸机上写代码是在是太复杂了，需要处理各种底层的逻辑，而且计算机这么贵，一次只能跑一个程序，太浪费了

后来前辈们就搞出了操作系统，操作系统可以使多个程序并发执行，这样带来了很多好处

1. 资源利用率的提高，一个程序运行时遇到了需要等待外部事件的时候，就可以运行其他的程序，充分利用计算机的资源
2. 支持了多用户，通过时间片进行调度，让多个用户的程序并发执行，而不是前一个程序再跑，后面的只能等着
3. 使程序编写更简单了，可以将一个复杂的任务拆分成不同的进程来运行，只在必要的时候进行信息同步

后来，人们发现，一个进程中，也有一定的异步性，后来便引入了线程的概念，一个进程中包含一个或多个线程，他们之间共享进程的资源

## 线程的优势

1. 充分利用多处理器，随着计算机硬件的发展，CPU的核数越来越多，多线程更能充分利用计算资源
2. 建模更简单，同上面进程的优势3一样，一个进程内也可以拆分出不同的任务，分别执行，将任务之间的同步、调度等问题交给框架去处理
3. 减少异步事件的使用，拿连接请求处理来说，单线程肯定尽量避免阻塞IO，使用非阻塞IO就比较复杂，如果每一个线程执行一个请求的处理，阻塞也就无所谓了，不影响主流程
4. 响应更灵敏的用户界面

## 多线程的风险

1. 安全性问题，多线程共享进程的数据，而线程执行的顺序是不可预测的，对共享数据的访问如果线程间没有正确的同步，就会导致错误
2. 活跃性问题，单线程如果不是死循环，程序终究会运行结束，多线程则不然，如果同步不正确，多个线程互相等待对方的资源，可能永远也不会往下执行了
3. 性能问题，多线程调度也需要开销，数据同步也需要开销，而且同步还会抑制编译器、运行时等对程序的优化

上面三点也是我们使用多线程时关注的主要关键点

《Java并发编程实战》这本书体系化的讲解了Java程序中多线程的使用，对于安全性问题，活跃性问题，性能问题都有深入的讲解，作者也都是这个领域的大神级人物，内容质量和深度等是有保证的

既然内容比较深，那肯定不像其他快餐书一样，走马观花就能读完，需要多读，多思考，有的地方一遍看不懂，就多看几遍，对于翻译不准确的地方，最好对照原版来读，当然，有能力直接读原版的更好

我之前也是本着实用主义的角度，读了这本书，主要看了怎么用线程池，怎样使用同步工具，可以说是浪费了这书的大部分价值，今天开始重读一遍，输出几篇笔记，希望对Java的并发编程有更深刻的理解

# 文章列表

* [线程安全性](thread-safe.md)
