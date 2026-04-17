**一、基本语法**
 yum基于rpm包管理，能够从指定的服务器自动下载并安装rpm包，可以自动处理依赖关系，并且一次安装所有依赖包。
 yum命令形式如下：
 yum [options] [command] [package ...]
 其中：
 options是可选的，选项包括-h（帮助）、-y（当安装过程提示选择时全部为yes）、-q（不显示安装过程）等。
 command是所要进行的操作，包括install、update、remove、list、info等。
 package是操作的对象。

**二、常用操作**
1、安装
 #yum install xxx
 安装指定程序包。
2、升级
 #yum update xxx
 更新指定程序包。
 #yum check-update
 检查可更新的程序。
 #yum upgrade xxx
 升级指定程序包。
3、查找
 #yum info xxx
 显示安装包信息。
 #yum info
 列出所有已安装包信息
 #yum list
 显示所有已经安装和可以安装的程序包。
 #yum list xxx
 显示指定程序包安装情况。
 #yum list updates
 列出所有可以更新的程序包。
 #yum list installed
 列出所有已安装的程序包。
 #yum list extras
 列出所有已安装但不在yum Repository中的程序包。
 #yum deplist xxx
 查看指定程序包的依赖关系。
 #yum search xxx
 查找指定程序包，xxx可以是包名的一部分，会列出所有包含xxx的包名。
4、卸载
 #yum remove xxx
 卸载指定程序包。
5、缓存
 #yum clean packages
 清除缓存目录下的软件包。
 #yum clean headers
 清除缓存目录下的headers。
 #yum clean oldheaders
 清除缓存目录下旧的headers。
 #yum clean,yum clean all
 清除缓存目录下的软件包及旧的headers。

