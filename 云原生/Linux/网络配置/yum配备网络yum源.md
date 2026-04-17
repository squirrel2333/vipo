
```
[hqw@localhost etc]$ cd yum.repos.d
[hqw@localhost yum.repos.d]$ ls
CentOS-Base.repo  CentOS-Debuginfo.repo  CentOS-Media.repo    CentOS-Vault.repo
CentOS-CR.repo    CentOS-fasttrack.repo  CentOS-Sources.repo  CentOS-x86_64-kernel.repo
[hqw@localhost yum.repos.d]$ sudo mkdir bak
[hqw@localhost yum.repos.d]$ mv C* bak

# 创建阿里源
[root@localhost yum.repos.d]wget -O /etc/yum.repos.d/CentOS-Base.repo https://mirrors.aliyun.com/repo/Centos-7.repo

# 查看现有yum库
 yum repolist all
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * base: mirrors.aliyun.com
 * extras: mirrors.aliyun.com
 * updates: mirrors.aliyun.com
repo id                                                    repo name                                          status
base/7/x86_64                                              CentOS-7 - Base - mirrors.aliyun.com               enabled: 10,072
centosplus/7/x86_64                                        CentOS-7 - Plus - mirrors.aliyun.com               disabled
contrib/7/x86_64                                           CentOS-7 - Codisabledntrib - mirrors.aliyun.com    disabled
extras/7/x86_64                                            CentOS-7 - Extras - mirrors.aliyun.com             enabled:    518
updates/7/x86_64                                           CentOS-7 - Updates - mirrors.aliyun.com            enabled:  5,434
repolist: 16,0

# 更改disabled
[root@localhost yum.repos.d]# vi CentOS-Base.repo
将enabled改为1

# 清除缓存
yum clean all

# 刷新
yum makecache
```
