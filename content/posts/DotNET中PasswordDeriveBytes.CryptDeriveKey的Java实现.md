---
title: ".NET中PasswordDeriveBytes.CryptDeriveKey的Java实现"
date: 2018-05-10T08:13:12+08:00
lastmod: 2018-05-10T08:13:12+08:00
draft: false
keywords: ["PasswordDeriveBytes", "CryptDeriveKey", ".NET", "Java"]
description: ".NET中PasswordDeriveBytes.CryptDeriveKey的Java实现"
tags: ["CryptDeriveKey"]
categories: ["java"]
author: ""

# You can also close(false) or open(true) something for this content.
# P.S. comment can only be closed
comment: true
toc: true
autoCollapseToc: false
postMetaInFooter: true
hiddenFromHomePage: false
# You can also define another contentCopyright. e.g. contentCopyright: "This is another copyright."
contentCopyright: ""
reward: true
mathjax: false
mathjaxEnableSingleDollar: false

flowchartDiagrams:
  enable: false
  options: ""

sequenceDiagrams: 
  enable: false
  options: ""

---

新老系统分别基于Java和.NET，老系统中使用`PasswordDeriveBytes.CryptDeriveKey`进行3DES密钥的生成。为实现新老系统的互操作性，需要使用Java实现密钥生成逻辑。

<!--more-->

# 为什么要使用密钥生成器

对于对称加密算法，需要使用一个密钥将明文加密为密文，那这个密钥如何产生呢？一个最简单的做法就是将用户提供的密码直接转成字节数组作为密钥。但这会导致如下问题：

1. 各种加密算法所要求的密钥长度不尽相同。以3DES为例，所需的密钥长度为192位，即需要用户提供24字节的密码，否则会导致密钥位数不够。还有一种做法是自动填充字符以满足密钥长度，这会导致密码中会有相同的字符进行加解密，使密文更容易被破解。
2. 用户输入的密码一般只包含a-z，A-Z，0-9，也可能包含一些符合。这意味着每个字节只能使用70-75种可能的组合。另外，并不是每个符号都会使用偶数频率。在英语密码的单个字节中熵的各种估计值从1.3到4位不等。这意味着为了生成一个好的256位密钥，你需要一个长度为64到197字节的密码。

[参考文档](https://blogs.msdn.microsoft.com/shawnfa/2004/04/14/generating-a-key-from-a-password/)

# .NET中PasswordDeriveBytes类介绍

为了解决上述问题，.NET提供了PasswordDeriveBytes类进行密钥的生成，可以实现屏蔽不同加密算法所需密钥长度的不同，生成指定加密算法的合适密钥长度。示例代码如下：

```c#
PasswordDeriveBytes pderiver = new PasswordDeriveBytes("password", null);
byte[] key = pderiver.CryptDeriveKey("TripleDES", "SHA1", 0, new byte[8]);
```

通过查看PasswordDeriveBytes类源码发现，CryptDeriveKey方法最终是调用了微软的加密dll实现

```c#
[System.Security.SecuritySafeCritical]  // auto-generated
public byte[] CryptDeriveKey(string algname, string alghashname, int keySize, byte[] rgbIV)
{
    if (keySize < 0)
        throw new CryptographicException(Environment.GetResourceString("Cryptography_InvalidKeySize"));

    int algidhash = X509Utils.NameOrOidToAlgId(alghashname, OidGroup.HashAlgorithm);
    if (algidhash == 0)
        throw new CryptographicException(Environment.GetResourceString("Cryptography_PasswordDerivedBytes_InvalidAlgorithm"));
    int algid = X509Utils.NameOrOidToAlgId(algname, OidGroup.AllGroups);
    if (algid == 0)
        throw new CryptographicException(Environment.GetResourceString("Cryptography_PasswordDerivedBytes_InvalidAlgorithm"));

    // Validate the rgbIV array
    if (rgbIV == null)
        throw new CryptographicException(Environment.GetResourceString("Cryptography_PasswordDerivedBytes_InvalidIV"));

    byte[] key = null;
    DeriveKey(ProvHandle, algid, algidhash,
                _password, _password.Length, keySize << 16, rgbIV, rgbIV.Length,
                JitHelpers.GetObjectHandleOnStack(ref key));
    return key;
}

//
// private methods
//
[System.Security.SecurityCritical]  // auto-generated
[ResourceExposure(ResourceScope.None)]
[DllImport(JitHelpers.QCall, CharSet = CharSet.Unicode), SuppressUnmanagedCodeSecurity]
private static extern void DeriveKey(SafeProvHandle hProv, int algid, int algidHash, byte[] password, int cbPassword, int dwFlags, byte[] IV, int cbIV, ObjectHandleOnStack retKey);
```

[参考文档](https://referencesource.microsoft.com/#mscorlib/system/security/cryptography/passwordderivebytes.cs,69f601fc0efaa588,references)

# Java实现方式

由于微软的加密dll并没有开源，所以Java中并没有PasswordDeriveBytes.CryptDeriveKey的官方实现。同时也查找了.NET的开源实现Mono中的相关源码，发现此方法也没有开源实现，见[mono源码](https://github.com/mono/mono/blob/07adebd8bf94f2c4310421460940fd6c873b24d5/mcs/class/referencesource/mscorlib/system/security/cryptography/passwordderivebytes.cs)。

通过搜索，发现有和我遇到相同问题的解决方法，但经测试，发现并不能实现.NET和Java的互通。

最后尝试参考 [C++ implementation of CryptDeriveKey](https://groups.google.com/forum/#!topic/microsoft.public.security.crypto/GdZ3vbFrUxo) 和 [CryptDeriveKey方法介绍](https://msdn.microsoft.com/en-us/library/windows/desktop/aa379916(v=vs.85).aspx) 自己实现密钥生成算法，算法说明如下：

> 1. Form a 64-byte buffer by repeating the constant 0x36 64 times. Let k be the length of the hash value that is represented by the input parameter hBaseData. Set the first k bytes of the buffer to the result of an XOR operation of the first k bytes of the buffer with the hash value that is represented by the input parameter hBaseData.
> 2. Form a 64-byte buffer by repeating the constant 0x5C 64 times. Set the first k bytes of the buffer to the result of an XOR operation of the first k bytes of the buffer with the hash value that is represented by the input parameter hBaseData.
> 3. Hash the result of step 1 by using the same hash algorithm as that used to compute the hash value that is represented by the hBaseData parameter.
> 4. Hash the result of step 2 by using the same hash algorithm as that used to compute the hash value that is represented by the hBaseData parameter.
> 5. Concatenate the result of step 3 with the result of step 4.
> 6. Use the first n bytes of the result of step 5 as the derived key.

按照此步骤进行实现后，经测试，发现部分字节会与正确字节相差1，例如：

``` js
// .NET
[212, 29, 193, 4, 11, 26, 237, 176, 53, 50, 159, 110, 57, 184, 185, 188, 229, 48, 73, 251, 199, 170, 248, 131]

// Java
[213, 28, 193, 4, 11, 26, 236, 176, 52, 50, 158, 110, 56, 185, 185, 188, 229, 49, 73, 251, 199, 171, 248, 131]
```

对字节二进制进行分析后发现可能是做了奇偶校验。具体逻辑为：如果二进制中1的个数为偶数，判断最低位为0时，将最低位设置为1，当最低位为1时，设置为0，保证1的个数为奇数。

结合上述逻辑，生成密钥代码如下：

```java
private static byte[] sha1(byte[] password) {
    try {
        MessageDigest md = MessageDigest.getInstance("SHA-1");
        md.reset();
        return md.digest(password);
    }
    catch (NoSuchAlgorithmException nsae) {
        nsae.printStackTrace();
        return new byte[0];
    }
}

private static byte[] cryptDeriveKey(byte[] password) {

    byte[] buff1 = new byte[64];
    byte[] buff2 = new byte[64];
    Arrays.fill(buff1, (byte) 0x36);
    Arrays.fill(buff2, (byte) 0x5C);

    byte[] hash = sha1(password);

    for (int i = 0; i < hash.length; i++) {
        buff1[i] ^= hash[i];
        buff2[i] ^= hash[i];
    }

    byte[] derivedKey = new byte[24];

    byte[] a = sha1(buff1);
    byte[] b = sha1(buff2);

    for (int i = 0; i < 24; i++) {
        if (i < a.length) {
            derivedKey[i] = parityOfOdd(a[i]);
        }
        else {
            derivedKey[i] = parityOfOdd(b[i - a.length]);
        }
    }

    return derivedKey;
}

private static byte parityOfOdd(byte b) {
    int k = 0x01;
    int count = 0;
    for (int i = 0; i < 8; i++) {
        if ((b & k) != 0) {
            count++;
        }
        k = k << 1;
    }
    if (count % 2 == 0) {
        b = (byte) (b ^ 0x01);
        return b;
    }
    else {
        return b;
    }
}
```
