---
date: 2014-04-05T00:00:00
title: Python转码问题的解决方法
categories: ["Python"]
tags: ["编码"]
---

[参考](http://www.jb51.net/article/16104.htm)

有时，若要将某个String对象s从gbk内码转换为UTF-8，可以如下操作
``` python
s.decode('gbk').encode('utf-8′)
```

<!-- more -->

可是，在实际应用中，我发现，这种办法经常会出现异常：

>UnicodeDecodeError: 'gbk' codec can't decode bytes in position 30664-30665: illegal multibyte sequence

这是因为遇到了非法字符——尤其是在某些用C/C++编写的程序中，全角空格往往有多种不同的实现方式，比如`\xa3\xa0`，或者`\xa4\x57`，这些字符，看起来都是全角空格，但它们并不是“合法”的全角空格（真正的全角空格是`\xa1\xa1`），因此在转码的过程中出现了异常。

这样的问题很让人头疼，因为只要字符串中出现了一个非法字符，整个字符串——有时候，就是整篇文章——就都无法转码。

解决办法：
s.decode('gbk', ‘ignore').encode('utf-8′)
因为decode的函数原型是decode([encoding], [errors='strict'])，可以用第二个参数控制错误处理的策略，默认的参数就是strict，代表遇到非法字符时抛出异常；

- 如果设置为ignore，则会忽略非法字符；
- 如果设置为replace，则会用?取代非法字符；
- 如果设置为xmlcharrefreplace，则使用XML的字符引用。

python文档

>decode( [encoding[, errors]])
Decodes the string using the codec registered for encoding. encoding defaults to the default string encoding. errors may be given to set a different error handling scheme. The default is 'strict', meaning that encoding errors raise UnicodeError. Other possible values are 'ignore', 'replace' and any other name registered via codecs.register_error, see section 4.8.1.
