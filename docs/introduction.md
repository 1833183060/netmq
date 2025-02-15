简介
=====

So you are looking for a messaging library, you might have become frustrated with WCF or MSMQ (we know we have been there too) and heard that ZeroMQ is very fast and then you got here, NetMQ, the .NET port of ZeroMQ (also known as ØMQ).

So yes, NetMQ is a messaging library and it is fast, but NetMQ has a bit of learning curve. Hopefully you will pick it up quickly.


## 从哪里开始

ZeroMQ和NetMQ并不是你下载的一个库，看看一些代码示例，就可以的。它背后有一套理念，要用好它你需要理解这套理念.所以最好的起点是 [ZeroMQ guide](http://zguide.zeromq.org/page:all).先阅读ZeroMQ guide，甚至可以读两遍，然后再回到这里.


## ZeroMQ中的 Zero

The philosophy of ZeroMQ starts with the _zero_. The zero is for zero broker (ZeroMQ is brokerless), zero latency, zero cost (it's free), and zero administration.

More generally, "zero" refers to the culture of minimalism that permeates the project. We add power by removing complexity rather than by exposing new functionality.


## 获取库

你可以从[NuGet](https://nuget.org/packages/NetMQ/)获取 NetMQ 库。


## 发送和接收

由于NetMQ全都是关于套接字的，人们自然期望能够发送/接收。由于这是NetMQ的一个常见领域，因此有一个关于[发送和接收](receiving-sending.md)的专用文档页面。


## 第一个例子

因此，让我们从一些代码开始，做一个“Hello world”示例(当然)。

### 服务端

``` csharp
using (var server = new ResponseSocket())
{
    server.Bind("tcp://*:5555");
    while (true)
    {
        var message = server.ReceiveFrameString();
        Console.WriteLine("Received {0}", message);
        // processing the request
        Thread.Sleep(100);
        Console.WriteLine("Sending World");
        server.SendFrame("World");
    }
}
```

服务器创建一个response类型的套接字 (你可以在 [request-response](request-response.md) 章节读到更多内容), 绑定到端口 5555，然后等待消息。

你也可以看到我们没有配置，我们只是发送字符串。 NetMQ可以发送的远不止字符串, 但是NetMQ没有任何序列化功能，你必须自己手动完成, 但是你将在下面学到一些很酷的技巧([Multipart messages](#multipart-messages)).

### 客户端

``` csharp
using (var client = new RequestSocket())
{
    client.Connect("tcp://localhost:5555");
    for (int i = 0; i < 10; i++)
    {
        Console.WriteLine("Sending Hello");
        client.SendFrame("Hello");
        var message = client.ReceiveFrameString();
        Console.WriteLine("Received {0}", message);
    }
}
```

客户端创建一个request类型的套接字，连接并开始发送消息。

 `Send` 和 `Receive` 方法都是阻塞的 (缺省情况下). 对于接收者来说，这很简单: 如果没有消息，该方法将阻塞。 用于发送则更复杂些，并取决于套接字类型。对于request 套接字, 如果达到高水位或没有对端连接，该方法将阻塞。

你可以调用' TrySend '和' TryReceive '来避免等待。如果操作会阻塞，则返回' false '。

``` csharp
string message;
if (client.TryReceiveFrameString(out message))
    Console.WriteLine("Received {0}", message);
else
    Console.WriteLine("No message received");
```


## Bind vs Connect

In the above you may have noticed that the server used `Bind` while the client used `Connect`. Why is this, and what is the difference?

ZeroMQ creates queues per underlying connection. If your socket is connected to three peer sockets, then there are three messages queues behind the scenes.

With `Bind`, you allow peers to connect to you, thus you don't know how many peers there will be in the future and you cannot create the queues in advance. Instead, queues are created as individual peers connect to the bound socket.

With `Connect`, ZeroMQ knows that there's going to be at least a single peer and thus it can create a single queue immediately. This applies to all socket types except ROUTER, where queues are only created after the peer we connect to has acknowledge our connection.

Consequently, when sending a message to bound socket with no peers, or a ROUTER with no live connections, there's no queue to store the message to.

### When should I use bind and when connect?

As a general rule use bind from the most stable points in your architecture, and use connect from dynamic components with volatile endpoints. For request/reply, the service provider might be point where you bind and the client uses connect. Just like plain old TCP.

If you can't figure out which parts are more stable (i.e. peer-to-peer), consider a stable device in the middle, which all sides can connect to.

You can read more about this at the [ZeroMQ FAQ](http://zeromq.org/area:faq) under the _"Why do I see different behavior when I bind a socket versus connect a socket?"_ section.


## Multipart messages

ZeroMQ/NetMQ work on the concept of frames, as such most messages are considered to be made up of one or more frames. NetMQ provides some convenience methods to allow you to send string messages. You should however, familiarise yourself with the the idea of multiple frames and how they work.

This is covered in much more detail in the [Message](message.md) documentation page.


## Patterns

ZeroMQ (and therefore NetMQ) is all about patterns and building blocks. The <a href="http://zguide.zeromq.org/page:all" target="_blank">ZeroMQ guide</a> covers everything you need to know to help you with these patterns. You should make sure you read the following sections before you attempt to start work with NetMQ.

+ <a href="https://zguide.zeromq.org/docs/chapter2/" target="_blank">Chapter 2 - Sockets and Patterns</a>
+ <a href="https://zguide.zeromq.org/docs/chapter3/" target="_blank">Chapter 3 - Advanced Request-Reply Patterns</a>
+ <a href="https://zguide.zeromq.org/docs/chapter4/" target="_blank">Chapter 4 - Reliable Request-Reply Patterns</a>
+ <a href="https://zguide.zeromq.org/docs/chapter5/" target="_blank">Chapter 5 - Advanced Pub-Sub Patterns</a>


NetMQ also has some examples of a few of these patterns written using the NetMQ APIs. Should you find the pattern you are looking for in the <a href="http://zguide.zeromq.org/page:all" target="_blank">ZeroMQ guide</a> it should be fairly easy to translate that into NetMQ usage.

Here are some links to the patterns that are available within the NetMQ codebase:

+ <a href="https://github.com/NetMQ/Samples/blob/master/src/Brokerless%20Reliability%20(Freelance%20Pattern)/Model%20One" target="_blank">Brokerless Reliability Pattern - Freelance Model one</a>
+ <a href="https://github.com/NetMQ/Samples/blob/master/src/Load%20Balancing%20Pattern" target="_blank">Load Balancer Patterns</a>
+ <a href="https://github.com/NetMQ/Samples/blob/master/src/Pirate%20Pattern/Lazy%20Pirate" target="_blank">Lazy Pirate Pattern</a>
+ <a href="https://github.com/NetMQ/Samples/blob/master/src/Pirate%20Pattern/Simple%20Pirate" target="_blank">Simple Pirate Pattern</a>

For other patterns, the <a href="https://zguide.zeromq.org/" target="_blank">ZeroMQ guide</a>
will be your first port of call

ZeroMQ patterns are implemented by pairs of sockets of particular types. In other words, to understand ZeroMQ patterns you need to understand socket types and how they work together. Mostly, this just takes study; there is little that is obvious at this level.

The built-in core ZeroMQ patterns are:

+ [**Request-reply**](request-response.md), which connects a set of clients to a set of services. This is a remote procedure call and task distribution pattern.
+ [**Pub-sub**](pub-sub.md), which connects a set of publishers to a set of subscribers. This is a data distribution pattern.
+ **Pipeline**, which connects nodes in a fan-out/fan-in pattern that can have multiple steps and loops. This is a parallel task distribution and collection pattern.
+ **Exclusive pair**, which connects two sockets exclusively. This is a pattern for connecting two threads in a process, not to be confused with "normal" pairs of sockets.

These are the socket combinations that are valid for a connect-bind pair (either side can bind):

+ `PublisherSocket` and `SubscriberSocket`
+ `RequestSocket` and `ResponseSocket`
+ `RequestSocket`  and `RouterSocket`
+ `DealerSocket` and `ResponseSocket`
+ `DealerSocket` and `RouterSocket`
+ `DealerSocket` and `DealerSocket`
+ `RouterSocket` and `RouterSocket`
+ `PushSocket` and `PullSocket`
+ `PairSocket` and `PairSocket`

Any other combination will produce undocumented and unreliable results, and future versions of ZeroMQ will probably return errors if you try them. You can and will, of course, bridge other socket types via code, i.e., read from one socket type and write to another.


## Options

NetMQ comes with several options that will effect how things work.

Depending on the type of sockets you are using, or the topology you are attempting to create, you may find that you need to set some ZeroMQ options. In NetMQ this is done using the `NetMQSocket.Options` property.

Here is a listing of the available options that you may set on a `NetMQSocket`. It is hard to say exactly which of these values you may need to set, as that obviously depends entirely on what you are trying to achieve. All I can do is list the options, and make you aware of them. So here they are:

+ `Affinity`
+ `BackLog`
+ `CopyMessages`
+ `Correlate`
+ `DelayAttachOnConnect`
+ `Endian`
+ `GetLastEndpoint`
+ `IPv4Only`
+ `Identity`
+ `Linger`
+ `MaxMsgSize`
+ `MulticastHops`
+ `MulticastRate`
+ `MulticastRecoveryInterval`
+ `ReceiveHighWaterMark`
+ `ReceiveMore`
+ `ReceiveBuffer`
+ `ReconnectInterval`
+ `ReconnectIntervalMax`
+ `Relaxed`
+ `SendHighWaterMark`
+ `SendTimeout`
+ `SendBuffer`
+ `TcpAcceptFilter`
+ `TcpKeepAlive`
+ `TcpKeepaliveIdle`
+ `TcpKeepaliveInterval`
+ `XPubVerbose`

We will not be covering all of these here, but shall instead cover them in the areas where they are used. For now just be aware that if you have read something in the <a href="http://zguide.zeromq.org/page:all" target="_blank">ZeroMQ guide</a> that mentions some option, that this is most likely the place you will need to set it/read from it.  Also, the socket options are described in the <a href="http://api.zeromq.org/master:zmq-setsockopt" target="_blank">zmq_setsockopt</a> documentation.
