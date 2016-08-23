# 源码测试
### 说明请提前参照[服务部署章节](chapter12.md)，安装好所有依赖
1. ```git clone https://github.com/mpusher/mpush.git```
2. 导入到eclipse或Intellij IDEA
3. 打开```mpush-test```模块，所有的测试代码都在该模块下
4. 修改配置文件```src/test/resource/application.conf```文件修改方式参照服务部署[第6点](chapter12.md)
5. 运行```com.mpush.test.sever.ServerTestMain.java```启动长链接服务
6. 运行```com.mpush.test.client.ConnClientTestMain.java``` 模拟一个客户端
7. 运行```com.mpush.test.push.PushClientTestMain``` 模拟给用户下发消息
8. 可以在控制台观察日志看服务是否正常运行，消息是否下发成功