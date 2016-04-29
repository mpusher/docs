# 服务部署

说明：mpush 主要依赖zookeeper和redis两个服务
1. 安装ZK
2. 安装redis
3. 到mpush工程根目录下的conf-dev.properties 文件，修改一下部分，按格式改成前两步骤对应的机器IP、端口、密码
```java
zk_ip = zk_serverIp:port
zk_digest = zk_password
zk_namespace = mpush-daily
redis_group = redis_server_ip:port:redis_password
```
4.运行```./start.sh```启动，如果一切ok会在log.home配置项对应的目录下看到相关日志。

其他：
1. 公私密钥对可以使用```com.shinemo.mpush.tools.crypto.RSAUtils#genKeyPair``` 来生成，生成的密钥的长度大小要和配置文件里配置的```ras_key_length=1024```相一致。

2. 其他配置项可以参考```com.shinemo.mpush.tools.config.ConfigCenter.java```


