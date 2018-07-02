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
