# Alloc实现

## alloc 的作用

> * alloc 是针对client提供的一个轻量级的负载均衡服务
> * 每次客户端在链接MPUSH server之前都要调用下该服务
> * 以获取可用的MPUSH server列表,然后按顺序去尝试建立TCP链接,直到链接建立成功

## 对外提供的接口定义

> 接口类型     ：HTTP
>
> Method       : GET
>
> 参数         ：无
>
> 返回值格式   : ip:port,ip:port
>
> content-type : text/plain;charset=utf-8 

## 实现讲解

1. 服务部署可以集成Tomcat或自己实现一个HttpServer比如基于Netty实现

2. mpush server 集群列表可以从Zookeeper查询，目前提供的有ZK查询客户端

3. 如果要实现负载均衡可以考虑使用以下几种方式实现：

   > 随机，每次从mpush server列表随机选取一个地址返回给客户端
   >
   > 轮播，每次把mpush server列表依次返回给客户端
   >
   > 按链接数量排序，链接数少的排最前面

## Alloc服务存在的意义

刚开始看MPUSH的童鞋可能会有疑问，这玩意有什么用，为什么不直接连mpush server ?

如果直连你可能遇到一些问题，比如你的mpush server 可能不止一台，你怎么选择该连哪一台？

其中某台服务挂了怎么办？要更换机器又怎么办？这时你必然希望有一台前置服务来对整个mpush集群进行统一调度。