# 语言指南（proto3）
本指南将指导你如何用Protocol Buffer语言来结构化Protocol Buffer数据，包括：.proto文件的语法和如何如果你的.proto文件里的类来生成数据。

此文档只是一个参考指南 -- 如果要看一步一步的详细示例，请选择的对应语言的[教程
](https://developers.google.com/protocol-buffers/docs/tutorials)

## 定义一个消息类型
首先让我们来看一个非常简单的示例。比如您想要顶一个一个查询请求的消息格式，其中包括一个查询字符串，指定的页码和每页的结果条数。那么这个.proto文件的定义如下:

```
syntax = "proto3"
message SearchRequest {
    string query = 1;
    int32 page_number = 2;
    int32 result_per_page = 3;
}
```

* 第一行声明了你使用的是proto3语法：如果你不这样做的话，Protocol buffer的编译器会认为你要使用proto2的语法。所以应该作为全文件第一个非空并且不为注释的行。
* SearchRequest消息定义了三个字段（name/value 对），每一个都是你想要包括在消息内的的数据。每一个字段都需要定义名称(name)和类型(type)。

### 指定字段类型
在上面的示例中，所有的的字段都是[标量类型](https://developers.google.com/protocol-buffers/docs/proto3#scalar): 两个整数（page_number和result_per_page)和一个字符串(query)。然而，你也可以把字段定义成复合类型（composite types）, 例如[枚举](https://developers.google.com/protocol-buffers/docs/proto3#enum)或者其他消息类型（message types）。

### 设置字段编号
如你所见, 消息里定义的每一个字段都有一个唯一的编号。这些编号用识别[二进制格式的消息](https://developers.google.com/protocol-buffers/docs/encoding)里的字段，并且如果该消息类型已经在使用，那么这个编号就不能更改。注意1到15范围内的数字编码的时候占用一个字节，包括字段编号和字段类型（点此查看[详细说明](https://developers.google.com/protocol-buffers/docs/encoding#structure)）。字段编号在16到2047之间的占两个字节。所以你应该为使用频率高的消息元素保留编号1到15。记住要预留一些空间给未来可以会添加进去的使用频率高的元素。

### 指定字段规则
消息字段可以是以下任意一种：
* 单一字段: 一个结构良好的消息可以有0个或一个该字段（但是不能超过一个）。
* 重复的字段: 在一个结构良好的消息中，该字段可以重复多次（也可以是0次）。重复的值的顺序会被保留下来。



