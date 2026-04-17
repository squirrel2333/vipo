使用zookeeper zkCli.sh 连接 zookeeper服务时，默认裸连，晓得ip与端口之后即可连接zookeeper服务，本文使用SASL 用户名密码配置服务端与客户端，在zkCli连接前，服务端配置xxxjaas.conf保存用户名密码，客户端（也就是zkCli或者各种语言的sdk）连接时同样也需要xxxjaas.conf文件来进行认证

# Server - Client

## 1. 修改配置zoo.cfg

解压目录中的conf目录修改zoo.cfg, 如果没有的话，复制zoo\_sample.cfg一份，命名为zoo.cfg, 添加sasl认证相关的配置：

```
sessionRequireClientSASLAuth=true

authProvider.1=org.apache.zookeeper.server.auth.SASLAuthenticationProvider
```
* **sessionRequireClientSASLAuth：** zk3.6.0版本中添加，为true时要求客户端连接zk时必须进行SASL认证才可以连接成功，也就是说没有进行SASL认证的匿名用户就无法连接了，相当于在连接时设置了一个登录密码。
* **authProvider.1：** zk的认证方式，可以为ZooKeeper指定多个认证提供程序类。通常使用此参数指定SASLAuthenticationProvider

## 2. 添加认证文件jaas.conf

```
Server {
       org.apache.zookeeper.server.auth.DigestLoginModule required
       user_super="adminsecret"
       user_bob="bobsecret";
};
```
* 提供了两个用户super和bob，他们的密码分别是"adminsecret"和"bobsecret"。
* 请注意，上面的密码都是纯文本，所以除了ZooKeeper服务器进程用户之外，JAAS配置文件应该不能被其他人读取。

* 不能加#注释
* 文件名没有要求但要以conf为后缀
* 结尾要有分号
## 3. 添加server启动参数

在conf目录下添加java.env,内容如下：

```
SERVER_JVMFLAGS="-Djava.security.auth.login.config=/home/dev/soft/sasl/conf/jaas.conf"
```
指定jaas.conf的路径，zkServer启动的时候会读取java.env，作为启动参数。

zkServer.sh启动时会执行一下 zkEnv.sh, 在zkEnv.sh 执行时会判断一下java.env zookeeper-env.sh 是否存在，这两个脚本谁存在就执行一下，因此这俩脚本可以做zookeeper 服务或者客户端启动前的变量配置

***记得创建完 java.env 或者 zookeeper-env.sh后 使用chmod a+x 赋予这俩脚本的可执行权限，不然zookeeper服务端或者客户端启动时是不会给你带上自定义参数的***

## 4. 启动server

ps -aux | grep java查看java启动参数'

## 5. Client配置

```
Client {
       org.apache.zookeeper.server.auth.DigestLoginModule required
       username="bob"
       password="bobsecret";
};
```
## 6. Client启动参数

```
CLIENT_JVMFLAGS="-Djava.security.auth.login.config=/path/to/client/jaas/file.conf"
```
## 7. 连接

### a. cli

`zkCli.sh -server localhost:6181`

### b. java

```
@Before
public void init() throws IOException, InterruptedException {
    System.setProperty("java.security.auth.login.config", "xxx/conf/jaas.conf");
    log.info("********************** start zk ..................");
    CountDownLatch countDownLatch = new CountDownLatch(1);
    zooKeeper = new ZooKeeper(ZK_ADDR, ZK_SESSION_TIMEOUT, event -> {
        log.info("%%%%%%%%%%%%%%%%%%%%%触发了事件：[{}]", event);
        countDownLatch.countDown();
    });
    countDownLatch.await();
}
```
# Server - Server

# ZooKeeper SASL configurations

Following configurations should be added in *$conf/zoo.cfg* configuration file.

**Sample zoo.cfg:**

|  |
| --- |
| quorum.auth.enableSasl=truequorum.auth.learnerRequireSasl=truequorum.auth.serverRequireSasl=truequorum.auth.learner.saslLoginContext=QuorumLearnerquorum.auth.server.saslLoginContext=QuorumServerquorum.auth.kerberos.servicePrincipal=servicename/\_HOSTquorum.cnxn.threads.size=20 |

1. Sets to enable quorum authentication using SASL.
**zoo.cfg**

|  |
| --- |
| # Defaulting to false |

2. Sets to connect using quorum authentication. If this is true, quorum peer learner will send authentication packet to quorum peer server then proceeds with LE on successful authentication. If false, then proceeds with LE without any authentication. This can be used while upgrading ZooKeeper server.
**zoo.cfg**

|  |
| --- |
| # Defaulting to false |

3. Sets to connect using quorum authentication. If this is true, then all unauthenticated quorum peer learner connection requests will be rejected. If false, then quorum peer server will accept quorum peer learner connection request and then proceeds with Leader Election even if the authentication did not succeed. This can be used while upgrading ZK server.**zoo.cfg**

|  |
| --- |
| # Defaulting to false |

4. (Optional) If you want to use different login context for learner/server other than the default values, then configure the following.**zoo.cfg**

|  |
| --- |
| # Defaulting to QuorumLearner |

5. The maximum number of threads to allow in the “connectionExecutors” thread pool, which will be used to process quorum server connection requests during Leader Election. This has to be tuned depending on the cluster size. For example, consider a 3-node cluster, during quorum formation at least 3 outgoing connection requests and 3 incoming connection requests will occur. So total 6 threads will be used. It is recommended to configure 2x number of threads for smooth execution, where 'x' represents the cluster size.**zoo.cfg**

|  |
| --- |
| # Defaulting to 20 |

quorum.auth.enableSasl=truequorum.auth.learnerRequireSasl=truequorum.auth.serverRequireSasl=truequorum.auth.learner.saslLoginContext=QuorumLearner # Defaulting to QuorumServerquorum.auth.server.saslLoginContext=QuorumServerquorum.cnxn.threads.size=20

## conf/java.env

Add the following settings to the *java.env* file located in the ZooKeeper configuration directory. (Create the file if it does not already exist.)

**java.env**

|  |
| --- |
| SERVER\_JVMFLAGS="-Djava.security.auth.login.config=/path/to/server/jaas/file.conf" |

ZooKeeper servers will talk to each other using the credentials configured in “jaas/file.conf” file. They will act like learner-server when creating connections during quorum formation. Set up the Java Authentication and Authorization Service (JAAS) by creating a “jaas/file.conf” file in the ZooKeeper configuration directory and add configuration entries into this file specific to your selected authentication schemes.

## DIGEST-MD5 based authentication

**JAAS configuration file, DIGEST-MD5 mechanism**

|  |
| --- |
| QuorumServer { org.apache.zookeeper.server.auth.DigestLoginModule required user\_test="test";}; QuorumLearner { org.apache.zookeeper.server.auth.DigestLoginModule required username="test" password="test";}; |

**Important Note:** Authorization is not supported in this format

