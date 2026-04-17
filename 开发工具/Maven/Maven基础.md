
# 什么是Maven？

如今我们构建一个项目需要用到很多第三方的类库，如写一个使用Spring的Web项目就需要引入大量的jar包。一个项目Jar包的数量之多往往让我们瞠目结舌，并且Jar包之间的关系错综复杂，一个Jar包往往又会引用其他Jar包，缺少任何一个Jar包都会导致项目编译失败。

以往开发项目时，程序员往往需要花较多的精力在引用Jar包搭建项目环境上，而这一项工作尤为艰难，少一个Jar包、多一个Jar包往往会报一些让人摸不着头脑的异常。而Maven就是一款帮助程序员构建项目的工具，我们只需要告诉Maven需要哪些Jar 包，它会帮助我们下载所有的Jar，极大提升开发效率。

# Maven 功能

Maven 能够帮助开发者完成以下工作：

* 构建
* 文档生成
* 报告
* 依赖
* SCMs
* 发布
* 分发
* 邮件列表

# 约定配置

Maven 提倡使用一个共同的标准目录结构，Maven 使用约定优于配置的原则，大家尽可能的遵守这样的目录结构。如下所示：

|  |  |
| --- | --- |
| 目录 | 目的 |
| ${basedir} | 存放pom.xml和所有的子目录 |
| ${basedir}/src/main/java | 项目的java源代码 |
| ${basedir}/src/main/resources | 项目的资源，比如说property文件，springmvc.xml |
| ${basedir}/src/test/java | 项目的测试类，比如说Junit代码 |
| ${basedir}/src/test/resources | 测试用的资源 |
| ${basedir}/src/main/webapp/WEB-INF | web应用文件目录，web项目的信息，比如存放web.xml、本地图片、jsp视图页面 |
| ${basedir}/target | 打包输出目录 |
| ${basedir}/target/classes | 编译输出目录 |
| ${basedir}/target/test-classes | 测试编译输出目录 |
| Test.java | Maven只会自动运行符合该命名规则的测试类 |
| ~/.m2/repository | Maven默认的本地仓库目录位置 |

![](Maven基础_001.jpg)

## 项目的构建

构建面向过程

1. 清理：把之前项目编译的东西删除掉，找新的编译代码做准备
2. 编译：把程序源代码编译为执行代码 java->class文件
1. maven同时编译很多文件
2. Javac 只编译一个
3. 测试：maven可以执行测试程序代码，批量测试
4. 报告：生成测试结果文件
5. 打包：把项目中所有的class文件配置文件等资源放到一个压缩文件中，这个压缩文件就是项目结果文件，通常java程序压缩为jar包，web应用压缩为war包
6. 安装：将5中的文件安装到本机仓库
7. 部署：把程序安装好可以执行

## POM

POM：pom.xml 项目对象模型，控制maven构建项目过程，管理jar依赖

* modelVersion: Maven版本 4.0.0
* groupId：组织id，一般是公司域名倒写
* artifactId：项目名称，也是模块名称
* Version：版本号 开发版本后带-SNAPSHOT快照

坐标：唯一标识 groupId+artifactId+version

mvnrepository.com 中央仓库

* packaging：打包后压缩文件的扩展名，不写默认是jar
+ packaging 属性为 jar（默认值），代表普通的Java工程，打包以后是.jar结尾的文件。
+ packaging 属性为 war，代表Java的web工程，打包以后.war结尾的文件。
+ packaging 属性为 pom，代表不会打包，用来做继承的父工程。
* 依赖：dependencies和dependency

```
<dependencies>
  <dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>5.1.9</version>
  </dependency>
 </dependencies>
```
* properties:设置属性
* build：maven在项目构建时，配置信息，例如指定编译java代码使用的jdk

# Idea构建Maven JavaSE工程

注意：此处省略了version，直接给了一个默认值1.0-SNAPSHOT

自己后期可以在项目中随意修改！

![](./attachments/Maven基础_002)

# Idea构建Maven JavaEE工程

1. 手动创建
1. 创建一个javasemaven工程
2. 手动添加web项目结构文件
注意：结构和命名固定
![](./attachments/Maven基础_003)
3. 修改pom.xml文件打包方式
修改位置：项目下/pom.xml

```
<groupId>com.atguigu</groupId>
<artifactId>maven_parent</artifactId>
<version>1.0-SNAPSHOT</version>
<!-- 新增一列打包方式packaging -->
<packaging>war</packaging>
```
 项目的webapp文件夹出现小蓝点，代表成功！！

2. 插件方式创建
1. 安装插件JBLJavaToWeb
file / settings / plugins / marketplace
![](./attachments/Maven基础_004)
2. 创建一个javasemaven工程
3. 右键、使用插件快速补全web项目
![](./attachments/Maven基础_005)

# Maven常用命令、生命周期、插件

maven生命周期是项目的过程

通过命令，完成maven的生命周期执行

maven执行时真正完成功能的是插件，就是一些jar文件，一些类

1. 单元测试（测试方法）：用junit测试，测试类中的方法，方法是测试的基本单元

maven借助单元测试，批量测试你类中大量方法

1. pom.xml中加入依赖

<dependency>

<groupId>junit</groupId>

<artifactId>junit</artifactId>

<version>4.11</version>

<scope>test</scope>

</dependency>

1. 在maven项目src/test/java目录下，创建测试程序

@Test

public void testAdd(){

测试HelloMaven的add方法是否正确

}

其中测试方法必须是public，没有返回值，方法名为test+方法名，方法上加@Test

当执行以下命令时，下面的命令会把上面的全部做一遍

* mvn clean 清理（会删除原来编译测试的目录，即target目录，但是已经install到仓库里的不会清理
* mvn compile

编译main/java/目录下的java为class文件，同时把class拷贝到target/classes目录下面

把main/resources目录下的所有文件，拷贝到target/classes目录下

+ mvn test-compile

编译测试程序

* mvn test 测试 生成一个surefire-reports，保存测试结果
* mvn package 打包主程序 会编译、编译测试、测试，并且按照pom.xml配置把主程序打包生成jar包或war包
* mvn install 安装主程序 会把工程打包，并且安装本工程的坐标保存到本地仓库中

