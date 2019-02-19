原文链接： [Motivations · RSocket](http://rsocket.io/docs/Motivations) - https://github.com/rsocket/rsocket  

<a href="https://github.com/rsocket/rsocket-website/tree/master/website/static/img"><img src="r-socket-pink.png" width="20%" align="right" /></a>

## 🍎 译序

译文由阿里中间件的 [罗毅(北纬)](https://yq.aliyun.com/articles/593279) 提供，感谢翻译！

关于`RSocket`包含三部分

- FAQ
- [动机](README.md)
- [协议](protocol.md)

# 动机

大型分布式系统往往通过模块化的方式来构建，不同团队可能采用不同的技术和不同的编程语言来实现其中的模块。这些模块需要可靠的通讯，并支持快速独立的演进。在分布式系统中，一个至关重要、需要考虑的因素是模块之间需要具备高效可扩展的通讯机制。这个因素将会显著的影响到用户能够感受到的网络延迟、以及构建并运行系统所需要消耗的资源。

在[Reactive 宣言](https://www.reactivemanifesto.org)中提及并由 [Reactive Streams](http://www.reactive-streams.org) 和 [Reactive Extensions](http://reactivex.io) 实现的架构模式倡导异步消息机制以及拥抱 request/response 之上的通讯模型。本文中的 "RSocket" 协议是一个遵循 "reactive" 准则的通讯协议。

以下是为什么重新定义一个新的协议的动机：

----------------------------------------

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->


- [消息驱动](#%E6%B6%88%E6%81%AF%E9%A9%B1%E5%8A%A8)
- [交互模型](#%E4%BA%A4%E4%BA%92%E6%A8%A1%E5%9E%8B)
    - [发射后不管](#%E5%8F%91%E5%B0%84%E5%90%8E%E4%B8%8D%E7%AE%A1)
    - [Request/Response (单一响应)](#requestresponse-%E5%8D%95%E4%B8%80%E5%93%8D%E5%BA%94)
    - [Request/Stream (多个 response，有限个)](#requeststream-%E5%A4%9A%E4%B8%AA-response%E6%9C%89%E9%99%90%E4%B8%AA)
    - [Channel](#channel)
- [行为](#%E8%A1%8C%E4%B8%BA)
    - [单个 response 对比多个 response](#%E5%8D%95%E4%B8%AA-response-%E5%AF%B9%E6%AF%94%E5%A4%9A%E4%B8%AA-response)
    - [双向](#%E5%8F%8C%E5%90%91)
    - [取消](#%E5%8F%96%E6%B6%88)
- [可恢复](#%E5%8F%AF%E6%81%A2%E5%A4%8D)
- [应用层流量控制](#%E5%BA%94%E7%94%A8%E5%B1%82%E6%B5%81%E9%87%8F%E6%8E%A7%E5%88%B6)
    - ["Reactive Streams" `request(n)` 异步拉取](#reactive-streams-requestn-%E5%BC%82%E6%AD%A5%E6%8B%89%E5%8F%96)
    - [租约](#%E7%A7%9F%E7%BA%A6)
- [多语言支持](#%E5%A4%9A%E8%AF%AD%E8%A8%80%E6%94%AF%E6%8C%81)
- [传输层上的灵活性](#%E4%BC%A0%E8%BE%93%E5%B1%82%E4%B8%8A%E7%9A%84%E7%81%B5%E6%B4%BB%E6%80%A7)
- [效率 & 性能](#%E6%95%88%E7%8E%87--%E6%80%A7%E8%83%BD)
- [比较](#%E6%AF%94%E8%BE%83)
    - [TCP & QUIC](#tcp--quic)
    - [WebSockets](#websockets)
    - [HTTP/1.1 & HTTP/2](#http11--http2)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

----------------------------------------

## 消息驱动

网络通讯是异步的。RSocket 协议遵从该点并将所有通讯模型都建模为单个网络连接上多路传输的消息流，并且，在这个连接上永远不会因为等待一个 response 而同步阻塞。

[Reactive 宣言](http://www.reactivemanifesto.org)申明：

> Reactive 系统依赖异步消息传递划分组件之间的边界，从而保证了松耦合、隔离、位置透明、并提供了通过消息传递错误的手段。明确的消息传递机制的运用，使得通过调整和观测系统中的消息队列并按需调整反压策略来进行负载管理、弹性、流控成为可能。非阻塞的通讯容许接受方仅在激活的时候消耗资源，从而减少了系统开销。


此外， [HTTP/2 FAQ](https://http2.github.io/faq/#why-is-http2-multiplexed) 对于为何使用长连接上的多路复用、面向消息的协议有很好的解释：

> HTTP/1.x 中存在一个所谓 "head-of-line blocking" 的问题，导致一条连接上在同一时间内只能有一个 request。


> HTTP/1.1 试图通过 pipeling 来解决这个问题，但是这个方案并不能完全的解决 (一个大的或者慢的 request 仍然会阻塞后续的 request)。另外，pipelining 这个方案部署起来十分困难，因为很多代理和服务器并不能正确的处理。


> 这种情况迫使客户端使用一系列启发式的手段 (或者称之为猜) 来决定当有多条连接的情况下（一个页面加载时往往有十条甚至更多的连接建立）请求应该由哪条连接来发送。这样做可能在性能上带来极大的影响，往往导致 “瀑布式” 的请求阻塞。


> 多路复用通过容许同一时间接受多个 request 和 response 消息通过很好的解决了这个问题。它甚至容许消息之间的交错传输。


> 这样，就达成了容许客户端在加载页面时为每一个来源只需要建立一条连接即可。


在这篇文章里还讨论了长连接：

> 在 HTTP/1 中，浏览器往往为每一个来源建立四到八条连接。由于许多网站引用了多个来源，这就意味着加载一个页面可能需要打开超过三十条连接。


> 一个应用在同一时刻打开如此多的连接打破了 TCP 协议中制定的很多假设。由于每一条连接上都存在一系列 response 数据的传回，会导致网络上缓冲区的溢出，从而导致网络拥塞和数据重传的发生。


> 此外，同时使用如此多的连接会霸占过多的网络资源，对于其他有礼貌的应用不公平(比如，VoIP)。


## 交互模型

一个不当的协议会增加一个系统的开发成本。一个不匹配的抽象会限制系统的设计，进而导致开发人员为了找到变通的手段来解决错误和性能上的问题而花费额外的时间。这个问题在多语言环境中还会被放大，因为不同的语言中变通方法是不一样的，并且团队之间也需要更多的协作。当下事实上的标准是 HTTP，这个协议中所有的内容都是关于 request/response。在一些场景下 request/response 并不是理想的通讯模型。

一个可能的例子是通知的推送。使用 request/response 模型迫使应用通过客户端不断发送请求到服务器端来检查是否有新的数据。服务器处理大量请求并告知客户端没有数据更新的例子在现实场景里屡见不鲜，这对于客户端、服务器、网络、金钱、基础设施、运维复杂度、以及根子上的系统可用性都是一种浪费。这对于接收通知的用户体验上也增加了延迟，原因是 polling 这种方式在减少延迟的尝试上是一种倒退。

由于这样和那样的原因，RSocket 协议被设计成不仅仅只有一种交互模型。下面描述的不同的交互模型使得系统设计有了新的可能性：

### 发射后不管

发射后不管 (Fire-and-forget) 是 request/response 模型中不需要返回 response 的一个特例。这个模型可以显著的减少性能上的开销，原因是它不但不用在网络上传输 response 消息，而且客户端和服务器不需要等待对应的 response 或者取消 request 从而避免了额外的处理逻辑。

这个交互模型在消息容许丢失的场景下有用，比如非关键的事件记录 (event logging) 场景。

使用这种模式的代码：

```java
Future<Void> completionSignalOfSend = socketClient.fireAndForget(message);
```

### Request/Response (单一响应)

标准的 request/response 语义仍然被支持，并且还会是 RSocket 连接上的主要的使用方式。这种 request/response 的交互可以被认为是 "只有一个 response 的消息流" 的特例，并且是单一网络连接上多路传输的异步消息。

客户端 "等待" 一个 response 消息的返回，从而看起来像是一个典型的 request/response，但是底层从来就不会同步阻塞。

使用这种模式的代码：

```java
Future<Payload> response = socketClient.requestResponse(requestPayload);
```

### Request/Stream (多个 response，有限个)

request/response 进一步的扩展是 request/stream，这个模式容许多个消息流式返回。可以把这种模式想象成 response 对象是一个 "collection" 或者一个 "list"，只不过不是通过单一的 reponse 返回，而是其中的每一个元素按序返回。

使用场景包括：

* 获取视频列表
* 获取某个类目下的所有商品
* 按行获取文件内容


使用这种模式的代码：

```java
Publisher<Payload> response = socketClient.requestStream(requestPayload);
```

### Channel

一个 channel 是双向的，并且在每个方向上都有一系列的消息传输。

一个能够借助这种交互模式的例子是：

* 客户端请求一组数据来构建世界的当前视图
* 当数据发生改变时，服务器将 delta/diff 发送回客户端
* 期间客户端也可以更新订阅，增加或者删除感兴趣的主题、条件、等等


如果没有双向的 channel，客户端就得取消初始的请求，重新发送新的请求并不得不重新接受所有的数据，而不是简单的更新订阅的主题，仅仅接受其中的改变。

使用这种模式的代码：

```java
Publisher<Payload> output = socketClient.requestChannel(Publisher<Payload> input);
```

## 行为

除了交互模式以外，其他的一些行为也会使得系统和应用在效率方面受益。

### 单个 response 对比多个 response

单个 response 和多个 response 中一个关键的区别是数据在 RSocket 上如何传输：一个 single-response 消息可能由多个 frame 传输，传输的连接是多路复用的，其他的消息可能也同时在此连接上传输。single-response 模式下，应用只能在接收完所有数据才能开始后续处理。另一方面，multi-response 零碎地发送数据。这样，用户在设计服务的时候可以借助 multi-response 的特性，在接收到第一份数据的时候就可以开始处理。

### 双向

RSocket 支持双向的请求，也就是说客户端和服务器可以互为对方的请求方和接收方。这样就容许一个客户端(比如，用户设备)成为来自服务器端请求的接收方。

例如：服务器可以询问客户端的 debug 信息、状态等。这样做可以减少系统扩容的压力，因为避免了成千上万无谓的来自客户端的数据上报，而替换为了服务端按需查询。这种行为也开启了目前还没有预见到的客户端和服务端新的交互模型。

### 取消

所有的消息流(包括 request 和 response)支持取消的行为使得服务器(消息回应方)可以有效的清理资源。比如，当客户端取消，或者离线，服务器有机会更早的终止相关的处理工作。这个行为不但对于 stream 和 subscription 这类交互模型是必要的，而且对于 request/response 这种模式更为有用，因为这样做对于运用类似 "backup requests" 这样的手段来处理尾延迟这类问题更加有效。 (更多相关信息请参阅[这里](http://highscalability.com/blog/2012/3/12/google-taming-the-long-latency-tail-when-more-machines-equal.html)，[这里](http://highscalability.com/blog/2012/6/18/google-on-latency-tolerant-systems-making-a-predictable-whol.html)，[这里](http://www.bailis.org/blog/doing-redundant-work-to-speed-up-distributed-queries/)，以及[这里](http://static.googleusercontent.com/external_content/untrusted_dlcp/research.google.com/en/us/people/jeff/Stanford-DL-Nov-2010.pdf))

## 可恢复

对于持久消息流的场景，尤其是那些服务来自移动客户端订阅的场景，如果在网络连接断开时所有的订阅必须重新建立，将会极大的影响性能开销。在以下的情况发生的时候这个问题会更加严重：网络连接立刻又重新建立起来，或者在 Wifi 和移动网络之间相互切换时。

RSocket 支持会话级别的恢复，容许通过一次简单的握手在新的网络连接上恢复客户端服务端之间的会话。

## 应用层流量控制

RSocket 支持两种形式的应用层流控来保护客户端和服务端资源不被滥用。

本协议被设计成既可以使用在数据中心、服务器到服务器之间的通讯，也适用于通过互联网在服务器和设备之间的通讯，比如：服务器和移动设备或者浏览器之间的通讯。

### "Reactive Streams" `request(n)` 异步拉取

这种形式的流控既适用于服务器-服务器场景，也适用于服务器-设备的场景。该流控形式借鉴了 Reactive Streams 中的 [Subscription.request(n)](https://github.com/reactive-streams/reactive-streams-jvm/blob/v1.0.0/README.md#3-subscription-code) 行为。在  [RxJava](https://github.com/ReactiveX/RxJava/)，[Reactor](https://github.com/reactor/reactor)，以及 [Akka Streams](http://doc.akka.io/docs/akka/2.4/scala/stream/index.html) 中都有这种所谓 "异步拉-推" 流控的实现。

RSocket 容许跨越网络边界在请求方和回应方之间(通常是客户端和服务端)构建 `request(n)` 的信号。这种从回应方向请求方的流量控制是通过在应用层的 Reactive Streams 语义来达成的，并且由于有界缓冲的接入使得流控与应用消费的速率相适配，而不是完全依靠网络层和传输层的缓冲。

相同的数据类型和做法也被 Java 9 中的 `java.util.concurrent.Flow` [suite of types](http://download.java.net/java/jdk9/docs/api/java/util/concurrent/Flow.Subscription.html) 采用。

### 租约

第二种类型的流控主要关注在一个数据中心内服务器-服务器之间的场景。该类型流控作用的前提下，一个回应方(通常是一个服务器)可以发送有关自己处理能力的租约给请求方，以期控制请求速率。在请求端，应用层的负载均衡将仅发送消息给通知过处理能力的回应方。基于这个来自服务器的信号，在数据中心的一个机器集群上实现一个更加智能的路由和负载均衡算法成为了可能。

## 多语言支持

上面提到的本协议的动机中很多都可以借助现有的协议、库、和技术得以实现。但是，这样往往导致与特定的实现强绑定，该实现必须能够做到跨语言、跨平台、跨技术栈。相反的，将交互模式和流量控制的行为定义在协议里，为不同语言中的实现提供了契约。与普遍存在的 HTTP/1.1 request/response 相比，通过更丰富的行为增强了多语言交互，同时，也使得 Reactive Stream 应用级别的跨语言流控成为可能 (而不仅仅是 Reactive Streams 最初定义的那样只适用于 Java)。

## 传输层上的灵活性

就如同 HTTP request/response 不是应用唯一的通讯手段，TCP 也不是传输层唯一的选择，甚至在某些场景下不是最佳选择。所以，RSocket 容许根据环境、设备的能力以及性能上的诉求来选择底层的传输层。RSocket (应用协议)可以基于 WebSockets、TCP、以及  [Aeron](https://github.com/real-logic/Aeron)，并且，也可以基于其他类似 TCP 的传输层协议，比如： [Quic](https://www.chromium.org/quic)。

也许更重要的原因是，基于 TCP、WebSockets 以及 Aeron 很容易实现。比如，采用 WebSockets 往往比较有吸引力，不过它暴露的是 framing 的语义，所以需要额外定义应用层的协议，这往往需要花费极大的努力才能实现。TCP 甚至都不提供 framing 的语义。所以，最终大部分应用只能采用 HTTP/1.1 中的 request/response，并因此丧失了使用同步 request/response 以外的交互模型的好处。

因此 RSocket 选择了在这些网络传输层之上定义应用层语义，从而使得它们可以根据实际情况来自由选择。这篇文档的后面会提供一个与其他协议的简要比较，目的是在决定引入一个新的应用层协议之前试试 WebSockets 和 Aeron 是否可以利用。

## 效率 & 性能

一个使用网络资源低效的协议(重复的握手、连接建立和取消、臃肿的消息格式等)往往极大的增加一个系统中可察觉的延迟。另外，如果没有流量控制的语义，一个实现很差的模块在其所依赖的服务变慢的时候过载整个系统，很有可能会引起重试风暴从而增加系统的压力。[Hystrix](https://github.com/Netflix/Hystrix/wiki#problem) 是一个试图解决同步 request/response 问题的例子，但是它也[带来了](https://github.com/Netflix/Hystrix/wiki/FAQ#what-is-the-processing-overhead-of-using-hystrix)额外的系统负担和复杂度。

还有，一个选错的通讯协议还会浪费服务器资源(CPU、内存、网络带宽)。对于较小规模的部署也许还可以接受，但是对于具有成百上千节点的大型系统，很小程度的效率低的问题会被急剧放大。虽然现在服务器资源很便宜但是也有上限，因为 footprint 越大，留给纵向扩展的空间就越小。即便有很好的工具，管理大型集群也是十分昂贵和笨拙的。而且常常被忽略的是，集群越大，管理的复杂度也就越大，并进一步成为可用性上的隐患。

RSocket 寻求的是：

* 通过支持非阻塞、多路复用、异步的应用层通讯来降低可察觉的延迟，提升系统的效率。同时该通讯还支持通过任何语言来控制多个传输通道上的流量。


* 通过以下措施降低硬件规模 (也就是成本和维护的复杂度)：
  * 通过二进制编码增加 CPU 和内存的使用效率
  * 容许持久连接来避免冗余无效的工作


* 通过以下的手段来降低可察觉的延迟：
  * 避免握手从而避免了与之对应的网络往返
  * 通过二进制编码的使用降低了计算时间
  * 分配更少内存从而降低 GC 开销


## 比较

以下是在决定引入 RSocket 之前对一些现有协议的简要调研。这个调研不是完整全面的调研，同时也不是针对这些协议的批评，这些协议都很适合它们最初被引入的场景。本章节的主要目的是想说明现有的这些协议都不能很好的满足 RScocket 之所以被引入的动机。

背景：

* RSocket 是 OSI 5/6 层或者 TCP/IP 应用层的协议。
* RSocket 被设计成全双工、二进制传输，并具备类似 TCP 行为的协议(更详细的描述请参阅[这里](protocol.md#transport-protocol))。


### TCP & QUIC

没有 framing 和应用语义。必须提供一个应用协议。

### WebSockets

没有应用语义，只有 framing。必须提供一个应用协议。

### HTTP/1.1 & HTTP/2

HTTP 为应用协议的构建提供了一个刚刚够用的能力，但是一个应用层的协议仍然需要在其上定义。对于定义应用语义来说它是不够的(([Google 的 GRPC](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) 就是一个在 HTTP/2 之上构建这些语义的例子)。

这些有限的应用语义通常要求应用层协议定义以下几点：
* 使用 GET、POST 或者 PUT 发送请求
* 使用 Normal、Chunked 或者 SSE 发送回应
* payload 的 MimeType
* 带有标准错误码的错误消息
* 客户端如何处理状态码的行为
* 在持久连接上使用 SSE 来实现服务器到客户端的推送


HTTP 中没有从回应方（往往是服务器）到请求方（往往是客户端）的流控机制。HTTP/2 有字节层面的流控，但是在应用级别并没有。通知请求方（通常来自服务器端）可用性（比如请求失败）的机制低效并且痛苦。协议缺乏对类似发射后不管等交互模式的支持，同时 streaming 模式支持也不完备 (chunked 编码和 SSE 仅支持 ASCII)。

虽然 REST 被广泛使用，但是它在定义应用语义方面是不充分的也是不合适的。

那么 HTTP/2 呢？难道它不是用来解决 HTTP/1 的问题，以及 RSocket 协议中提出的这些动机吗？

很不幸的是，并不能。HTTP/2 在浏览器以及 request/resonse 文档传输等方面可以很好的工作，但是它没有为应用暴露出如同本文描述的那种行为和交互模式。

下文引用了 HTTP/2 [spec](https://http2.github.io/http2-spec/) 和 [FAQ](https://http2.github.io/faq/) 以方便理解 HTTP/2 被引入的上下文：

> “HTTP 现有的语义并未发生改变。”


> “… 从应用的角度，绝大部分协议中的特性并未改变 …”


> "这个工作专注在 wire protocol 上的修正 - 比如，如何在网络上传输 HTTP header、method 等等，而不是改变 HTTP 的语义。"


另外，"push promises" 主要专注在为标准 web 浏览行为填充浏览器缓存：

> “推送的回应总是与一个特定的来自客户端的请求相关联。”


这就代表着我们仍然需要 SSE 或者 WebSockets (并且 SSE 是文本协议，要求编码是 Base64 而不是 UTF-8) 来做推送。

HTTP/2 比 HTTP/1.1 好，主要是从网站上如何获取文档的方面来说的。从应用方面来说，我们可以做的比 HTTP/2 更好。

　

-----------------

　　　　　　[协议 »](protocol.md)
