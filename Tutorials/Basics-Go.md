# Protocol Buffer基础: Go

此教程只提供一个基本的Go程序员使用protocol buffers的介绍。通过一个简单的示例应用，你将学到如下内容：
* 如何在一个 .proto 文件里定义消息格式
* 如何使用protocol buffer编译器
* 如何使用Java的protocol buffer API来读写消息

这不是一个在Go语言里使用protocol buffer的完整指南。更多细节请参阅[Protocol Buffer语言指南](../Overview/LanguageGuideProto3.md), [Go API手册](https://godoc.org/github.com/golang/protobuf/proto), [Go代码生成指南](https://developers.google.com/protocol-buffers/docs/reference/go-generated) 和 [编码手册](https://developers.google.com/protocol-buffers/docs/encoding)

## 为什么需要使用Protocol Buffers

我们将要用到的例子是一个非常简单的通讯录应用，我们可以从一个文件里读取或写入联系人信息。通讯录中的每个人都有一个名字，一个ID，一个email地址, 和一个联系电话字段。

那么你将如何序列化并且获取像这样的结构化数据呢？解决这个问题可以有下面几种方法：

* 使用[gobs](https://golang.org/pkg/encoding/gob/)序列化Go数据结构。在Go语言环境中这是一个好的解决方案，但是如果你需要与其他平台下的应用共享数据的时候就会出问题。

* 你可以发明一个专门的方法来编码数据，转成一个字符串 -- 比如把4个整数编码成"12:3:-23:67"。尽管这个需要写一次性的编码和转换的代码，并且转换需要消耗一点点运行时资源，但这是一个简单灵活的实现。对于非常简单的数据，这是最好的实现。

* 把数据序列化成XML。这个实现由于XML看起来是可读性比较高，并且很多语言都有现成的操作库，所以比较吸引人。如果你想和其他项目或应用共享数据，那么这是一个好的选择。但是，众所周知，XML是空间密集型的，并且编码/解码的时候会大大影响应用的性能。而且，找到一个XML的DOM节点比在一个类中找一个属性要复杂的多。

对于这个问题，Protocol buffers的灵活，高效，全自动化刚好可以解决它。使用Protocol buffers，你需要写一个.proto文件描述一下你想要存储的数据结构。从定义的文件中，Protocol Buffer编译器会创建一个类，以高效的二进制格式来实现自动编码和转换Protocol Buffer数据。生成好的类提供了属性的getter和setter方法，并且照顾到了作为一个组件读写Protocol Buffer的细节。重要的是，随着时间的推移，当我们需要扩展数据格式的时候，Protocol Buffer格式支持以老的格式读取和编码数据。

## 示例代码在哪里
我们的示例是一些管理通讯录数据文件的命令行应用，是用Protocol buffers编码的。add_person_go命令可以添加一个新的数据到数据文件。list_people_go用来转换数据文件并且把数据在控制台打印出来。

你可以在GitHub仓库找到完整的[示例代码](https://github.com/google/protobuf/tree/master/examples)

## 定义你的协议格式
为了创建你的通讯录应用，你需要先创建一个 .proto文件。.proto文件里的定义很简单：你需要为每个想要序列化的数据结构添加一个message，然后为message里的每一个field（字段）指定一个name（名称）和type（类型）。在我们的代码示例中，.proto文件定义的message是[addressbook.proto文件](https://github.com/google/protobuf/blob/master/examples/addressbook.proto)

.proto文件以包(package)的申明开始，这样可以避免不同项目间的命名冲突。

```
syntax = "proto3";
package turorial;
```

在Go语言里，除非你专门定义了go_package，否则package的名称会被用做Go文件的package名称。尽管你在文件中定义了go_package，你依然需要定义一个package来避免Protocol Buffers的命名冲突。当然，在其他非Go语言的环境中也是一样的。

接下来，你定义好了你的message之后。