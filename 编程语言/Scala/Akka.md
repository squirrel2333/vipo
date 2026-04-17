
Akka是一个用于构建高并发,分布式可扩展的基于事件驱动的应用工具包。

![](Akka_001.png)

# 创建Actor

## Api介绍

* ActorSystem负责创建和监督Actor

1. ActorSystem是一个重量级结构，需要分配多个线程
2. ActorSystem通常是一个单例对象，可以创建很多Actor
3. 直接使用context.system就可以获取到管理该Actor的ActorSystem的引用
* 实现Actor类

1. 定义类或单例对象继承Actor
2. 实现receive方法，在方法里处理消息。不需要loop react Akka会自动调用receive接收消息
3. 还可以实现preStart()方法
* 加载Actor

1. 要创建Akka的Actor，必须先获取创建一个ActorSystem。需要给一个指定名称，并加载一些配置项
2. 调用`ActorSystem.actorOf(Props(Actor对象), "Actor名字")`来加载Actor

```
case class SubmitTaskMessage(msg: String)
case class SuccessSubmitTaskMessage(msg: String)
object SenderActor {
  override def receive: Receive = {
      case "start" => {
        println("接收到start")
        // 获取ReceiverActor路径
        // 格式：akka://actorSystem的名字/user/具体获取对象的名字
        val receiverActor = context.actorSelection("akka://actorSystem/user/receiverActor")
        receiverActor ! SubmitTaskMessage("我在给你发消息")
    	}
    	case SuccessSubmitTaskMessage(msg) =>
    		println(s"接收到消息${msg}")
  }
}

object ReceiverActor {
  override def receive: Receive = {
		case SubmitTaskMessage(msg) => {
      println(s"接收到的消息是${msg}")
      sender ! SuccessSubmitTaskMessage("接收任务成功！")
  }
}

object Entrance {
  def main(args: Array[String]): Unit = {
  	val actorSystem = ActorSystem("actorSystem", ConfigFactory.load())
    val senderActor = actorSystem.actorOf(Props(SenderActor), "senderActor")
    val receiverActor = actorSystem.actorOf(Props(ReceiverActor), "receiverActor")

    senderActor ! "start"
  }
}
```

---

# Akka定时任务

`ActorSystem.scheduler.schedule()`方法可以启动一个定时任务

`def schedule(initialDelay: FiniteDuration, interval: FiniteDuration, receiver: ActorRef, message: Any)(implicit executor: ExcutionContext)`

`def schedule(initialDelay: FiniteDuration, interval: FiniteDuration)(f: => Unit)(implicit executor: ExcutionContext)`

```
import akka.actor.Actor

object MainActor{
  object ReceiverActor extends Actor {

     override def receive: Receive = {
       case x => println(x)
     }
  }

  def main(args: Array[String]): Unit = {
  	val actorSystem = ActorSystem("actorSystem", ConfigFactory.load())
    val receiverActor = actorSystem.actorOf(Props(ReceiverActor), "receiverActor")

    //导入隐式转换，支持定时器
    import actorSystem,dispatcher
    // 导入隐式参数，用来给定时器设置默认值
    import scala.concurrent.duration._

    // 通过定时器3秒开始，间隔2秒，给ReceiverActor发送一句话
    // 方式一 采用发送消息形式
    actorSystem.scheduler.schedule(3 seconds, 2 seconds, receiverActor, "恭喜")

    // 方式二 采用自定义消息，结合函数
    actorSyste.scheduler.schedule(3 seconds, 2 seconds)(receiverActor ! "恭喜")

    // 实际写法
    actorSyste.scheduler.schedule(3 seconds, 2 seconds){
      receiverActor ! "恭喜"
    }
  }
}

```

---

# 实现两个进程之间的通信

```
object WorkerActor extends Actor {
  override def receive(): Receive = {
    case "setup" => {
      println("接收到消息setup")
      // 获取远程Actor
      val masterActor = context.system.actorSelection("akka.tcp://actorSystem@127.0.0.1:8888/user/masterActior")
      masterActor ! "connect"
    }
    case "success" => println("接收到消息success")
  }
}

object Entrance {
  def main(args: Array[String]): Unit = {
    val actorSystem = ActorSystem("actorSystem", ConfigFictory.load())
    val workerActor = actorSystem.actorOf(Props(WorkerAcotr),"workerActor")
    workerActor ! "setup"
  }
}
------------------------------------------------------------
// 对象地址: akka.tcp://actorSystem@127.0.0.1:8888
object MasterActor extends Actor {
  override def receive: Receive = {
    case "connect" => {
      println("接收到消息connect")
      // 获取远程Actor
      sender ! "success"
    }
  }
}

object Entrance {
    def main(args: Array[String]): Unit = {
      val actorSystem = ActorSystem("actorSystem", ConfigFictory.load())
      val workerActor = actorSystem.actorOf(Props(MasterAcotr),"masterActor")
  	}
}

```
两个main函数都需要启动

