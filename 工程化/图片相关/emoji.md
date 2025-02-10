## Unicode

Unicode 是一种电脑编码系统，它试图为世界上所有文本字符提供唯一的数字表示。在unicode还没有问世前，英语国家使用的编码规则是ASCII，但是亚洲许多国家，比如中国，又有自己的编码规则，如GB2312。各国之间传输文件一定会因此出现乱码的问题。而Unicode的出现帮我们统一了这些规则。

不管是哪种语言、哪种符号，Unicode 都提供了一种方法来将它们转换为数字码，所以它们可以在计算机和互联网上使用。

### 码点

Unicode 规定了每个字符的数字编号，这个编号被称为码点(code point)。码点以 U+hex 的形式表示，U+是代表Unicode的前缀，而 hex 是一个16进制数。取值范围是从 U+0000 到 U+10FFFF。

每个码点对应一个字符，绝大部分的常见字符在最前面的 65536 个字符，范围是 U+0000到U+FFFF。 一般汉字的码点区间为 U+2E80 - U+9FFF。

### 字符平面

目前的Unicode分成了17个编组，也称平面，每个平面有65536个码点。

- 第一个平面是基本多语言平面，范围：U+0000 - U+FFFF，多数常见字符都在该区间。
- 其他平面则为辅助平面，范围：U+10000 到 U+10FFFF，如我们在网上常见 Emoji 表情。

### 码元

码元(Code Unit)可以理解为对码点进行编码时的最小基本单元，规定计算机应该如何存储这个码点。而字符编码的作用就是将Unicode 码点转换成码元序列。 Unicode常用的编码方式有 UTF-8 、UTF-16 和 UTF-32，UTF是Unicode TransferFormat的缩写。UTF-8是8位的单字节码元，UTF-16是16位的双字节码元，UTF-32是32位的四字节码元。

#### UTF-16

javascript中的字符串是基于UTF-16编码的，字符串的length属性，实际上是使用UTF-16的码元个数来进行计算的：

- ASCII码和大部分中文，都是一个码元
- 而表情字符和其他特殊字符都是两个码元

具体编码规则：

- 基本多语言平面：
  - 码点小于U+FFFF，基本字符，不需处理，直接使用，占两个字节
  - 在Unicode基本多语言平面内，从U+D800到U+DFFF之间的码点区间是不对应字符的，UTF-16需要利用这块码位来对辅助平面的字符进行编码。
- 辅助平面：拆分成两个码元，四个字节，cp表示码点：
  - 低位——((cp - 65536) / 1024) + 0xD800，值范围是 0xD800~0xDBFF；
  - 高位——((cp - 65536) % 1024) + 0xDC00，值范围是 0xDC00~0xDFFF

```JavaScript
汉字:
'好'.codePointAt() // 22909，码点小于U+FFFF，直接进行十六进制转换：579D

表情符号 
'😄'.codePointAt() 
// 128516，码点需要拆分：
// 低位：Math.floor(((128516 - 65536) / 1024)) + 0xD800 // 55357, 得到 D83D
// 高位：((128516 - 65536) % 1024) + 0xDC00 // 56836，得到 DE04
```

## Emoji

#### 为什么不同 emoji 的长度不一样呢？

```JavaScript
'😊'.length // 2
'🤔️'.length // 3
'🤦🏻♂️'.length // 7
```

- 先对 emoji 拆解一下

```JavaScript
[...'😊'] // ['😊']

[...'🤔️'] 
// ['🤔', '️'] 
// '️'.length === 1

[...'🤦🏻♂️'] 
// ['🤦', '🏻', '', '♂', '️']
```

![img](https://bytedance.sg.larkoffice.com/space/api/box/stream/download/asynccode/?code=MGRkMjMzMjE3YmY2MjFhZDcxZjQxZDZmMDg5MjE1NzVfdTRVS0YzNlR5dE83RExDN1lSbXdSMFdVS3ZtMlljbkZfVG9rZW46U1NvdWIwbDhUb0dkd0Z4WHdUMGNHWnBmbldkXzE3MTg5NDg3NjI6MTcxODk1MjM2Ml9WNA)

- 这些长度为 1 的空字符是什么呢

```JavaScript
'🤔️'.charCodeAt(2).toString(16) // 'fe0f'

'🤦🏻♂️'.charCodeAt(4).toString(16) // 200d
'🤦🏻♂️'.charCodeAt(6).toString(16) // 'fe0f'
```

Unicode 有一些非打印字符，所以在大多数文本编辑器或显示器中，是可能无法直观地看到它们的。

1. U+200D：零宽连接符（Zero-width joiner)，它可以使两个本不会发生连字的字符产生连字效果。当被放在两个emoji之间时，可以让它成为一个新的字符。
   1.  零宽字符是一种特殊的 Unicode 字符，它没有可视化表现，也就是在文本中不占用任何空间。常见的零宽字符有零宽空格（Zero-width space, U+200B）和零宽连接符（Zero-width joiner, U+200D）等。这些字符常常用于控制字符的间隔和联结，但在正常阅读中是不可见的。
2. U+FE0F：变体选择符，是一种可以改变前一个字符显示样式的 Unicode 字符。当它跟随在某些字符后面时，可以将这些字符显示为 emoji 样式。

#### 怎么将 emoji 的长度作为 1 计算 length

https://www.npmjs.com/package/runes?activeTab=readme

```JavaScript
'hello 😊'.length // 8
=> 
['h','e','l', 'l', 'o', ' ', '😊'].length // 7
```

## Unicode in ES2018

https://github.com/tc39/proposal-regexp-unicode-property-escapes