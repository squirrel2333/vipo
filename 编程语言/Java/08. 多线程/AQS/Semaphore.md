### **1、Semaphore 是什么**

Semaphore 通常我们叫它信号量， 可以用来控制同时访问特定资源的线程数量，通过协调各个线程，以保证合理的使用资源。

可以把它简单的理解成我们停车场入口立着的那个显示屏，每有一辆车进入停车场显示屏就会显示剩余车位减1，每有一辆车从停车场出去，显示屏上显示的剩余车辆就会加1，当显示屏上的剩余车位为0时，停车场入口的栏杆就不会再打开，车辆就无法进入停车场了，直到有一辆车从停车场出去为止。

### **2、使用场景**

通常用于那些资源有明确访问数量限制的场景，常用于限流 。

比如：数据库连接池，同时进行连接的线程有数量限制，连接不能超过一定的数量，当连接达到了限制数量后，后面的线程只能排队等前面的线程释放了数据库连接才能获得数据库连接。

比如：停车场场景，车位数量有限，同时只能容纳多少台车，车位满了之后只有等里面的车离开停车场外面的车才可以进入。

### **3、Semaphore常用方法说明**

```java
acquire()
获取一个令牌，在获取到令牌、或者被其他线程调用中断之前线程一直处于阻塞状态。

acquire(int permits)
获取一个令牌，在获取到令牌、或者被其他线程调用中断、或超时之前线程一直处于阻塞状态。

acquireUninterruptibly()
获取一个令牌，在获取到令牌之前线程一直处于阻塞状态（忽略中断）。

tryAcquire()
尝试获得令牌，返回获取令牌成功或失败，不阻塞线程。

tryAcquire(long timeout, TimeUnit unit)
尝试获得令牌，在超时时间内循环尝试获取，直到尝试获取成功或超时返回，不阻塞线程。

release()
释放一个令牌，唤醒一个获取令牌不成功的阻塞线程。

hasQueuedThreads()
等待队列里是否还存在等待线程。

getQueueLength()
获取等待队列里阻塞的线程数。

drainPermits()
清空令牌把可用令牌数置为0，返回清空令牌的数量。

availablePermits()
返回可用的令牌数量。
```
### **4、用semaphore 实现停车场提示牌功能。**

每个停车场入口都有一个提示牌，上面显示着停车场的剩余车位还有多少，当剩余车位为0时，不允许车辆进入停车场，直到停车场里面有车离开停车场，这时提示牌上会显示新的剩余车位数。

**业务场景 ：**

1、停车场容纳总停车量10。

2、当一辆车进入停车场后，显示牌的剩余车位数响应的减1.

3、每有一辆车驶出停车场后，显示牌的剩余车位数响应的加1。

4、停车场剩余车位不足时，车辆只能在外面等待。

```java
public class TestCar {

    //停车场同时容纳的车辆10
    private  static  Semaphore semaphore=new Semaphore(10);

    public static void main(String[] args) {

        //模拟100辆车进入停车场
        for(int i=0;i<100;i++){

            Thread thread=new Thread(new Runnable() {
                public void run() {
                    try {
                        System.out.println("===="+Thread.currentThread().getName()+"来到停车场");
                        if(semaphore.availablePermits()==0){
                            System.out.println("车位不足，请耐心等待");
                        }
                        semaphore.acquire();//获取令牌尝试进入停车场
                        System.out.println(Thread.currentThread().getName()+"成功进入停车场");
                        Thread.sleep(new Random().nextInt(10000));//模拟车辆在停车场停留的时间
                        System.out.println(Thread.currentThread().getName()+"驶出停车场");
                        semaphore.release();//释放令牌，腾出停车场车位
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
            },i+"号车");

            thread.start();

        }

    }
}
```
