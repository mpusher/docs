# 服务端SDK

## 使用方式

1. 添加maven依赖

   ```xml
   <dependency>
     <groupId>com.github.mpusher</groupId>
     <artifactId>mpush-client</artifactId>
     <version>0.0.2</version>
   </dependency>
   ```

   ​

2. 在工程`resources`目录增加配置文件`application.conf`，并配置`zookeeper`

   ```java
      mp.zk.namespace=mpush
      mp.zk.server-address="127.0.0.1:2181"
   ```

   ​

3. 使用`com.mpush.api.push.PushSende.java`进行推送，使用其`create`方法创建服务，`start`方法启动服务，`stop`方法停止服务。推送接口定义如下：

   ```java
   public interface PushSender extends Service {
     	//批量推送，建议对userIds的大小进行限制
       void send(String content, Collection<String> userIds, Callback callback);
   	//单用户推送
       FutureTask<Boolean> send(String content, String userId, Callback callback);

       void send(byte[] content, Collection<String> userIds, Callback callback);

       FutureTask<Boolean> send(byte[] content, String userId, Callback callback);
   	
     	//推送回调
       interface Callback {
           void onSuccess(String userId, ClientLocation location);//推送成功

           void onFailure(String userId, ClientLocation location);//推送失败

           void onOffline(String userId, ClientLocation location);//用户不在线

           void onTimeout(String userId, ClientLocation location);//推送超时
       }
   }
   ```

   ​

## 推送流程

### 流程图

![](MPush接消息推送.png)

### 流程分析

1. `PushSender`启动后首先从ZK里获取可用的`GatewayServer`列表，然后创建相应的`Client`分别连接到对应的`GatewayServer`
2. 当调用`send`方法去推送时，`PushSender`首先会通过`RemoteRouterManager`查询要推送的用户当前所登录的机器IP，然后通过IP选择`GatewayServer`并通过第1中对应的`Client`把消息发送到该机器，因为该机器拥有用户的链接。
3. `GatewayServer`接收到`Client`发送过来的消息后，首先通过查询本地路由`LocalRouterManager`找到用户连接`Connection`，该链接是连接到`ConnectionServer`的。
4. 如果连接存在，`ConnectionServer`会通过此连接把消息下发到客户端。
5. 如果推送成功，`GatewayServer`会发送消息推送成功的消息给`PushSender`所持有的`Client`
6. `PushSender`收到推送成功消息后，会通过`Callback#onSuccess`回调调用方，整个推送流程结束。
7. 如果中间有任何失败则回调`Callback#onFailure`。
8. 如果用户不在线则回调`Callback#onOffline`。
9. 如果在一定时间内`PushSender`没有收到`GatewayServer`响应的消息则推送超时，回调`Callback#onTimeout`通知调用方。

## 源码解读

1. `PushSender`的实现类为`com.mpush.client.push.PushClient.java`

2. `PushClient`使用的是`ConnectionRouterManager`该类继承自`RemoteRouterManager`增加了本地缓存可在消息频繁时减轻`Redis`压力，但会存在一定情况的误判。

3. `com.mpush.client.push.PushRequestBus.java`用于维持异步推送任务，线程的调整可通过配置设置，任务的拒绝策略为__在调用线程执行Callback__。具体见`DefaultThreadPoolFactory.java`。线程池默认配置如下：

   ```java
   mp.thread.pool.push-callback={
     min:2//核心线程数
     max:2//最大线程数
     queue-size:0//队列大小
   }
   ```

   ​

4. `GatewayClient`会根据`GatewayServer`的运行状态自行调整，如果有`GatewayServer`宕机对应的`Client`会及时销毁，如果有新的机器进来，对应`Client`也会自动创建。具体参见`GatewayClientFactory.java`。

   ​