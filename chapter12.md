# 服务部署

###### 说明：mpush 服务只依赖于zookeeper和redis

1. 安装zk
2. 安装Redis
3. 修改工程根目录下的```conf-dev.properties```配置文件，主要修改一下部分
```java
#zk配置
zk_ip = 127.0.0.1:2181
zk_digest = shinemoIpo
zk_namespace = mpush-daily
#redis配置多个用，隔开
redis_group = 127.0.0.1:6379:shinemoIpo
```
4.执行工程跟目录下的```./start.sh```启动服务，如果一切正常可以到```log.home=/opt/logs/mpush```配置的目录下查看相关日志。

其他：
* 关于配置说明可以参考:```com.shinemo.mpush.tools.config.ConfigCenter.java```
* 另外客户端需要一个allot接口返回mpush server 的ip列表，返回值格式```ip:port,ip:port```



