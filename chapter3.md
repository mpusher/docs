# 协议
### 数据结构

  ![](MPush数据包结构图.png)
### Header说明
1. length表示body的长度
2. cmd表示协议消息类型
3. checkcode是根据body生成的一个校验码
4. flags表示当前包使用的一些特性，比如是否启用加密，是否启用压缩
5. sessionId消息会话标识用于消息响应
6. lrc用于校验header
