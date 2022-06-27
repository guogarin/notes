- [1. 编码和解码](#1-编码和解码)
  - [1.1 编解码器(codec/'kodɛk/)](#11-编解码器codeckodɛk)
  - [1.2 编解码问题](#12-编解码问题)






&emsp;
&emsp;
&emsp; 
# 1. 编码和解码
## 1.1 编解码器(codec/'kodɛk/)
&emsp;&emsp; Python 自带了超过 100 种编解码器（codec, encoder/decoder），用于在文本和字节之间相互转换。 每个编解码器都有一个名称， 如 'utf_8'， 而且经常有几个别名， 如 'utf8'、 'utf-8' 和 'U8'。 这些名称可以传给 `open()、str.encode()、bytes.decode()`等函数的 `encoding` 参数。

## 1.2 编解码问题
`SyntaxError: (unicode error)`
`UnicodeEncodeError`
`UnicodeDecodeError`
