原文链接： [Protocol · RSocket](http://rsocket.io/docs/Protocol) - https://github.com/rsocket/rsocket  

<a href="https://github.com/rsocket/rsocket-website/tree/master/website/static/img"><img src="r-socket-pink.png" width="20%" align="right" /></a>

## 🍎 译序

译文由阿里中间件的 [罗毅(北纬)](https://yq.aliyun.com/articles/593279) 提供，感谢翻译！

关于`RSocket`包含三部分

- FAQ
- [动机](README.md)
- [协议](protocol.md)

# 协议

## 状态

本协议目前处于草案状态。
当前协议版本是 __0.2__ (主版本：0，辅版本：2)。本版本是 1.0 发布版本的候选。不久的将来会为了发布 1.0 版本在 Java 和 C++ 的实现上做最终的测试。

## 介绍

为异步、双向的 [Reactive Streams](http://www.reactive-streams.org/) 语义指定一个应用层的协议。更多细节请参阅  [rsocket.io](http://rsocket.io/)。

RSocket 假设了一种操作范式。这些假设包括：

* 一对一的通讯
* 没有被代理的通讯。或者被代理的时候，RSocket 语义和假设被代理遵从。
* 协议不会在 [传输协议](#transport-protocol) 会话之间保存状态

本文中使用的关键词遵从  [RFC 2119](https://tools.ietf.org/html/rfc2119) 中的含义。

所有字段的字节序是 big endian。

## 目录

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->


- [术语](#%E6%9C%AF%E8%AF%AD)
- [版本号说明](#%E7%89%88%E6%9C%AC%E5%8F%B7%E8%AF%B4%E6%98%8E)
    - [跨版本兼容性](#%E8%B7%A8%E7%89%88%E6%9C%AC%E5%85%BC%E5%AE%B9%E6%80%A7)
- [数据和元信息](#%E6%95%B0%E6%8D%AE%E5%92%8C%E5%85%83%E4%BF%A1%E6%81%AF)
- [组帧 (Framing)](#%E7%BB%84%E5%B8%A7-framing)
    - [Transport 协议](#transport-%E5%8D%8F%E8%AE%AE)
    - [组帧 (Framing) 协议的用法](#%E7%BB%84%E5%B8%A7-framing-%E5%8D%8F%E8%AE%AE%E7%9A%84%E7%94%A8%E6%B3%95)
    - [组帧格式](#%E7%BB%84%E5%B8%A7%E6%A0%BC%E5%BC%8F)
    - [Frame 头的格式](#frame-%E5%A4%B4%E7%9A%84%E6%A0%BC%E5%BC%8F)
    - [Stream 标识](#stream-%E6%A0%87%E8%AF%86)
    - [Frame 类型](#frame-%E7%B1%BB%E5%9E%8B)
- [恢复操作](#%E6%81%A2%E5%A4%8D%E6%93%8D%E4%BD%9C)
    - [假设](#%E5%81%87%E8%AE%BE)
    - [隐式位置](#%E9%9A%90%E5%BC%8F%E4%BD%8D%E7%BD%AE)
    - [客户端生命管理](#%E5%AE%A2%E6%88%B7%E7%AB%AF%E7%94%9F%E5%91%BD%E7%AE%A1%E7%90%86)
    - [恢复操作](#%E6%81%A2%E5%A4%8D%E6%93%8D%E4%BD%9C-1)
    - [身份标识的处理](#%E8%BA%AB%E4%BB%BD%E6%A0%87%E8%AF%86%E7%9A%84%E5%A4%84%E7%90%86)
- [连接建立](#%E8%BF%9E%E6%8E%A5%E5%BB%BA%E7%AB%8B)
    - [协商](#%E5%8D%8F%E5%95%86)
    - [不带 LEASE 的序列](#%E4%B8%8D%E5%B8%A6-lease-%E7%9A%84%E5%BA%8F%E5%88%97)
    - [带 LEASE 的序列](#%E5%B8%A6-lease-%E7%9A%84%E5%BA%8F%E5%88%97)
- [分段和重组](#%E5%88%86%E6%AE%B5%E5%92%8C%E9%87%8D%E7%BB%84)
- [Stream 序列和存活时间](#stream-%E5%BA%8F%E5%88%97%E5%92%8C%E5%AD%98%E6%B4%BB%E6%97%B6%E9%97%B4)
    - [Request Response](#request-response)
    - [Request Fire-n-Forget](#request-fire-n-forget)
    - [Request Stream](#request-stream)
    - [Request Channel](#request-channel)
- [流量控制](#%E6%B5%81%E9%87%8F%E6%8E%A7%E5%88%B6)
    - [Reactive Streams 语义](#reactive-streams-%E8%AF%AD%E4%B9%89)
    - [租约语义](#%E7%A7%9F%E7%BA%A6%E8%AF%AD%E4%B9%89)
    - [服务质量和优先级](#%E6%9C%8D%E5%8A%A1%E8%B4%A8%E9%87%8F%E5%92%8C%E4%BC%98%E5%85%88%E7%BA%A7)
- [意外的处理](#%E6%84%8F%E5%A4%96%E7%9A%84%E5%A4%84%E7%90%86)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## 术语

* __Frame__: 一个单一的消息，其中包含了一个请求、一个回应、或者协议的处理。
* __Fragment__: 一个应用消息的一部分，被分段以便可以被包含在一个 Frame 中。参见 [分段与重组](#fragmentation-and-reassembly).
* __Transport__: 用于搭载 RSocket 协议的协议。WebSockets、TCP、或者 Aeron 中的一个。Transport **必须** 提供在 [transport protocol](#transport-protocol) 章节中提到的能力。
* __Stream__: 操作单位（request/response 等）。参见[动机](README.md)。
* __Request__: 一个 stream 请求。可能是四种类型中的一个。也可以是请求更多的请求或者说取消上一次请求的请求。
* __Payload__: 一个 stream 消息（上游或者下游）。包含与上次请求创建的 stream 想关联的数据。在 Reactive Streams 和 Rx 中这代表 'onNext' 事件。
* __Complete__: 终止一个 stream 上事件的发送并示意成功完成。在 Reactive Streams 和 Rx 中代表 'onComplete' 事件。
  * 在本文档中，一个带有 Complete 标志位的 frame (PAYLOAD 或者 REQUEST_CHANNEL) 有时也看做是 COMPLETE，只要该 frame 的引用在语义上是关于 Complete 位/事件即可。
* __Client__: 发起连接的一方。
* __Server__: 接受来自客户端连接的一方。
* __Connection__: 客户端和服务端之间的 transport 会话实例。
* __Requester__: 发送请求的一方。一条连接上最多有两个请求方。一头一个。
* __Responder__: 接受请求的一方。一条连接上最多有两个回应方。一头一个。

## 版本号说明

RSocket 的版本由一个数字主版本和一个数字辅版本组成。

### 跨版本兼容性

RSocket 假设所有版本（包括主版本和辅版本）都保持向前兼容性。
一个客户端可以通过 [Setup Frame](#frame-setup) 来传递它所支持的版本。
由服务端自主决定是否接受来自客户端的比其所能支持的版本更低的版本。

## 数据和元信息

RSocket 为应用提供了将 payload 区分成两种类型的机制。数据和原信息。至于二者之间的差别由应用自主定义。

以下是数据与元信息的一些特征。

* 元信息可以使用与数据不同的编码。
* 元信息上可以"附着"(比如，与之关联)以下的实体：
  * 通过元信息推送和连接 (Stream ID 为 0)
  * 单独的 Request 或者 Payload (上游或者下游)

## 组帧 (Framing)

### Transport 协议

RSocket 协议使用更底层的 transport 协议来搭载 RSocket 的 frame。一个 transport 协议 **必须** 提供以下能力： 

1. 单播[可靠传输](https://en.wikipedia.org/wiki/Reliability_(computer_networking))。
2. [面向连接](https://en.wikipedia.org/wiki/Connection-oriented_communication)以及保持 frame 有序。在 Frame B 之前发送的 Frame A 必须按照原始顺序首先到达。也就是说，如果 Frame A 也是由 Frame B 的源头发送的话，那么 Frame A 永远应该早于 Frame B 抵达。另一方面，跨源头的顺序不会保证。
3. 假设 [FCS](https://en.wikipedia.org/wiki/Frame_check_sequence) 在 transport 协议或者 MAC 层的每一跳中运用。但是对于恶意攻击的保护没有做任何假设。

实现在处理协议的过程中**也许**会"关闭"一个 transport 连接。当这种情况发生时，可以认为连接上没有更多的 frame 要发送，剩余的 frame 将会被忽略。

本文中描述的 RSocket 是基于 TCP、WebSocket、Aeron、和 [HTTP/2 streams](https://http2.github.io/http2-spec/#StreamsLayer) 作为 transport 协议来设计和测试的。

### 组帧 (Framing) 协议的用法

RSocket 所支持的 transport 协议中有些不支持保持消息边界的特定的组帧方式。对于这些协议，**必须**使用一个组帧协议来确保在 RSocket frame 的前面包含该 RSocket Frame 的长度。

如果 transport 协议保存了消息的边界，例如，提供了兼容组帧 (compatible framing)，那么 Frame 长度字段**必须**忽略。但是，如果 transport 协议仅仅提供了一个 stream 的抽象，或者合并消息同时不保存边界，或者使用了多种 transport 协议，那么**必须**使用 frame 头。

| Transport 协议  | 是否需要 Frame 长度字段 |
| :------------ | :-------------- |
| TCP           | __是__           |
| WebSocket     | __否__           |
| Aeron         | __否__           |
| HTTP/2 Stream | __是__           |

### 组帧格式

当使用一个提供组帧能力的 transport 协议时，RSocket frame 被简单的封装在 transport 协议的消息体里。

```
    +-----------------------------------------------+
    |                RSocket Frame          ...
    |                                              
    +-----------------------------------------------+
```

当使用的 transport 协议不提供兼容的组帧能力，**必须**在 RSocket Frame 之前增加 Frame 长度字段。

```
     0                   1                   2
     0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3
    +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
    |                    Frame Length               |
    +-----------------------------------------------+
    |                RSocket Frame          ...
    |                                              
    +-----------------------------------------------+
```

* __Frame 长度__: (24 位 = 最大值 16,777,215) 无符号 24-bit 整形，表示 Frame 的字节长度。不包含 Frame 长度字段本身。

__注意__: 字节序是 big endian。

### Frame 头的格式

RSocket frames 开头部分是 RSocket Frame 头。Frame 头的布局如下：

```
     0                   1                   2                   3
     0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
    +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
    |0|                         Stream ID                           |
    +-----------+-+-+---------------+-------------------------------+
    |Frame Type |I|M|     Flags     |     Depends on Frame Type    ...
    +-------------------------------+
```

* __Stream ID__: (31 位 = 最大值 2^31-1 = 2,147,483,647) 无符号 31-bit 整数，代表当前帧所属的 stream 的标识，如果是 0，则代表整个连接。
  * 对于支持取消多路传输 (demultiplexing) 的协议，比如 HTTP/2，在所有参与方同意的前提下，Stream ID 字段可以忽略。也就是说，协商和同意的责任交给了 transport 协议这一层。
* __Frame Type__: (6 位 = 最大值 63) Frame 类型。
* __Flags__: (10 位) 当发送的 Frame 还没有被接收方解析时，任何与 frame 类型无关的 Flags 都应该被置为 0。一般来说，Flags 依赖 Frame 类型，但是所有的 frame 类型**必须**为以下的 flags 提供空间：
  * (__I__)gnore: 对于不能理解的 Frame 可以忽略
  * (__M__)etadata: 有 Metadata 存在

__注意__: 字节序是 big endian。

#### 处理 Ignore 标记

 (__I__)gnore 标记位被用来扩展协议。当标记位为 0 时表示协议不能忽略当前 frame。而当该标记位没有设置时，协议的实现**可能**在无法理解接受到的 Frame 时选择发送回一个 ERROR[CONNECTION_ERROR] 的 frame 并随后关闭底层的 transport 连接。

#### Frame 校验

RSocket 实现可能会在元数据层面为特定的 frame 提供自己的校验逻辑。但是，这个是应用应该关注的内容，而不是协议处理必须要做的。

#### 可选的元数据头

特定的 Frame 类型**可能**会包含元数据。如果该种 Frame 类型同时支持数据(Data)和元数据(Metadata)，那么**必须**提供一个可选的元数据头。这个元数据头位于 Frame 头和 payload 之间。

元数据长度**必须**等于 Frame 长度减去 Frame 头的长度和 Frame Payload 的长度(如果有的话)。如果元数据长度不等于这个值的时候，这个 frame 就是非法的，接收方**必须**发送一个 ERROR[CONNECTION_ERROR] 的 frame 回应并关闭底层的 transport 连接，除非该 frame 的 IGNORE 标记位被设置。

一个包含数据和元数据的 frame：

```
     0                   1                   2                   3
     0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
    +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
    |              Metadata Length                  |
    +---------------------------------------------------------------+
    |                       Metadata Payload                       ...
    +---------------------------------------------------------------+
    |                       Payload of Frame                       ...
    +---------------------------------------------------------------+
```

一个容许包含数据和元数据的 frame，但是数据长度为 0：

```
     0                   1                   2                   3
     0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
    +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
    |              Metadata Length                  |
    +---------------------------------------------------------------+
    |                       Metadata Payload                       ...
    +---------------------------------------------------------------+
```

如果一个 frame 只包含元数据，那么元数据长度的字段可以不提供：

```
     0                   1                   2                   3
     0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
    +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
    |                       Metadata Payload                       ...
    +---------------------------------------------------------------+
```

* __元数据长度__: (24 位 = 最大值 16,777,215) 无符号 24-bit 整数，代表元数据的字节长度。排除了元数据长度字段本身的长度。

<a name="stream-identifiers"></a>

### Stream 标识

#### 生成

Stream ID 由请求方生成。Stream ID 的生命周期由请求类型和该类型上的 stream 语义来决定。

Stream ID 0 为保留值，用于任何涉及连接的操作。

一个 Stream ID 在一条连接上对于请求方来说**必须**是唯一的。

Stream ID 的生成遵循 [HTTP/2](https://tools.ietf.org/html/rfc7540) 中的做法，也就是，客户端**必须**用奇数作为 Stream ID，服务端**必须**使用偶数。

客户端的 Stream ID **必须**从 1 开始，然后以 2 为步长递增，比如：1，3，5，7 等等。

服务端的 Stream ID **必须**从 2 开始，然后以 2 为步长递增，比如：2，4，6，8 等等。

#### 生命周期

一旦最大的 Stream ID (2^31-1) 被使用，请求方**可能**会重用 Stream ID。回应方**必须**假设 Stream ID 是可重用的。

在最大的 Stream ID 被使用之后：

1) 如果 Stream ID 重用没有激活：

* 没有新的 stream 可以被创建，因此，在达到最大 ID 之后**必须**创建一条新的连接以便在其上可以重新创建新的 stream。

2) 如果 Stream ID 可以重用：

* 请求方**必须**重用 ID，客户端从 1 开始，服务端从 2 开始，并以步长 2 递增。
* 请求方**必须**跳过还在使用的 ID。
* 如果回应方认为某个 ID 仍然在使用，**可能**选择回应 ERROR[REJECT]。然后请求方**可能**会用序列中的下一个它认为未被使用的 ID 重试。
* 如果所有的 Stream ID 都正在使用，那么无法生成新的 stream。在这种情况下，**必须**建立一条新的连接以便在其上继续生成新的 stream。

**建议**当且仅当恢复特性(resumability)存在时才使用 Stream ID 的重用特性。 

### Frame 类型

| 类型                                       | 值    | 描述                                       |
| :--------------------------------------- | :--- | :--------------------------------------- |
| __RESERVED__                             | 0x00 | __Reserved__                             |
| [__SETUP__](#frame-setup)                | 0x01 | __Setup__: 由客户端发起一次协议的处理                 |
| [__LEASE__](#frame-lease)                | 0x02 | __Lease__: 有回应端发送，授予对端发送请求的权利            |
| [__KEEPALIVE__](#frame-keepalive)        | 0x03 | __Keepalive__: 连接保活                      |
| [__REQUEST_RESPONSE__](#frame-request-response) | 0x04 | __Request Response__: 请求一次回应             |
| [__REQUEST_FNF__](#frame-fnf)            | 0x05 | __Fire And Forget__: 一次单向消息              |
| [__REQUEST_STREAM__](#frame-request-stream) | 0x06 | __Request Stream__: 请求一个有限的 (completable) stream |
| [__REQUEST_CHANNEL__](#frame-request-channel) | 0x07 | __Request Channel__: 请求一个有限的 (completable) 双向 stream |
| [__REQUEST_N__](#frame-request-n)        | 0x08 | __Request N__: Reactive Streams 语义下的再请求 N 条 |
| [__CANCEL__](#frame-cancel)              | 0x09 | __Cancel Request__: 取消请求                 |
| [__PAYLOAD__](#frame-payload)            | 0x0A | __Payload__: Stream 上的 Payload。例如：对应一个 request 的 response，或者 channel 中的消息 |
| [__ERROR__](#frame-error)                | 0x0B | __Error__: 连接级别或者应用级别的错误                 |
| [__METADATA_PUSH__](#frame-metadata-push) | 0x0C | __Metadata__: 异步元数据 frame                |
| [__RESUME__](#frame-resume)              | 0x0D | __Resume__: 替代 SETUP，恢复操作（可选）            |
| [__RESUME_OK__](#frame-resume-ok)        | 0x0E | __Resume OK__ : RESUME 的回应，当恢复操作可行的情况下发送 （可选） |
| [__EXT__](#frame-ext)                    | 0x3F | __Extension Header__: 用来扩展更多的 frame 类型以及更多的扩展 |

<a name="frame-setup"></a>

#### SETUP Frame (0x01)

Setup frames **必须**始终使用 Stream ID 0，因为它们与连接相关。

客户端通过发送 SETUP frame 告知服务端它想以什么样的参数来操作。用法以及其所使用的消息序列可以参考[连接建立](#connection-establishment)。

连接上一个重要的参数和格式、布局、数据的 schema、以及 frame 的元信息有关。因为找不到更好的词来描述，可以借用一下 "MIME 类型" 来类比。实现**可能**借用典型的 MIME类型，也有**可能**使用自定义的类型来表示格式、布局、以及数据和元数据的 schema。协议的实现**一定不能**解析 MIME 类型，这个是应用才需要考虑的。

数据的编码格式和元数据的编码格式在 SETUP 中是分别存放的。

Frame 内容

```
     0                   1                   2                   3
     0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
    +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
    |                         Stream ID = 0                         |
    +-----------+-+-+-+-+-----------+-------------------------------+
    |Frame Type |0|M|R|L|  Flags    |
    +-----------+-+-+-+-+-----------+-------------------------------+
    |         Major Version         |        Minor Version          |
    +-------------------------------+-------------------------------+
    |0|                 Time Between KEEPALIVE Frames               |
    +---------------------------------------------------------------+
    |0|                       Max Lifetime                          |
    +---------------------------------------------------------------+
    |         Token Length          | Resume Identification Token  ...
    +---------------+-----------------------------------------------+
    |  MIME Length  |   Metadata Encoding MIME Type                ...
    +---------------+-----------------------------------------------+
    |  MIME Length  |     Data Encoding MIME Type                  ...
    +---------------+-----------------------------------------------+
                          Metadata & Setup Payload
```

* __Frame Type__: (6 bits) 0x01
* __Flags__: (10 bits)
  * (__M__)etadata: 是否存在元数据
  * (__R__)esume Enable: 客户端请求可恢复。恢复身份标识存在
  * (__L__)ease: 是否遵从 LEASE
* __Major Version__: (16 位 = 最大值 65,535) 16位无符号整数，代表协议的主版本
* __Minor Version__: (16 位 = 最大值 65,535) 16位无符号整数，代表协议的辅版本
* __Time Between KEEPALIVE Frames__: (31 位 = 最大值 2^31-1 = 2,147,483,647) 31 位无符号整数，代表客户端发送两个 KEEPALIVE frame 之间的时间间隔（以毫秒为单位）。值**必须** > 0
  * 对于服务器-服务器之间的连接，一个合理的时间间隔是 500ms
  * 对于移动端-服务器之间的连接，时间间隔往往应该 > 30,000ms
* __Max Lifetime__: (31 位 = 最大值 2^31-1 = 2,147,483,647) 31位无符号整数，代表客户端发送 KEEPALIVE 后等待服务器回应的最长等待时间（单位毫秒），超出等待时间可以认为服务器挂了。该值**必须** > 0 
* __Resume Identification Token Length__: (16 位 = 最大值 65,535) 16位无符号整数，代表恢复身份标识的字节长度。（如果 R 标记没有设置，该字段也不会存在）
* __Resume Identification Token__: 客户端用来恢复身份的标识（如果 R 标记没有设置，该字段也不会存在）
* __MIME Length__: MIME 类型的字节长度
* __Encoding MIME Type__: 用来编码数据和元数据的 MIME 类型。它**应该**是一个包含在 [Internet media type](https://en.wikipedia.org/wiki/Internet_media_type) 中的符合 [RFC 2045](https://tools.ietf.org/html/rfc2045) 规范的 US-ASCII 字符串。其中许多类型在 [IANA](https://www.iana.org/assignments/media-types/media-types.xhtml) 注册，比如 [CBOR](https://www.iana.org/assignments/media-types/application/cbor)。 [Suffix](http://www.iana.org/assignments/media-type-structured-suffix/media-type-structured-suffix.xml) 规则**可能**被用来处理布局。例如，`application/x.netflix+cbor`、
  `application/x.reactivesocket+cbor` 或 `application/x.netflix+json`。这个字符串**必须不能**以 null 作为结束符。
* __Setup Data__: 包含描述Setup 头的发送方的连接能力的 payload。

__注意__: 如果服务器接受到了一个设置了 (__R__)esume Enabled 的 SETUP frame，但是不支持恢复操作，**必须**拒绝该 SETUP 请求并以 ERROR[REJECTED_SETUP] 回应。

<a name="frame-error"></a>

#### ERROR Frame (0x0B)

当某个 request/stream 发生错误时，或者连接发生错误，或者回应 SETUP frame 时，都可以使用 Error frame。

Frame 内容

```
     0                   1                   2                   3
     0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
    +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
    |                           Stream ID                           |
    +-----------+-+-+---------------+-------------------------------+
    |Frame Type |0|0|      Flags    |
    +-----------+-+-+---------------+-------------------------------+
    |                          Error Code                           |
    +---------------------------------------------------------------+
                               Error Data
```

* __Frame Type__: (6 位) 0x0B
* __Error Code__: (32 位 = 最大值 2^31-1 = 2,147,483,647) 错误类型。
  * 参见下面合法的错误码。
* __Error Data__: 包含描述错误信息的 Payload。Error Data **应该**是一个 UTF-8 编码的字符串，并且不能以 null 结尾

Stream ID 为 0 表示错误与连接有关，包括连接的建立。Stream ID > 0 表示错误与某个特定的 stream 相关。

Error Data 通常是 Exception 消息，但是也可以包含 stacktrace 信息，如果合适的话。

##### 错误码

| 类型                    | 值          | 描述                                       |
| :-------------------- | :--------- | :--------------------------------------- |
| __RESERVED__          | 0x00000000 | __Reserved__                             |
| __INVALID_SETUP__     | 0x00000001 | 服务器认为 Setup frame 非法(可能是因为客户端太新而服务器太老). Stream ID **必须**为 0 |
| __UNSUPPORTED_SETUP__ | 0x00000002 | 服务器不支持部分（或者全部）客户端指定的参数。Stream ID **必须**为 0 |
| __REJECTED_SETUP__    | 0x00000003 | 服务器拒绝 setup，并可以在 payload 中包含拒绝的理由。Stream ID **必须**为 0 |
| __REJECTED_RESUME__   | 0x00000004 | 服务器拒绝 resume，并可以在 payload 中包含拒绝的理由。Stream ID **必须**为 0 |
| __CONNECTION_ERROR__  | 0x00000101 | 连接即将被终止。Stream ID **必须**为 0。该 frame 的发送方和接受方**可能**会立即关闭连接，而不会等待其中的 stream 结束。 |
| __CONNECTION_CLOSE__  | 0x00000102 | 连接即将被终止。Stream ID **必须**为 0。该 frame 的发送方和接受方**必须**等待连接上的 stream 处理完毕后才能关闭该连接。新的请求**可能**不被接受。 |
| __APPLICATION_ERROR__ | 0x00000201 | 应用层逻辑，产生 Reactive Streams 中的 _onError_ 事件。Stream ID **必须** > 0 |
| __REJECTED__          | 0x00000202 | 回应方选择拒绝请求，即使请求合法。回应方需要确保这个请求没有被处理过。拒绝的理由在 Error Data 章节详细阐述。Stream ID **必须** > 0 |
| __CANCELED__          | 0x00000203 | 回应方取消请求，可能已经开始处理该请求（与 REJECTED 类似，但是不保证没有副作用）。Stream ID **必须** > 0 |
| __INVALID__           | 0x00000204 | 非法请求。Stream ID **必须** > 0                |
| __RESERVED__          | 0xFFFFFFFF | __保留，扩展字段__                              |

__注意__: 0x0001 - 0x00300 之间还未使用的值作为协议未来的扩展保留。0x00301 - 0xFFFFFFFE 预留，用作应用层的错误。

在本文中，当提及某个特定错误码的帧的表示时，使用这种形式来表达：ERROR[error_code] 或 ERROR[error_code|error_code]

例如:

* ERROR[INVALID_SETUP] 表示 INVALID_SETUP 的 ERROR frame
* ERROR[REJECTED] 代表 REJECTED 的 ERROR frame
* ERROR[CONNECTION_ERROR|REJECTED_RESUME] 代表 CONNECTION_ERROR 或者 REJECTED_RESUME 的 ERROR frame

<a name="frame-lease"></a>

#### LEASE Frame (0x02)

Lease frame **可能**由来自客户端或者服务器端的回应方发送，用来通知请求方可以发送请求的时长，以及在这段时间窗口之内可以发送的数量。详见 [租约场景](#lease-semantics)。

最近收到的 LEASE frame 覆盖以往所有的 LEASE frame。

Lease frame **必须**使用 Stream ID 0，因为其与连接有关。

Frame 内容

```
     0                   1                   2                   3
     0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
    +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
    |                         Stream ID = 0                         |
    +-----------+-+-+---------------+-------------------------------+
    |Frame Type |0|M|     Flags     |
    +-----------+-+-+---------------+-------------------------------+
    |0|                       Time-To-Live                          |
    +---------------------------------------------------------------+
    |0|                     Number of Requests                      |
    +---------------------------------------------------------------+
                                Metadata
```

* __Frame Type__: (6 位) 0x02 
* __Flags__: (10 位)
  * (__M__)etadata: 存在元数据
* __Time-To-Live (TTL)__: (31 位 = 最大值 2^31-1 = 2,147,483,647) 31位无符号整数，表示自收到 LEASE 起有效的租约时间（单位毫秒）。值**必须** > 0
* __Number of Requests__: (31 位 = 最大值 2^31-1 = 2,147,483,647) 31位无符号整数，表示在收到下一个 LEASE 之前可以发送的请求数。值**必须** > 0

回应方的实现**可能**会通过发送一个 __Number of Requests__ 或者 __Time-To-Live__ 为 0 的 LEASE 来终止后续的请求。

当一个 LEASE 到期，请求方可以继续发送请求数为 0。

该 frame 只支持包含元信息，所以元信息的长度**必须不能**包括，即使是  (M)etadata 标志位被设置为 true。

<a name="frame-keepalive"></a>

#### KEEPALIVE Frame (0x03)

KEEPALIVE frame **必须**永远使用 Stream ID 0，因为其与连接相关。

KEEPALIVE frame **必须**由客户端发起，设置 (__R__)espond 标志位，并周期性的发送。

KEEPALIVE frame **可能**由服务器端发起，设置 (__R__)espond 标志位，并在收到应用请求后发送。

在接收到  (__R__)espond 标志位被设置的 KEEPALIVE frame 之后，客户端或者服务器端**必须**回应一个  (__R__)espond flag **没有**设置的 KEEPALIVE。接受的 KEEPALIVE 中的数据**必须**与生成的 KEEPALIVE 一致。

服务器端收到 KEEPALIVE 表明客户端存活。

客户端收到 KEEPALIVE 说明服务器端存活。

Frame 内容

```
     0                   1                   2                   3
     0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
    +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
    |                         Stream ID = 0                         |
    +-----------+-+-+-+-------------+-------------------------------+
    |Frame Type |0|0|R|    Flags    |
    +-----------+-+-+-+-------------+-------------------------------+
    |0|                  Last Received Position                     |
    +                                                               +
    |                                                               |
    +---------------------------------------------------------------+
                                  Data
```

* __Frame Type__: (6 位) 0x03
* __Flags__: (10 位)
  * (__R__)espond：是否回应 KEEPALIVE
* __Last Received Position__: (63 位 = 最大值 2^63-1) 63 位无符号长整形，表示恢复到上次接收的位置。值**必须** > 0。（可选。如不支持，全部置空）
* __Data__: 关联在 KEEPALIVE 上的数据

<a name="frame-request-response"></a>

#### REQUEST_RESPONSE Frame (0x04)

Frame 内容

```
     0                   1                   2                   3
     0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
    +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
    |                           Stream ID                           |
    +-----------+-+-+-+-------------+-------------------------------+
    |Frame Type |0|M|F|     Flags   |
    +-------------------------------+
                         Metadata & Request Data
```

* __Frame Type__: (6 位) 0x04
* __Flags__: (10 位)
  * (__M__)etadata: 存在元数据
  * (__F__)ollows: 后续还有更多分片 (fragment)
* __Request Data__: 被请求的服务标识，以及请求参数。

<a name="frame-fnf"></a>

#### REQUEST_FNF (Fire-n-Forget) Frame (0x05)

Frame 内容

```
     0                   1                   2                   3
     0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
    +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
    |                           Stream ID                           |
    +-----------+-+-+-+-------------+-------------------------------+
    |Frame Type |0|M|F|    Flags    |
    +-------------------------------+
                          Metadata & Request Data
```

* __Frame Type__: (6 位) 0x05
* __Flags__: (10 位)
  * (__M__)etadata: 存在元数据
  * (__F__)ollows: 后续还有更多分片 (fragment)
* __Request Data__: 被请求的服务标识，以及请求参数。

<a name="frame-request-stream"></a>

#### REQUEST_STREAM Frame (0x06)

Frame 内容

```
     0                   1                   2                   3
     0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
    +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
    |                           Stream ID                           |
    +-----------+-+-+-+-------------+-------------------------------+
    |Frame Type |0|M|F|    Flags    |
    +-------------------------------+-------------------------------+
    |0|                    Initial Request N                        |
    +---------------------------------------------------------------+
                          Metadata & Request Data
```

* __Frame Type__: (6 位) 0x06
* __Flags__: (10 位)
  * (__M__)etadata: 存在元数据
  * (__F__)ollows: 后续还有更多分片 (fragment)
* __Initial Request N__: (31 位 = 最大值 2^31-1 = 2,147,483,647) 31位无符号整数，代表初始的请求条目。值**必须** > 0
* __Request Data__: 被请求的服务标识，以及请求参数。

参阅  [流量控制：Reactive Streams 语义](#flow-control-reactive-streams) 了解有关 RequestN 行为的更多信息。

<a name="frame-request-channel"></a>

#### REQUEST_CHANNEL Frame (0x07)

Frame 内容

```
     0                   1                   2                   3
     0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
    +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
    |                           Stream ID                           |
    +-----------+-+-+-+-+-----------+-------------------------------+
    |Frame Type |0|M|F|C|  Flags    |
    +-------------------------------+-------------------------------+
    |0|                    Initial Request N                        |
    +---------------------------------------------------------------+
                           Metadata & Request Data
```

* __Frame Type__: (6 位) 0x07
* __Flags__: (10 位)
  * (__M__)etadata: 存在元数据
  * (__F__)ollows: 后续还有更多分片 (fragment)
  * (__C__)omplete: 代表 stream 结束的标志位
    * 如果设置，Subscriber/Observer 上的`onComplete()`或者对等的方法会被调用。
* __Initial Request N__: (31 位 = 最大值 2^31-1 = 2,147,483,647) 31位无符号整数，代表 channel 上初始的 request N 的值。值**必须** > 0。
* __Request Data__: 被请求的服务标识，以及请求参数。

A requester MUST send only __one__ REQUEST_CHANNEL frame. Subsequent messages from requester to responder MUST be sent as PAYLOAD frames. 

请求方**必须**只发送**一个** REQUEST_CHANNEL frame。请求方后续发给回应方的消息**必须**是 PAYLOAD frame。

请求方在发送 REQUEST_CHANNEL frame 之后，**不能**继续发送 PAYLOAD frame，直到响应方回应 REQUEST_N frame 授信指定数目的 PAYLOAD 可以继续发送为止。

参阅  [流量控制：Reactive Streams 语义](#flow-control-reactive-streams) 了解有关 RequestN 行为的更多信息。

<a name="frame-request-n"></a>

#### REQUEST_N Frame (0x08)

Frame 内容

```
     0                   1                   2                   3
     0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
    +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
    |                           Stream ID                           |
    +-----------+-+-+---------------+-------------------------------+
    |Frame Type |0|0|     Flags     |
    +-------------------------------+-------------------------------+
    |0|                         Request N                           |
    +---------------------------------------------------------------+
```

* __Frame Type__: (6 位) 0x08
* __Request N__: (31 位 = 最大值 2^31-1 = 2,147,483,647) 31位无符号整数，代表请求回应的条目。值**必须**  > 0。

参阅  [流量控制：Reactive Streams 语义](#flow-control-reactive-streams) 了解有关 RequestN 行为的更多信息。

<a name="frame-cancel"></a>

#### CANCEL Frame (0x09)

Frame 内容

```
     0                   1                   2                   3
     0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
    +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
    |                           Stream ID                           |
    +-----------+-+-+---------------+-------------------------------+
    |Frame Type |0|0|    Flags      |
    +-------------------------------+-------------------------------+
```

* __Frame Type__: (6 位) 0x09

<a name="frame-payload"></a>

#### PAYLOAD Frame (0x0A)

Frame 内容

```
     0                   1                   2                   3
     0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
    +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
    |                           Stream ID                           |
    +-----------+-+-+-+-+-+---------+-------------------------------+
    |Frame Type |0|M|F|C|N|  Flags  |
    +-------------------------------+-------------------------------+
                         Metadata & Data
```

* __Frame Type__: (6 位) 0x0A
* __Flags__: (10 位)
  * (__M__)etadata: 存在元信息
  * (__F__)ollows: 后续还有更多分片 (fragment)
  * (__C__)omplete: 代表 stream 结束的标志位
    * 如果设置，Subscriber/Observer 上的`onComplete()`或者对等的方法会被调用。
  * (__N__)ext: 代表 Next 的标志位（还存在 Payload 数据，或者元数据，或者二者皆有）
    * 如果设置，Subscriber/Observer 上的 `onNext(Payload)` 或者对等的方法会被调用。
* __Payload Data__: Reactive Streams onNext 的 payload 数据。

 (C)omplete 和 (N)ext 合法的组合包括:

* (C)omplete 和 (N)ext 同时设置表示 PAYLOAD 既包含数据也代表 stream 的结束。
  * 例如：一个 Observable stream 接收了 `onNext(payload)` 然后紧接着又接收了 `onComplete()`。
* 只有  (C)omplete  设置，表示 PAYLOAD 不包含数据，仅仅表示 stream 结束。
  * 例如：一个 Observable stream 接收了 `onComplete()`。
* 只有 (N)ext 设置，表示 PAYLOAD 包含数据但是 stream **没有**结束。
  * 例如：一个 Observable stream 接收了`onNext(payload)`。

一个 PAYLOAD **不应该**存在 (C)complete 和  (N)ext 同时为空 (false) 的情况。

引入 (N)ext 标记位，而不是沿用数据长度(> 0)的理由是，长度为 0 的数据是合法的 PAYLOAD，递交给应用层的 PAYLOAD 中包含 0 字节的数据。

例如：一个 Observable stream 通过  `onNext(payload)` 接收到了 0 字节长度的数据。

<a name="frame-metadata-push"></a>

#### METADATA_PUSH Frame (0x0C)

请求方或者回应方可以使用 Metadata Push frame 来异步发送元信息通知给它的对端。

METADATA_PUSH frames **必须**始终使用 Stream ID 0，因为与连接相关。

捆绑在某个 stream 上的元信息使用的是自有的 Payload frame 元信息标识。

Frame 内容

```
     0                   1                   2                   3
     0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
    +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
    |                         Stream ID = 0                         |
    +-----------+-+-+---------------+-------------------------------+
    |Frame Type |0|1|     Flags     |
    +-------------------------------+-------------------------------+
                                Metadata
```

* __Frame Type__: (6 位) 0x0C

这个 frame 仅支持元数据，因此**不能**包含元数据长度头。

<a name="frame-ext"></a>

#### EXT (Extension) Frame (0x3F)

扩展 frame 的通用格式如下所示。

```
     0                   1                   2                   3
     0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
    +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
    |                           Stream ID                           |
    +-----------+-+-+---------------+-------------------------------+
    |Frame Type |I|M|    Flags      |
    +-------------------------------+-------------------------------+
    |0|                      Extended Type                          |
    +---------------------------------------------------------------+
                          Depends on Extended Type...
```

* __Frame Type__: (6 位) 0x3F
* __Flags__: (10 位)
  * (__I__)gnore: 如果不能解析 frame 是否可以忽略？
  * (__M__)etadata: 存在元信息
* __Extended Type__: (31 位 = 最大值 2^31-1 = 2,147,483,647) 31位无符号整数，表示扩展类型的信息。值**必须** > 0。

## 恢复操作

由于在 RSocket 中有大量活跃请求存在的事实，在传输失败时提供可恢复的能力是很有必要的。当然，支持与否完全取决于协议的实现。

### 假设

RSocket 恢复特性仅适用于特定的场景，并不是一个普适 ("always works") 的方案。当恢复操作不可行的情况下，连接应当被终止，并按照协议规定以一个 ERROR 回应对端。

1. 恢复是一个可选实现，但是强烈建议实现。客户端和服务器端应当假设协议**没有**恢复的能力。
2. 恢复是一个优化操作。不能保证该操作永远能成功。
3. 恢复操作应当是快速的，只需要最小集合的状态交换。
4. 恢复是针对失联场景设计的特性，并假设客户端状态和服务器状态在连接断开的情况下也会维持，也就是，假定双方的状态不会丢失。这是一个非常重要的假设，否则，就需要依赖可靠消息传递 ("guarenteed messaging") 的存在。
5. 恢复假设 Lease，数据格式(编码) 等在操作过程中不会变化。例如：在恢复操作的过程中，客户端不容许改变元信息的 MIME 类型或者数据的 MIME 类型或者版本等信息。
6. 恢复操作永远由客户端发起，服务端可以选择接受或者拒绝。
7. 恢复操作对于发送 frame 的应用状态不做原子性、事务性方面的假设。参见上文。

### 隐式位置

恢复操作需要知道上一次连接上数据接收到了哪个地方。处于简化的目的，假定底层的传输层支持帧级别上的连续传输 (contiguous delivery)。换句话说，不容许发送帧的一部分，帧与帧之间不能有空隙。TCP、WebSocket、和 Aeron 都是满足要求的传输层协议。特别的，在 TCP 上需要额外的工作就可以满足要求。

当请求方或者回应方**发送** REQUEST、CANCEL、或者 PAYLOAD frame 时，会维护一个这个 frame 在连接上发送方向的**位置**。这是一个从 0 开始计数的 64位整数。当请求方或者回应方**接收到** REQUEST、CANCEL、或者 PAYLOAD frame 时，会维护一个这个 frame 在连接上接收方向的**隐式位置**。这也是一个从 0 开始计数的 64位整数。

之所以称之为 "隐式" 的原因是每个 frame 中并不包含位置信息，位置是根据连接上发送/接收消息相互关系推断出来的。

该位置用来定位恢复操作开始的位置。

除 REQUEST、CANCEL、ERROR、和 PAYLOAD，其他 frame 没有指派的（或者隐式的）位置。

当客户端发送一个 RESUME frame 时，会发送两个隐式位置：最近一个接受到的 frame；仍然保存的最早的 frame 位置。然后服务器端根据该信息决定恢复是否可能：客户端最近接收的 frame 之后的所有 frame 在服务器端是否还存在？服务器端保存的位置之后的 frame 在客户端是否还保留。如果恢复是可行的，服务器端会回应一个 RESUME_OK，其中标明它上次接收到的位置。

### 客户端生命管理

服务器对客户端的生命管理**必须**客户端为了成功恢复重建连接的时间（应该翻译的不对…）。客户端生命管理的手段完全由实现决定。

### 恢复操作

所有 ERROR frame 必须发送 CONNECTION_ERROR 或者 REJECTED_RESUME 的错误码。

客户端恢复操作发生的时机是，客户端全新建立一条连接并试图恢复。操作如下:

* 客户端发送 RESUME frame。其后客户端**禁止**发送其它类型的 frame，直到恢复完成。RESUME 中的身份标识**必须**与原始的 SETUP frame 中的标识一致。RESUME 中最近接收位置的字段**必须**是服务器最新成功接收的隐性位置。
* 客户端等待来自服务器端的回应，可能是 RESUME_OK 或者 ERROR[CONNECTION_ERROR|REJECTED_RESUME] frame。
* 接收到 ERROR[REJECTED_RESUME] frame 之后，客户端**严禁**重复发起恢复请求。
* 接收到 RESUME_OK，客户端：
  * **必须**假定后续的 REQUEST、CANCEL、ERROR、PAYLOAD frames 的隐性位置从上一次隐性位置开始
  * **可能**根据服务端回应的 RESUME_OK 中的最新接收位置开始重新传输*所有的* REQUEST、CANCEL、ERROR、和 PAYLOAD frames
  * **可能**发送一个 ERROR[CONNECTION_ERROR|CONNECTION_CLOSE] frame 表明连接结束，之后**严禁**再次发起恢复

服务器端在接收到客户端发送的 RESUME frame 后开始恢复操作。操作如下：

* 接收到 RESUME frame 后，服务器：
  * 如果不支持恢复操作，**必须**发送一个 ERROR[REJECTED_RESUME] frame
  * 通过 RESUME 中的身份标识字段决定为哪一个客户端恢复。正确识别客户端身份后，**可以**继续恢复。如果不能识别，那么服务器**必须**发送 ERROR[REJECTED_RESUME] frame 回应。
  * if successfully identified, then the server MAY send a RESUME_OK and then:
  * 如果正确识别，服务器**可以**发送 RESUME_OK 回应，并：
    * **必须**假定后续的 REQUEST、CANCEL、ERROR、PAYLOAD frames 的隐性位置从上一次隐性位置开始
    * **可能**根据客户端发送的 RESUME 中的最新接收位置开始重新传输*所有的* REQUEST、CANCEL、ERROR、和 PAYLOAD frames
  * 正确识别后，如果服务器无法（无法恢复的位置，或者其他原因）从 RESUME 中的最近接收位置字段处恢复，服务器**也可能**发送一个 ERROR[REJECTED_RESUME] frame。

服务器端在接收到 SETUP frame 之后又接收到了一个 RESUME frame，**应当**发送 ERROR[CONNECTION_ERROR] 作为回应。

服务器端在接收到 RESUME frame 之后又接收到了 RESUME frame，**应当**发送 ERROR[CONNECTION_ERROR] 作为回应。

在恢复的场景中**不应当**遵从上条连接上的租约语义，**必须**遵从服务器端在新的连接上发送过来的新的租约语义。

<a name="frame-resume"></a>

#### RESUME Frame (0x0D)

Resume frame 的格式如下。

RESUME frames **必须**始终使用 Stream ID 0，因为与连接相关。

```
     0                   1                   2                   3
     0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
    +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
    |                         Stream ID = 0                         |
    +-----------+-+-+---------------+-------------------------------+
    |Frame Type |0|0|    Flags      |
    +-------------------------------+-------------------------------+
    |        Major Version          |         Minor Version         |
    +-------------------------------+-------------------------------+
    |         Token Length          | Resume Identification Token  ...
    +---------------------------------------------------------------+
    |0|                                                             |
    +                 Last Received Server Position                 +
    |                                                               |
    +---------------------------------------------------------------+
    |0|                                                             |
    +                First Available Client Position                +
    |                                                               |
    +---------------------------------------------------------------+
```

* __Frame Type__: (6 位) 0x0D
* __Major Version__: (16 位 = 最大值 65,535) 16位无符号整数，表示协议的主版本。
* __Minor Version__: (16 位 = 最大值 65,535) 16位无符号整数，表示协议的辅版本。
* __Resume Identification Token Length__: (16 位 = 最大值 65,535) 16位无符号整数，恢复身份标识的字节长度。
* __Resume Identification Token__: 用来恢复身份的标识。与 SETUP 中的身份标识相同。
* __Last Received Server Position__: (63 位 = 最大值 2^63-1) 63位无符号长整形，表示客户端最近接收到的隐形位置。
* __First Available Client Position__: (63 位 = 最大值 2^63-1) 63位无符号长整形，表示客户端可以重放 frame 的最早位置

<a name="frame-resume-ok"></a>

#### RESUME_OK Frame (0x0E)

Resume OK frame 格式如下。

RESUME OK frames **必须**始终使用 Stream ID 0，因为与连接相关。

```
     0                   1                   2                   3
     0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
    +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
    |                         Stream ID = 0                         |
    +-----------+-+-+---------------+-------------------------------+
    |Frame Type |0|0|    Flags      |
    +-------------------------------+-------------------------------+
    |0|                                                             |
    +               Last Received Client Position                   +
    |                                                               |
    +---------------------------------------------------------------+
```

* __Frame Type__: (6 位) 0x0E
* __Last Received Client Position__: (63 位 = 最大值 2^63-1) 63位无符号长整型，表示服务端接收客户端数据最新的隐式位置

#### Keepalive 位置字段

Keepalive frame 包含客户端（或者服务器端）的隐式位置。目的是让对端知晓自己的位置。

这个信息**可能**被用在重新传输的场景里更新状态，比如：缩短用作重新传输的缓冲区大小，或者 stream 状态之间可能的关联 。

### 身份标识的处理

对于恢复身份标识的具体要求由协议的实现决定。但是，以下是实现上的一些建议和指南：

* 标识可以由客户端产生
* 标识也可以由客户端和服务器端之外的系统产生，并独立管理
* 标识用来唯一标识在服务器上一条连接。服务器不应该对标识生成的方法做任何假设，并应该认为标识是不透明的。这样做确保了客户端可以和任何支持恢复操作的 RSocket 实现相兼容，并容许客户端对身份标识的生成完全控制
* 在每一个 RSocket 的生命周期中（包含可能出现的恢复阶段），标识**必须**保持有效
* 服务器不应该接受一个包含已经在使用的标识的 SETUP 请求
* 标识应当可以防御重放攻击，因此，标识的有效性只应该与特定连接的生命周期相关
* 标识不应该被第三方的攻击软件猜测到

## 连接建立

__注意__: 语义与 [TLS False Start](https://tools.ietf.org/html/draft-bmoeller-tls-falsestart-00) 类似

成功建立连接之后，客户端**必须**发送包含 Stream ID 为 0 的 SETUP 或者 RESUME frame。如果接收到了不是 SETUP|RESUME 的 frame 或者 SETUP|RESUME frame 中的 Stream ID > 0，服务器**必须**发送  ERROR[INVALID_SETUP] 作为回应，并关闭连接。

关于恢复更多的信息参阅[恢复操作](#resume-operation)。本章节后续内容假设使用 SETUP 建立一个连接。

客户端的请求方可以知会服务端的回应方它是否根据 SETUP frame 中 __L__ 标识的存在来遵循 LEASE。

客户端的请求方，如果**没有**在 SETUP frame 中设置 __L__ 标识的话，可能不会等待服务器端的 LEASE 回应，而立即发送请求。

客户端的请求方，如果在 SETUP frame 中设置 __L__ 标识的话，**必须**等待服务器端的回应方回应一个 LEASE frame 之后才能发请求。

如果服务器接受了 SETUP frame 的内容，并且其中设置了 __L__ 标识的话，它**必须**发送一个 LEASE frame 回应。服务器端的请求方可能在收到 SETUP frame 时立即发送请求，如果它容许 __L__ 标识可以不在 SETUP frame 中设置的话。

如果服务器**不接受** SETUP frame 中的内容，服务器**必须**发送 ERROR[INVALID_SETUP|UNSUPPORTED_SETUP] 作为回应，并关闭连接。

服务器端的请求方镜像了客户端的请求方的 LEASE 请求。如果服务器端的请求方在 SETUP frame 里设置了 __L__ 标志，那么服务器端的请求方**必须**等待客户端的回应方发送的 LEASE frame 后才能发送请求。客户端的回应方**必须**在接收到一个 __L__ 标志被设置的 SETUP frame 之后回应一个 LEASE frame。 

当客户端接收到了一个请求对应的回应，一个 LEASE frame，或者看到了一个 REQUEST 类型，就认为 SETUP 已被接受。

当客户端接收到一个 ERROR 就认为 SETUP 被拒绝。

在连接建立完成之前，请求方**禁止**发送任何请求 frame。

在连接建立完成之前，回应方**禁止**回应任何 PAYLOAD frame。

### 协商

协议假定客户端会支配服务器要做什么。服务器决定是（接受）否（拒绝）支持发送过来的 SETUP frame。ERROR[INVALID_SETUP|UNSUPPORTED_SETUP|REJECTED_SETUP] 用来表明拒绝的原因。

### 不带 LEASE 的序列

不带 LEASE 的序列如下所示：

1. 客户端请求，服务器端**接受** SETUP
   * 客户端建连 & 发送 SETUP & 发送 REQUEST
   * 服务器接受 SETUP，处理 REQUEST，根据 REQUEST 类型做正常的回应
2. 客户端请求，服务器端**拒绝** SETUP
   * 客户端建连 & 发送 SETUP & 发送 REQUEST
   * 服务器拒绝 SETUP，回应 ERROR[INVALID_SETUP|UNSUPPORTED_SETUP|REJECTED_SETUP]，关闭连接
3. 服务器端请求，服务器端**接受** SETUP
   * 客户端建连 & 发送 SETUP
   * 服务器接受 SETUP，回应 REQUEST 类型
4. 服务器端请求，服务器端**拒绝** SETUP
   * 客户端建连 & 发送 SETUP
   * 服务器拒绝 SETUP，回应 ERROR[INVALID_SETUP|UNSUPPORTED_SETUP|REJECTED_SETUP]，关闭连接

### 带 LEASE 的序列

带 LEASE 的序列如下所示：

1. 客户端请求，服务器端**接受** SETUP
   * 客户端建连 & 发送带有__L__标识的  SETUP
   * 服务器接受 SETUP，回应 LEASE frame
   * 客户端发送 REQUEST
2. 客户端请求，服务器端**拒绝** SETUP
   * 客户端建连 & 发送带有__L__标识的  SETUP
   * 服务器拒绝 SETUP，回应 ERROR[INVALID_SETUP|UNSUPPORTED_SETUP|REJECTED_SETUP]，关闭连接
3. 服务器端请求，服务器端**接受** SETUP
   * 客户端建连 & 发送带有__L__标识的  SETUP
   * 服务器接受 SETUP，回应 LEASE frame
   * 客户端发送 LEASE frame
   * 服务器发送 REQUEST
4. 服务器端请求，服务器端**拒绝** SETUP
   * 客户端建连 & 发送带有__L__标识的  SETUP
   * 服务器拒绝 SETUP，回应 ERROR[INVALID_SETUP|UNSUPPORTED_SETUP|REJECTED_SETUP]，关闭连接

## 分段和重组

PAYLOAD frame 和所有的 REQUST frame 可能代表一个大对象，因此可能需要分段以符合 Frame 中的数据尺寸。在这种情况发生时，可以用 __F__ 标志位来表示当前 frame 后是否还有更多的分段。

分段不会改签 request(n) 或者 lease 数目的语义。换句话说，分了段的 PAYLOAD frame 等同于一个 request(n)，一个 request 等同于一个 lease 数目，所以与一个 frame 被分成多少段无关。

#### PAYLOAD Frame

当一个 PAYLOAD frame 需要分段时，将会使用 (F)ollows 标志位来传输一系列的 PAYLOAD frame。

当一个 PAYLOAD 分段时，元数据**必须**在数据之前完全传输。

举例，一个有 20MB 元数据和 25M 数据的 PAYLOAD 被分段成 3 个 frame：

```
-- PAYLOAD frame 1
Frame length = 16MB
(M)etadata present = 1
(F)ollows = 1 (fragments coming)
Metadata Length = 16MB

16MB of METADATA
0MB of Data

-- PAYLOAD frame 2
Frame length = 16MB
(M)etadata present = 1
(F)ollows = 1 (fragments coming)
Metadata Length = 4MB

4MB of METADATA
12MB of Data

-- PAYLOAD frame 3
Frame length = 13MB
(M)etadata present = 0
(F)ollows = 0

0MB of METADATA
13MB of Data
```

如果发送方（请求方或者回应方）要取消一个分段序列的发送，它**可能**会在分段传输完成之前就发送 CANCEL frame。

#### REQUEST Frames

当 REQUEST_RESPONSE、REQUEST_FNF、REQUEST_STREAM、或 REQUEST_CHANNEL 需要分段的时候，第一个 frame 是带有 (F)ollows 标识的 REQUEST_* frame，后续是一系列的 PAYLOAD frame。

分段时，元数据**必须**在数据之前完全传输。

举例，一个有 20MB 元数据和 25M 数据的 PAYLOAD 被分段成 3 个 frame：

```
-- REQUEST_RESPONSE frame 1
Frame length = 16MB
(M)etadata present = 1
(F)ollows = 1 (fragments coming)
Metadata Length = 16MB

16MB of METADATA
0MB of Data

-- PAYLOAD frame 2
Frame length = 16MB
(M)etadata present = 1
(F)ollows = 1 (fragments coming)
Metadata Length = 4MB

4MB of METADATA
12MB of Data

-- PAYLOAD frame 3
Frame length = 13MB
(M)etadata present = 0
(F)ollows = 0

0MB of METADATA
13MB of Data
```

如果请求方要取消一个分段序列的发送，它**可能**会在分段传输完成之前就发送 CANCEL frame。

## Stream 序列和存活时间

Stream 存活一段特定的时间。因此协议的实现可以假设 Steam ID 仅在有限的时间段内有效。这个时间段受限于: a) 底层传输协议连接的存活时间，或 b) 当使用可恢复性跨连接延长会话寿命的情况下，会话的存活时间。在此之上，每一种交互模式都会影响到存活时间，取决于请求方和回应方的交互序列。

在下面的章节中，"RQ -> RS" 代表请求方发送一个 frame 给回应方。"RS -> RQ" 表示回应方发送一个 frame 给请求方。

在下面的章节中，"*" 代表 0 或者更多，"+" 表示 1 或者更多。

一旦 stream "终止"，请求方和回应方就可以"遗忘"对应的 Stream ID，但是**严禁**重用该 Stream ID。更多信息参见 [Stream Identifier](#stream-identifiers)。

<a name="stream-sequences-request-response"></a>

### Request Response

1. RQ -> RS: REQUEST_RESPONSE
2. RS -> RQ: PAYLOAD with COMPLETE

或者

1. RQ -> RS: REQUEST_RESPONSE
2. RS -> RQ: ERROR[APPLICATION_ERROR|REJECTED|CANCELED|INVALID]

或者

1. RQ -> RS: REQUEST_RESPONSE
2. RQ -> RS: CANCEL

自回应 response 起，stream 在接收方终止。

自接收到 CANCEL 起，stream 在接收方终止，**不应该**发送 response。

自发送完 CANCEL 起，stream 在请求方终止。

自接收到 COMPLETE 或 ERROR[APPLICATION_ERROR|REJECTED|CANCELED|INVALID] 起，stream 在请求方终止。

<a name="stream-sequences-fire-and-forget"></a>

### Request Fire-n-Forget

1. RQ -> RS: REQUEST_FNF

Upon reception, the stream is terminated by the Responder.

自接收起，stream 在接收方终止。

自发送起，stream 在请求方终止。

假定 REQUEST_FNF 会尽可能被处理，但是**也有可能**不会：(1) SETUP 被拒绝，(2) 格式错误 (3) 等等

<a name="stream-sequences-request-stream"></a>

### Request Stream

1. RQ -> RS: REQUEST_STREAM
2. RS -> RQ: PAYLOAD*
3. RS -> RQ: ERROR[APPLICATION_ERROR|REJECTED|CANCELED|INVALID]

或者

1. RQ -> RS: REQUEST_STREAM
2. RS -> RQ: PAYLOAD*
3. RS -> RQ: COMPLETE

或者

1. RQ -> RS: REQUEST_STREAM
2. RS -> RQ: PAYLOAD*
3. RQ -> RS: CANCEL

请求方在任何时候都可能发送 REQUEST_N frame。

自接收到 CANCEL 起，stream 在接收方终止。

自发送完 CANCEL 起，stream 在请求方终止。

自接收到 COMPLETE 或 ERROR[APPLICATION_ERROR|REJECTED|CANCELED|INVALID] 起，stream 在请求方终止。

自发送完 COMPLETE 或 ERROR[APPLICATION_ERROR|REJECTED|CANCELED|INVALID] 起，stream 在接收方终止。

<a name="stream-sequences-channel"></a>

### Request Channel

#### 请求方和回应方的 COMPLETE 共存

1. RQ -> RS: REQUEST_CHANNEL

2. RQ -> RS: PAYLOAD*

3. RQ -> RS: COMPLETE

   同时存在 

4. RS -> RQ: PAYLOAD*

5. RS -> RQ: COMPLETE

#### 请求方的错误，回应方终止

1. RQ -> RS: REQUEST_CHANNEL

2. RQ -> RS: PAYLOAD*

3. RQ -> RS: ERROR[APPLICATION_ERROR]

   同时存在

4. RS -> RQ: PAYLOAD*

#### 请求方的错误，回应方已经完成

1. RQ -> RS: REQUEST_CHANNEL

2. RQ -> RS: PAYLOAD*

3. RQ -> RS: ERROR[APPLICATION_ERROR]

   同时存在

4. RS -> RQ: PAYLOAD*

5. RS -> RQ: COMPLETE

#### 回应方错误，请求方终止

1. RQ -> RS: REQUEST_CHANNEL

2. RQ -> RS: PAYLOAD*

   同时存在

3. RS -> RQ: PAYLOAD*

4. RS -> RQ: ERROR[APPLICATION_ERROR|REJECTED|CANCELED|INVALID]

#### 回应方错误，请求方已经完成

1. RQ -> RS: REQUEST_CHANNEL

2. RQ -> RS: PAYLOAD*

3. RQ -> RS: COMPLETE

   同时存在

4. RS -> RQ: PAYLOAD*

5. RS -> RQ: ERROR[APPLICATION_ERROR|REJECTED|CANCELED|INVALID]

#### 请求方取消，回应方终止

1. RQ -> RS: REQUEST_CHANNEL

2. RQ -> RS: PAYLOAD*

3. RQ -> RS: COMPLETE

4. RQ -> RS: CANCEL

   同时存在

5. RS -> RQ: PAYLOAD*

请求方可能在任意时间发送 PAYLOAD frame。

请求方和回应方可能在任意时间发送 REQUEST_N frame。

协议的实现**必须**确保请求方只发送一个单一的初始 REQUEST_CHANNEL frame 给回应方。回应方**必须**使用 REQUEST_N frame 作为对初始 REQUEST_CHANNEL frame 的回应。

自接收到 CANCEL 起，stream 在接受方终止。

自发送完 CANCEL 起，stream 在请求方终止。

自接收到 ERROR[APPLICATION_ERROR|REJECTED|CANCELED|INVALID] 起，stream 在请求方和接收方终止。

自发送完 ERROR[APPLICATION_ERROR|REJECTED|CANCELED|INVALID] 起，stream 在请求方和接收方终止。

没有 ERROR 或者 CANCEL 的情况下，stream 在请求方和回应方都发送并接收到 COMPLETE 后终止。

请求方可以通过在初始的 REQUEST_CHANNEL frame 中或者最后一个 PAYLOAD frame 中设置 C 标志位来表示 COMPLETE。请求方**严禁**在发送了带 C 标志位的 frame 之后再发送额外的 PAYLOAD frame。

## 流量控制

协议提供了多种流量控制机制。

<a name="flow-control-reactive-streams"></a>

### Reactive Streams 语义

[Reactive Streams](http://www.reactive-streams.org/) 语义用在 Streams，Subscriptions，以及 Channels 的流控上。这是一个基于信用的模型，请求方授信回应方可以发送的 PAYLOAD 数目。有时这个模型也被称之为 "request-n" 或者 "request(n)"。

信用值是累加的。一旦请求方授予了回应方某个信用值，就不能再撤回。比如，发送  `request(3)` 和 `request(2)` 会得到累加值 5，容许回应方发送 5 个 PAYLOAD。

需要注意的是，这个行为明确的**不遵循** https://github.com/reactive-streams/reactive-streams-jvm/blob/v1.0.0/README.md#3-subscription-code 中的规则 17。

虽然 Reactive Streams 中信用值可以高达  2^63-1，并且 2^63-1 是一个 magic number，代表可以请求无限数目 ( a magic number signaling to not track demand)，但是 RSocket 不支持这个行为。RSocket 优先考虑字节长度，仅使用 4 字节而不是 8 字节，所以 magic number 无效。

请求方和回应方**必须**遵从 Reactive Streams 语义。

例如，这里展示了一次带流控的流式调用。

1. RQ -> RS: REQUEST_STREAM (REQUEST_N=3)
2. RS -> RQ: PAYLOAD
3. RS -> RQ: PAYLOAD
4. RS -> RQ: PAYLOAD
5. 此时 RS 需要等待一个新的 REQUEST_N
6. RQ -> RS: REQUEST_N (N=3)
7. RS -> RQ: PAYLOAD
8. RS -> RQ: PAYLOAD with COMPLETE

<a name="flow-control-lease"></a>

### 租约语义

LEASE 语义控制在给定时间段内请求方可以发送的请求（所有类型）数量。

对于 LEASE，协议实现的唯一责任是，在请求方遵从它。回应方的应用负责生成的逻辑并通知回应方应当发送一个 LEASE 给对端的请求方。

请求方**必须**遵从 LEASE 契约。请求方**严禁**在 __Time-To-Live__ (LEASE frame 中的字段) 指定的时间段之内发送超过在 __Number of Requests __（(LEASE frame 中的字段)）中指定的请求数。

回应方在收到一个由于 LEASE 限制无法处理的请求时**必须**回应一个 ERROR[REJECTED]。这个可以包含在初始 LEASE 里作为[连接建立](#connection-establishment)的一部分发送。

<a name="flow-control-qos"></a>

### 服务质量和优先级

Stream 的服务质量和优先级是应用层和网络层应该考虑的，并且只有它们才能做的更好。元信息的能力，包括 METADATA_PUSH，是应用可以借助调优优先级的有效工具。

DiffServ via IP QoS 最好由底层的网络层协议来处理。

## 意外的处理

本协议对错误 frame 的处理持容忍态度。如果与当前上下文无关的错误**就应该**采取忽略的态度。下面是针对一些场景的进一步澄清：

1. TCP 半开连接（还有 WebSockets）或者其他类型的死连接可以通过 [Keepalive Frame](#frame-keepalive) 中描述的 KEEPALIVE frame 的缺失来检测。是否因为连接上无活动而关闭连接由应用来决定。
2. 请求 keepalive 和 timeout 的语义是应用的责任。
3. 由于 REQUEST_N frame 的缺失而阻止 stream 是应用应该考虑的事情，而**不应当**由协议来处理。
4. 由于 LEASE frame 的缺失而阻止新的请求是应用应该考虑的事情，而**不应当**由协议来处理。
5. 如果一个 REQUEST_RESPONSE 的 PAYLOAD 没有设置 COMPLETE 标志位，协议实现**必须**按照标志位被设置来处理。
6. PAYLOAD 和 REQUEST_CHANNEL 的重组**必须**考虑无限流的可能。
7. 一个带有 __F__ 和 __C__ 标志位的 PAYLOAD，应当忽略掉其中的 __F__ 标志。
8. 特定 frame 类型中没有要求的标记位**必须**忽略。
9. 对于上面部分没有说明的其他情形的 frame，**必须**忽略。比如：
   1. 收到一个已经在使用中的 Stream ID 的 Request frame **必须**忽略。
   2. 收到未知 Stream ID (包括 0) 上的 CANCEL **必须**忽略。
   3. 收到未知 Stream ID 上的 ERROR **必须**忽略。
   4. 收到未知 Stream ID  (包括 0) 上的 PAYLOAD **必须**忽略。
   5. 收到非 0 Stream ID 上的 METADATA_PUSH **必须**忽略。
   6. 服务器**必须**在接受一个 SETUP 之后忽略后续的 SETUP。
   7. 服务器**必须**忽略 ERROR[INVALID_SETUP|UNSUPPORTED_SETUP|REJECTED_SETUP|REJECTED_RESUME] frame
   8. 客户端**必须**在连接成功建立之后忽略 ERROR[INVALID_SETUP|UNSUPPORTED_SETUP|REJECTED_SETUP|REJECTED_RESUME] frame
   9. 客户端**必须**忽略 SETUP frame。


　

-----------------

[« 动机](README.md)

