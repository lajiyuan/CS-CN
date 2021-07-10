# gRPC简介

## 对 gRPC 和 协议缓冲区的简单介绍

本页用来介绍gRPC和协议缓冲区。gRPC 可以使用协议缓冲区(protocal buffers)作为其接口定义语言 (IDL) 和底层消息交换格式。本篇文章适用于刚接触gRPC和缓冲协议区的新手。如果你仅仅首先想要了解grpc如何运行， [选择一个语言](https://grpc.io/docs/languages/) 并且该尝试页面中的 *Quick start.*



## 概览

在 gRPC 中，一个客户端应用可以直接地调用一个服务端的方法，并且可以跨机器调用，就好像服务端是一个本地对象一样。 gRPC 使得创建一个分布式应用和服务变得更加简单。和许多 RPC 系统一样，gRPC 基于定义服务的思想，指定可以通过参数和返回类型远程调用的方法。在服务器端，服务器实现了这个接口并运行一个 gRPC 服务来处理客户端调用。在客户端有一个存根（在某些语言中简称为客户端），它提供与服务器相同的方法供使用者调用。

![gRPC框架图](https://grpc.io/img/landing-2.svg)

gRPC 客户端和服务端可以在各种环境中运行并相互通信，比如可以从google内部的服务器连接到你自己的笔记本，并且支持用任意gRPC所支持的语言编写服务端和客户端代码。举个例子，你可以很简单地创建一个基于JAVA实现的gRPC 服务端，并且用 Go、Python、Ruby其中任意一种语言作为客户端。此外，最新的 Google API 将具有其接口的 gRPC 版本，让你可以轻松地将 Google 功能构建到你自己的应用程序中。

## 协议缓冲区 (Protocol Buffers)

默认情况下，gRPC使用 [协议缓冲区](https://developers.google.com/protocol-buffers/docs/overview) ，Google 开源的成熟的结构化数据序列化的机制（虽然它可以与其他数据格式一起使用，例如 JSON）。 下面是关于如何使用协议缓冲区的简单介绍，如果你已经对于协议缓冲区很熟悉，可以选择跳过这一章节。

使用协议缓冲区的第一步是定义要在 proto 文件（文件名以.proto为结尾的文本文件）中序列化的数据的结构。协议缓冲区数据被构造为消息，其中每条消息都是一个小的逻辑信息记录，包含一系列称为字段的名称-值对。这里是一个简单示例,文件名为 `person.proto`:
```
message Person {
  string name = 1;
  int32 id = 2;
  bool has_ponycopter = 3;
} 
```
当确认好proto文件后，就可以使用协议缓冲区编译器 protoc 使用proto文件以你喜欢的语言生成对应的数据访问类。数据访问类提供一些简单的接口函数，比如 name() and set_name() 等，以及将整个结构序列化为原始字节 或者 从原始字节解析整个结构的方法。 如果您选择的语言是 C++，则在上面的示例上运行编译器将生成一个名为 Person 的类。你就可以在方法中使用这个类来进行业务逻辑的实现。

你可以在普通的 proto 文件中定义 gRPC 服务，将 RPC 方法参数和返回类型指定为协议缓冲区消息。比如像下面这样。

```
// The greeter service definition.
service Greeter {
  // Sends a greeting
  rpc SayHello (HelloRequest) returns (HelloReply) {}
}

// The request message containing the user's name.
message HelloRequest {
  string name = 1;
}

// The response message containing the greetings
message HelloReply {
  string message = 1;
}
```
gRPC 使用插件 protoc 用 proto 文件生成代码：将得到生成的 gRPC 客户端和服务端代码，以及用于填充、序列化和检索消息类型的常规协议缓冲区代码。使用者只需要实现对应的方法即可完整gRPC服务的编写。 

要了解有关协议缓冲区的更多信息，包括如何使用所选语言的 gRPC 插件安装 protoc，请参阅[协议缓冲区文档](https://developers.google.com/protocol-buffers/docs/overview)。

## 协议缓冲区版本

虽然协议缓冲区已经对开源用户开放一段时间了，但该站点的大多数示例都使用协议缓冲区版本 3 (proto3)，它具有稍微简化的语法、一些有用的新功能并支持更多语言。 Proto3 目前可用于 Java、C++、Dart、Python、Objective-C、C#、lite-runtime (Android Java)、Ruby 和 JavaScript，以及来自 [golang/protobuf](https://pkg.go.dev/google.golang.org/protobuf) 的支持Go语言的生成器，其他的更多语言正在开发中。您可以在 [proto3支持的语言参考文档](https://developers.google.com/protocol-buffers/docs/proto3)  和 这个 [参考文档](https://developers.google.com/protocol-buffers/docs/reference/overview)中找到更多信息。 对于 .proto 文件格式的正式规范可以参考这个文档[proto 格式规范](https://developers.google.com/protocol-buffers/docs/reference/proto3-spec)。

一般来说，虽然你可以使用 proto2（当前的默认协议缓冲区版本），但我们建议你将 proto3 与 gRPC 一起使用，因为它可以让你使用所有 gRPC 支持的语言，并避免 proto2 与 proto3 的客户端服务端之间通信的兼容性问题。


