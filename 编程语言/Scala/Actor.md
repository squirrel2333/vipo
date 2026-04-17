
# Actor介绍

java中，每个对象都有monitor逻辑监视器，用来控制对象的多线程访问，通过添加sychronized关键字来标记。但容易出现死锁等问题。

Actor并发编程模型，是Scala提供的一种事件模型的并发机制，**Actor不共享数据，依赖消息传递**，有效避免资源争夺，死锁等情况

![](Actor_001.png)

1. scala再2.11版本加入了Akka并发编程框架，老版本以废弃
2. Actor和Akka很像

---

# 创建Actor

通过类或者单例对象，继承Actor特质的方式，来创建Actor对象

1. 定义class或object继承Actor特质
2. 重写act方法
3. 调用Actor的start方法执行Actor
#### 案例： 通过class实现

```
import scala.actors.Actor

object Test {
  class Actor1 extends Actor {
    override def act(): Unit = {
      // 打印1-10
      for(i <- 1 to 10) println("Actor1..." + i)
    }
  }

  class Actor2 extends Actor {
    override def act(): Unit = {
      // 打印1-10
      for(i <- 1 to 10) println("Actor1..." + i)
    }
  }

  def main(args: Array[String]): Unit = {
  	val a1 = new Actor1()
    a1.start() // 自动调用act方法

    val a2 = new Actor2()
    a2.start()
  }
}
```
#### 案例：通过object实现

```
object Actor1 extends Actor{
    override def act(): Unit = {
      // 打印1-10
      for(i <- 1 to 10) println("Actor1..." + i)
    }
}

def main(args: Array[String]): Unit = {
	Actor1.start()
}
```

---

# 发送消息/接受消息

## 发送消息

|  |  |
| --- | --- |
| ! | 发送异步消息，没有返回值 |
| !? | 发送同步消息，等待返回值 |
| !! | 发送异步消息，返回值是Future[Any] |

给actor1发送一个异步字符串消息

`actor1 ! "你好!"`

## 接收消息

Actor中使用`receive`方法来接受消息，需要给receive方法传入一个偏函数

```
{
  case 变量名1: 消息类型1 => 业务处理1
  case 变量名2: 消息类型2 => 业务处理2
  ...
}
```
注意：receive方法只能接收一个消息，接受完后继续执行act方法

#### 案例：发送接收一句话

```
object Test {
	object ActorSender extends Actor {
  	override def act(): Unit = {
      ActorReceiver ! "你好, 我是sender"
      ActorReceiver ! "你叫什么？"
    }
  }

  object ActorReceiver extends Actor {
  	override def act(): Unit = {

      // receive()只能接收一次消息
      receive {
      	case x: String => println(x)
      }
    }
  }

  def main(args:Array[String]): Unit = {
  	ActorSender.start()
    ActorReceiver.start()
  }
}

// 结果： 你好, 我是sender
```
#### 案例： 持续发送和接收消息

```
object Test {

  object ActorSender extends Actor {
  	override def act(): Unit = {
      while(true) {
       	ActorReceiver ! "你好, 我是sender"
      	// 休眠3秒
        TimeUnit.SECONDS.sleep(3)
      }
    }
  }

  object ActorReceiver extends Actor {
  	override def act(): Unit = {
      while(true){
        receive {
          case x: String => println(x)
        }
      }
    }
  }

  def main(args:Array[String]): Unit = {
  	ActorSender.start()
    ActorReceiver.start()
  }
}
```
#### 案例： 优化持续接收消息

```
  object ActorSender extends Actor {
  	override def act(): Unit = {
      while(true) {
       	ActorReceiver ! "你好, 我是sender"
      	// 休眠3秒
        TimeUnit.SECONDS.sleep(3)
      }
    }
  }

  object ActorReceiver extends Actor {
  	override def act(): Unit = {
			// 提高效率
      loop {
        react{
          case x: String => println(x)
        }
      }
    }
  }
```
#### 案例：发送和接收自定义消息(同步有返回)

```
object Test {
	case class Message(id: Int, message: String)
  case class ReplyMessage(message: String, name: String)

  object MsgActor extends Actor {
  	override def act(): Unit = {
    	loop {
        react{
          case Message(id, message) => {
            println(s"我是MsgActor,我接受到的消息是${message}")
            // 回复
            sender ! ReplyMessage("我很好","赵丽颖")
        }
      }
    }
  }

  // main方法底层也是一个Actor,MainActor
  def main(args: Array[String]): Unit = {
    MsgActor.start()
    // 发送并接收
    val result: Any = MsgActor !? Message(1, "你好,我是MainActor")
    // 转型
    val reply: ReplyMessage = result.asInstanceOf[ReplyMessage]
  }
}
```
#### 案例：发送和接收自定义消息(异步有返回)

* 使用!!
* 返回类型为Future[Any]
* Future表示异步返回数据的封装,虽然获取到Future的返回值,但不一定有值,可能在未来某一时刻才会有返回消息
* Future的isSet()可检查是否已经收到返回消息,apply()方法可以获取返回数据
![](Actor_002.png)

```
object Test {
	case class Message(id: Int, message: String)
  case class ReplyMessage(message: String, name: String)

  object MsgActor extends Actor {
  	override def act(): Unit = {
    	loop {
        react{
          case Message(id, message) => {
            println(s"我是MsgActor,我接受到的消息是${message}")
            // 回复
            sender ! ReplyMessage("我很好","赵丽颖")
        }
      }
    }
  }

  // main方法底层也是一个Actor,MainActor
  def main(args: Array[String]): Unit = {
    MsgActor.start()
    // 发送并接收
		val future: Future[Any] = MsgActor !! Message(1, "你好,我是MainActor")
    while(!future.isSet){}
    val reply: ReplyMessage = future.apply().asInstanceOf[ReplyMessage]
  }
}
```
# WordCount

**需求:**

1. 进行单词统计文本文件名发送给WordCountActor
2. 接收每一个WordCountActor返回的结果进行合并

**步骤:**

1. 获取所有文件的全路径

2. 根据文件数量,创建指定个数的WordCountActor对象

 wordCountActor对象要和文件拉链到一起

3. 启动WordCountActor对象,统计文件中单词数量

4. 每个WordCountActor都需要对文件中的单词数量进行统计

5. 每个WordCountActor统计后的结果,都要返回给MainActor

6. 对接收到的每个返回数据统计并打印

```
object MainActor {
  def main(args: Array[String]): Unit = {
    // 1. 获取所有要统计的文件的路径
    var dir = "./data/"
    var fileNameList = new File(dir).list().toList
    val fileDirList = fileNameList.map(dir + _)
    println(fileDirList)

    // 2. 根据文件数量创建对应个数的WordCountActor对象
    val wordCountList = fileNameList.map(_ => new WordCountActor)
    val actorWithFile = wordCountList.zip(fileDirList)
    // WordCountActor1 -> ./data/1.txt      WordCountActor2 -> ./data/2.txt
    println(actorWithFile)

    // 3.
    val futureList: List[Future[Any]] = actorWithFile.map {
    	keyVal =>
      	val actor = keyVal._1
      	actor.start()
     		val future = actor !! WordCountTask(keyVal._2)
      	future
    }

    // 如果没有返回具体值的future对象个数不为零,说明还有Actor没有返回值
    while(futureList.filter(!_.isSet).size != 0){}

    val wordCountMap = futureList.map(_.apply().asInstanceOf[WordCountResult].wordCountMap)

    val result = wordCountMap.flatten.groupBy(_._1).map(keyVal => keyVal._1 -> keyVal._2.map(_._2).sum)
    println(result)
  }
}

class WordCountActor extends Actor {
	override def act(): Unit = {
    loop {
      react {
        case WordCountTask(fileName) =>
        	println(s"获取到的任务是 ${fileName}")

        	//4.
        	val lineList = Source.fromFile(fileName).getLines().toList  // List("hadoop sqoop hadoop" , "hadoop hadoop flume")

        	val strList = lineList.flatMap(_.split(" ")) // List("hadoop", "sqoop", "hadoop", "hadoop", "hadoop", flume")

        	val wordCount = strList.map(_ -> 1) // List("hadoop"->1, "sqoop"->1, "hadoop"->1, "hadoop"->1, "hadoop"->1, flume"->1)

        	val groupMap = wordCount.groupBy(_._1) // "hadoop" -> List("hadoop"->1, "hadpop"->1), "sqoop" -> List("sqoop" -> 1)
                                                    //  ↓                       ↓
        	val wordCountMap = groupMap.map(keyVal => keyVal._1 -> keyVal._2.map(_._2).sum )

          //5.
        	sender ! WordCountResult(wordCountMap)

      }
    }
  }
}

case class WordCountTask(fileName: String)
case class WordCountResult(wordCountMap: Map[String, Int])

1.txt
hadoop sqoop hadoop
hadoop hadoop flume
```
