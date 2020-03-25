# 一些不常用的并发工具类

最近看到了《Java 编程思想》并发这章，发现了几个从来没有用过的工具和数据结构，照着书中的代码熟悉以后，以后遇到问题的时候看看能不能用上

## DelayQueue

```java
package thinkinjava.chapter21;

import java.util.ArrayList;
import java.util.List;
import java.util.Random;
import java.util.concurrent.*;

class DelayedTask implements Runnable, Delayed {

    private static int counter = 0;
    private final int id = counter++;
    private final int delta;
    private final long trigger;
    protected static List<DelayedTask> sequence = new ArrayList<>();

    public DelayedTask(int delta) {
        this.delta = delta;
        trigger = System.currentTimeMillis() + TimeUnit.MILLISECONDS.toMillis(delta);
        sequence.add(this);
    }

    @Override
    public long getDelay(TimeUnit unit) {
        return unit.convert(trigger - System.currentTimeMillis(), TimeUnit.MILLISECONDS);
    }

    @Override
    public int compareTo(Delayed o) {
        DelayedTask that = (DelayedTask) o;
        return Long.compare(trigger, that.trigger);
    }

    @Override
    public void run() {
        System.out.print(this + " ");
    }

    @Override
    public String toString() {
        return String.format("[%1$-3d]", delta) + " Task " + id;
    }

    public String summary() {
        return "(" + id + ":" + delta + ")";
    }

    public static class EndSentinel extends DelayedTask {
        private ExecutorService executorService;

        public EndSentinel(int delta, ExecutorService executorService) {
            super(delta);
            this.executorService = executorService;
        }

        public void run() {
            System.out.println();
            System.out.println("Tasks in sequence: " + " ");
            for (DelayedTask delayedTask : sequence) {
                System.out.print(delayedTask.summary() + " ");
            }
            System.out.println();
            System.out.println(this + " Calling shutdownNow()");
            executorService.shutdownNow();
        }
    }
}

class DelayedTaskConsumer implements Runnable {

    private DelayQueue<DelayedTask> q;

    public DelayedTaskConsumer(DelayQueue<DelayedTask> q) {
        this.q = q;
    }

    @Override
    public void run() {
        try {
            System.out.println("Take task from delayed queue");
            while (!Thread.interrupted()) {
                q.take().run();
            }
        } catch (InterruptedException e) {
            // exit
        }
        System.out.println("Finished DelayedTaskConsumer");
    }
}

public class DelayQueueDemo {

    private static final int UPPER_BOUND = 5000;

    public static void main(String[] args) {
        Random random = new Random();
        ExecutorService executorService = Executors.newCachedThreadPool();
        DelayQueue<DelayedTask> delayedTasks = new DelayQueue<>();
        for (int i = 0; i < 10; i++) {
            delayedTasks.put(new DelayedTask(random.nextInt(UPPER_BOUND)));
        }
        delayedTasks.add(new DelayedTask.EndSentinel(UPPER_BOUND, executorService));
        executorService.execute(new DelayedTaskConsumer(delayedTasks));
    }
}

```
