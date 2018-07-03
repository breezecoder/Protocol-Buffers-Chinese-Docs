# GO语言代码生成

本文章描述了Protocol Buffer编译器如何依据一个指定的协议定义来生成Go语言的代码。proto2和proto3生成的代码的区别被高亮标识出来了 -- 注意这里所说的区别是指本文档所述的生成的代码里面的区别，而不是基础的API，基础API在两个版本中是一样的。
在读本文之前你应该先读[proto2语言指南](https://developers.google.com/protocol-buffers/docs/proto) 和（或者）[proto3语言指南](../Overview/LanguageGuideProto3.md)。

## 编译器调用
Protocol Buffer编译器需要一个插件来[生成Go语言代码](https://github.com/golang/protobuf)。先安装该插件：
`$ go get github.com/golang/protobuf/protoc-gen-go`


