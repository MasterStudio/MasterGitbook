## 在Gitbook中实现多级导航栏的支持

### 前言
gitbook默认只支持两层的导航栏，在遇到内容比较多需要章节层次比较深时，很不方便。
好在后来gitbook终于提供了多级导航栏的支持。

### 实现

具体做法挺简单，以下是一个例子：

```
bui# Summary

* [介绍](introduction/index.md)
    * [mercury 信息](introduction/information.md)
        * [Google Dapper](dapper/index.md)
* [mercury实现](implementation/index.md)
```

就改动两个地方：

1. 第一行从 “# Summary” 修改为 “bui# Summary”
2. 章节的层次结构按照原来的格式要求继续缩进

实际测试结果，可以支持三级和四级，再多我就没有测试了，一般也不需要。

注意：需要新版本的gitbook，具体是哪个版本开始才支持我没有去考究，反正记得用最新版本就是。

### 参考文章

http://skyao.github.io/2015/06/15/gitbook-multiple-layer-menu/