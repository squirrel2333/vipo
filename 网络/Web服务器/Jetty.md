
# 简介

提供HTTP的服务器、客户端。javax.servlet容器的开源项目。Jetty实现的JavaEE规范主要是Servlet规范。

* 轻量级web服务器和servlet引擎
* 可作为嵌入式服务器

## Maven坐标

```
<dependency>
    <groupId>org.eclipse.jetty</groupId>
    <artifactId>jetty-server</artifactId>
    <version>9.4.44.v20210927</version>
</dependency>
```
## Server包

`<https://www.eclipse.org/jetty/download.html>`

# 使用

## Contents

|  |  |
| --- | --- |
| Location | Description |
| license-eplv10-aslv20.html | Jetty的许可文件 |
| README.txt | 有用的开始信息 |
| VERSION.txt | 版本信息 |
| bin/ | 存放在Unix系统下运行的shell脚本 |
| demo-base/ | 一个可运行包含示例web应用的Jetty服务器基目录 |
| etc/ | Jetty的配置文件 |
| lib/ | Jetty运行所必须的jar文件 |
| logs/ | 日志 |
| modules/ | 各个模块 |
| notice.html | 许可信息等 |
| resources/ | 包含新增到classpath配置文件夹，如log4j.properties |
| start.ini | 存放启动信息 |
| start.jar | 运行Jetty的jar |
| webapps/ | 一个用来存放运行在默认配置下的Jetty Web应用目录 |

## 运行

`java -jar start.jar`

通过浏览器访问8080。此时$JETTY\_HOME/webapps下没有部署web应用，404。

### 应用例子

Jetty中有demo-base. 在该目录下运行

`java -jar ../start.jar`

### 改变端口

`java -jar $JETTY\_HOME/start.jar jetty.http.port=8081`

## 部署Web应用

Jetty支持热部署，可以自动部署webapps目录下的项目。标准的war文件和Jetty配置文件放置在webapps目录下，通过以下规则进行热部署

* 例如一个包含 WEB-INF/ 子文件夹的example/文件夹做为一个标准的web应用进行部署，否则就按静态资源部署，context路径为/example（访问路径： http://localhost:8080/example/）；若文件夹名字为ROOT，则访问路径为/；若文件夹名字末尾为.d则此文件夹被忽略（除非被特殊的配置文件引用）
* 若一个文件example.war被部署在webapps下，则context路径为example/，如果名字为ROOT则访问路径为/，如果example.war文件和example/文件夹同时存在，只有war文件会被部署（这个文件会被当做一个没有被解压的目录来使用）
* 一个像example.xml的配置文件，若文件中是标准的配置信息，也会被部署，文件中必须包含context path，如果context path和example.xml同时存在，只有example.xml会被部署。

 如果你有一个标准的web应用，可以直接将其拷贝到webapps文件夹下进行热部署。

## 配置应用

![](Jetty_001.png)

```
private val threadPool = new QueuedThreadPool(200, 50, 10000)
private val server = new NonTraceableServer(threadPool) // extends jetty server

private val gracefulShutdownHandler = new GracefulShutdownHandler(CoolDownPeriod)
gracefulShutdownHandler.setHandler(createServletContextHandler(conf))

server.setHandler(gracefulShutdownHandler)
val insecureConnectors: Array[Connector] = createInsecureConnectors()
val replayConnectors: Array[Connector] = createReplayConnectors()
val secureConnectors: Array[Connector] = createSslConnectors()
server.setConnectors(insecureConnectors ++ secureConnectors ++ replayConnectors)
server.setStopAtShutdown(true)
server.setStopTimeout(JettyStopTimeout.toMillis)
server.start()
```
# GracefulShutdown的实例

```
class GracefulShutdownHandler(coolDownPeriod : Duration) extends HandlerWrapper with Graceful with LazyLogging {
  private val counter = new AtomicInteger(0)
  @volatile private var cooledDown = false
  @volatile private var reachedShutdown = false

  override def handle(target: String, baseRequest: Request, request: HttpServletRequest, response: HttpServletResponse): Unit = {
    if (cooledDown) {
      response.sendError(SERVICE_UNAVAILABLE_503, "Server is halted")
      logger.warn(s"Received request after cool down period, ${counter.get()} requests are ongoing")
    } else {
      counter.getAndIncrement()
      try {
        getHandler.handle(target, baseRequest, request, response)
      } finally {
        counter.getAndDecrement()
      }
    }
  }

  override def isShutdown: Boolean = reachedShutdown

  override def shutdown(): Future[Void] = {
    val waitForNoPendingRequests = new Runnable {
      override def run(): Unit = {
        logger.info(s"Entering cool down, ${counter.get()} ongoing requests")
        Thread.sleep(coolDownPeriod.toMillis)
        cooledDown = true
        logger.info(s"Cooled down, stopped accepting new requests, ${counter.get()} ongoing requests")
        while (counter.get > 0) {
          Thread.sleep(500)
        }
        reachedShutdown = true
        logger.info("Drained all ongoing requests")
      }
    }

    Executors.newSingleThreadExecutor().submit(waitForNoPendingRequests, null)
  }
}
```
