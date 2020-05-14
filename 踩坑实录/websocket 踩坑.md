# 记一次 WebSocket 踩坑

问题描述：

当 WebSocket 发送大量实时消息时，通过前端的 Console.log 发现存在部分消息前端根本没有收到的情况，与此同时观察后端日志，发现 WebSocket 抛出了`The remote endpoint was in state [TEXT_PARTIAL_WRITING] which is an invalid state for called method` 异常。

如果 WebSocket 所抛出的异常是丢失消息的根本原因，那么对于一些实时性要求高的系统产生的影响是致命的。

后来通过 Google 发现了一篇关于此异常的帖子，大概意思就是 Springboot 下默认的 WebSocketSession 实现不支持并发，问题来自于 Tomcat，有人对 Tomcat 提出该异常，但 Tomcat 似乎不认为这是一个问题，因为 Tomcat 已经在相关代码处说明了，不要在高并发的情况下使用。（相关链接：
[RemoteEndpoint.Async#sendText(String, SendHandler) not thread safe](http://bz.apache.org/bugzilla/show_bug.cgi?id=56026)）

> 看到这个我的内心是崩溃的，但只要思想不滑坡，办法总比困难多。

初步的想法是：在 WebSocketSession.sendMessgae() 方法前加入队列，一条条发消息。但可能造成的问题是，消息消费速度不及消息生产速度，导致消息积压，消息实时性降低。

后来观察 WebSocketSession 的默认实现是一个名为 StandardWebSocketSession 的类，该类的 sendMessage 方法正是调用了之前那篇帖子里说的 RemoteEndpoint.sendText()，那么问题思路就出来了，如何更换一个支持并发的 WebSocketSession 实现就行了。

最后，发现了一个来自 springframework 的类：

**ConcurrentWebSocketSessionDecorator**

不得不说，Spring 的开发人员真的很厉害，为广大开发者考虑了很多东西，使用了这个类之后，之前的问题引刃而解。

完。