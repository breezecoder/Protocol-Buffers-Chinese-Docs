# Protocol Buffer基础: Java

此教程只提供一个基本的Java程序员使用protocol buffers的介绍。通过一个简单的示例应用，你将学到如下内容：
* 如何在一个 .proto 文件里定义消息格式
* 如何使用protocol buffer编译器
* 如何使用Java的protocol buffer API来读写消息

这不是一个在Java里使用protocol buffer的完整指南。更多细节请参阅[Protocol Buffer语言指南](../Overview/LanguageGuideProto3.md), [Java API手册](https://developers.google.com/protocol-buffers/docs/reference/java/), [Java代码生成指南](https://developers.google.com/protocol-buffers/docs/reference/java-generated) 和 [编码手册](https://developers.google.com/protocol-buffers/docs/encoding)

## 为什么需要使用Protocol Buffers

我们将要用到的例子是一个非常简单的通讯录应用，我们可以从一个文件里读取或写入联系人信息。通讯录中的每个人都有一个名字，一个ID，一个email地址, 和一个联系电话字段。

那么你将如何序列化并且获取像这样的结构化数据呢？解决这个问题可以有下面几种方法：

* 使用Java序列化. 