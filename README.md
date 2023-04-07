<img src="https://cdn.rawgit.com/zeromq/netmq/master/img/NetMQLogo.svg" width="350" />

[![GitHub Actions CI](https://github.com/zeromq/netmq/actions/workflows/CI.yml/badge.svg)](https://github.com/zeromq/netmq/actions/workflows/CI.yml)
[![NetMQ AppVeyor Build](https://ci.appveyor.com/api/projects/status/as5fiw8a3suw53iu/branch/master?svg=true)](https://ci.appveyor.com/project/somdoron/netmq-2bhss) [![codecov](https://codecov.io/gh/zeromq/netmq/branch/master/graph/badge.svg)](https://codecov.io/gh/zeromq/netmq) [![NetMQ NuGet version](https://img.shields.io/nuget/v/NetMQ.svg)](https://www.nuget.org/packages/NetMQ/) [![NetMQ NuGet prerelease version](https://img.shields.io/nuget/vpre/NetMQ.svg)](https://www.nuget.org/packages/NetMQ/)

NetMQ 是一个用百分百原生C# 实现的 轻量级消息库ZeroMQ的 移植.

NetMQ使用传统上由专门的消息传递中间件产品提供的特性扩展了标准套接字接口。 NetMQ套接字提供了一个
异步消息队列的抽象、多种消息传递模式、
消息过滤(订阅)、无缝访问多个传输
协议等等。

## 安装

你可以通过[NuGet](https://nuget.org/packages/NetMQ/) 安装 NetMQ。
## 版本

目前维护了两个版本
版本3（稳定的NetMQ）和版本4（版本3一样，但没有废弃的代码）。
你可以在 Nuget找到这两个版本, 更多信息请阅读 [Migrating-to-v4](https://github.com/zeromq/netmq/wiki/Migrating-to-v4).

此存储库适用于版本4, 版本3位于: https://github.com/NetMQ/NetMQ3-x.

## 使用 / 文档

使用NetMQ前,一定要阅读 [ZeroMQ Guide](http://zguide.zeromq.org/).

NetMQ文档可以在 [netmq.readthedocs.org](http://netmq.readthedocs.org/en/latest/)找到. 感谢 [Sacha Barber](http://www.codeproject.com/Members/Sacha-Barber) 他同意做这份文档。

你可以在https://github.com/NetMQ/Samples 找到NetMQ 的示例，这些示例由不同用户贡献。

还有一些博客文章，你可以在这里阅读:

+ [Somdoron's blog](http://somdoron.com/category/netmq/)
+ [Hello World](http://sachabarbs.wordpress.com/2014/08/19/zeromq-1-introduction/)
+ [The Socket Types](http://sachabarbs.wordpress.com/2014/08/21/zeromq-2-the-socket-types-2/)
+ [Socket Options/Identity and SendMore](http://sachabarbs.wordpress.com/2014/08/26/zeromq-3-socket-optionsidentity-and-sendmore/)
+ [Multiple Socket Polling](http://sachabarbs.wordpress.com/2014/08/27/zeromq-4-multiple-sockets-polling/)
+ [Sending From Multiple Sockets](https://sachabarbs.wordpress.com/2014/08/30/zeromq-sending-from-multiple-sockets/)
+ [Divide And Conquer](http://sachabarbs.wordpress.com/2014/09/01/zeromq-6-divide-and-conquer/)


这里有一个简单的例子:

```csharp
using (var server = new ResponseSocket("@tcp://localhost:5556")) // bind
using (var client = new RequestSocket(">tcp://localhost:5556"))  // connect
{
    // Send a message from the client socket
    client.SendFrame("Hello");

    // Receive the message from the server socket
    string m1 = server.ReceiveFrameString();
    Console.WriteLine("From Client: {0}", m1);

    // Send a response back from the server
    server.SendFrame("Hi Back");

    // Receive the response from the client socket
    string m2 = client.ReceiveFrameString();
    Console.WriteLine("From Server: {0}", m2);
}
```

## 参与

We need help, so if you have good knowledge of C# and ZeroMQ just grab one of the issues and add a pull request.
We are using [C4.1 process](http://rfc.zeromq.org/spec:22), so make sure you read this before.

Regarding coding standards, we are using C# coding styles, to be a little more specific: we are using `camelCase` for variables and fields (with `m_` prefix for instance members and `s_` for static fields) and `PascalCase` for methods, classes and constants. Make sure you are using 'Insert Spaces' and 4 for tab and indent size.

You can also help us by:

* Joining our [mailing list](https://groups.google.com/d/forum/netmq-dev?hl=en) and be an active member
* Writing tutorials in the github wiki
* Writing about the project in your blog (and add a pull request with a link to your blog at the bottom of this page)

## 咨询与支持
Name | Email | Website | Info
-----|-------|---------|-----
Doron Somech | somdoron@gmail.com | http://somdoron.com | Founder and maintainer of NetMQ, expertise in Fintech and high performance scalable systems.

If you are providing support and consulting for NetMQ please make a pull request and add yourself to the list.

## 关于向后兼容性的重要注意事项

Since version 3.3.07 NetMQ changed the number serialization from Little Endian to Big Endian to be compatible with ZeroMQ.
Any NetMQ version prior to 3.3.0.7 is not compatible with the new version. To support older versions you can set Endian option on a NetMQ socket to Little Endian,
however doing so will make it incompatible with ZeroMQ.

We recommend to update to the latest version and use Big Endian which is now the default behavior.

## 邮件列表

You can join our mailing list [here](https://groups.google.com/d/forum/netmq-dev?hl=en). 

## 谁拥有NetMQ?

NetMQ is owned by all its authors and contributors. 
This is an open source project licensed under the LGPLv3. 
To contribute to NetMQ please read the [C4.1 process](http://rfc.zeromq.org/spec:22), it's what we use.
There are open issues in the issues tab that still need to be taken care of, feel free to pick one up and submit a patch to the project.

## 构建服务器

![Code Better](http://www.jetbrains.com/img/banners/Codebetter300x250.png)

[YouTrack by JetBrains - keyboard-centric bug tracker](http://www.jetbrains.com/youtrack)
