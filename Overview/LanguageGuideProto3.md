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
* singular: 一个结构良好的消息可以有0个或一个该字段（但是不能超过一个）。
* repeated: 在一个结构良好的消息中，该字段可以重复多次（也可以是0次）。重复的值的顺序会被保留下来。

在proto3中，标量数字类型的repeated字段默认使用packed编码。

你可以在[这里](https://developers.google.com/protocol-buffers/docs/encoding#packed)了解更多关于packed的信息。

### 添加更多的消息类型
多个消息类型可以定义在同一个.proto文件中。当你需要定义多个相关的消息时这很有用 -- 比如，你想要定义一个响应消息格式SearchResponse，那么你可以把他添加到同一个.proto文件中。
```
message SearchRequest {
    string query = 1;
    int32 page_number = 2;
    int32 result_per_page = 3;
}

message SearchResponse {
    ...
}
```

### 添加注释
用C/C++风格（// 或者 /* ... \*/）的注释语法即可为您的.proto文件添加注释。
```
/* SearchRequest represents a search query, with pagination options to
 * indicate which results to include in the response. */

message SearchRequest {
  string query = 1;
  int32 page_number = 2;  // Which page number do we want?
  int32 result_per_page = 3;  // Number of results to return per page.
}
```

### 保留字段
如果你以直接删除一个字段或者注释掉一个字段的方式来更新一个消息类型，那么后面的开发者做修改的时候可以重用这个字段编号。一旦他们加载了老版本的.proto文件，会导致严重的问题：数据错误，隐蔽的bug等等。避免这个问题的一种方法是：把你删除的字段的编号（和/或者名称, 名称也会导致Json序列化的时候出问题）指定为保留字段（reserved）。那么后面的开发者如果使用了这些字段编号或名称，Protocol Buffer编译器就会报错。
```
message Foo {
  reserved 2, 15, 9 to 11;
  reserved "foo", "bar";
}
```
注意：您不能把字段名称和字段编号放在同一个表达式中。

### 从.proto文件中生成了什么？



