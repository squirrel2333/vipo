
```
$ mvn dependency:tree >> test.log

## 跳过单元测试
-Dmaven.test.skip=true

你先把inbound-interface 整个mvn clean install -DskipTests，这样你最新的api就进入到你本地的.m2了，
然后再把CA-inbound里面对应的inbound-interface的版本改为你本地最新编译过的
```

