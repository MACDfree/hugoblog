---
title: "根证书生成及签发证书Java实现"
date: 2018-05-17T14:02:39+08:00
lastmod: 2018-05-17T14:02:39+08:00
draft: false
keywords: ["java", "bouncycastle", "生成证书"]
description: "根证书生成及签发证书Java实现"
tags: ["bouncycastle"]
categories: ["java"]
author: ""

# You can also close(false) or open(true) something for this content.
# P.S. comment can only be closed
comment: false
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

Java中使用Keytool可以方便的实现证书的生成及存储。但实际项目中，可能需要在现有系统中实现证书的签发功能，这就要求通过Java代码实现证书的签发。

<!--more-->

# 背景

一个平台产品，为保证各项目与平台信息交互的安全性，计划通过非对称加密方式，私钥签名，公钥验签保证在传输过程中数据不被篡改。为实现此功能，平台中需要提供为各项目提供证书签发功能，各项目提交申请通过后平台会自动生成存储证书和私钥的pfx文件，各项目使用此pfx文件进行签名验签。

# 实现

使用BouncyCastle开源加密库。生成证书大概包括以下步骤：

1. 生成密钥对
2. 填充证书的基本信息和扩展信息
3. 使用私钥进行对证书进行签名
4. 将私钥和签过名的证书存储到文件中

需要注意的是，如果是使用根证书签发证书，上述步骤二中的`Issuer Name`需要设置为根证书的`Subject Name`，步骤三中的私钥应该是根证书的私钥。

下面来看一下具体实现

## 生成密钥对

此处使用RSA生成密钥对：

```java
KeyPairGenerator keyPairGen = KeyPairGenerator.getInstance("RSA");
SecureRandom rand = SecureRandom.getInstance("SHA1PRNG");
keyPairGen.initialize(2048, rand);
return keyPairGen.generateKeyPair();
```

## 填充证书的基本信息和扩展信息

填充证书基本信息

```java
X500NameBuilder nameBuilder = new X500NameBuilder(BCStyle.INSTANCE);
if (sEmailAddress != null) {
  nameBuilder.addRDN(BCStyle.E, sEmailAddress);
}
if (sCountryCode != null) {
  nameBuilder.addRDN(BCStyle.C, sCountryCode);
}
if (sState != null) {
  nameBuilder.addRDN(BCStyle.ST, sState);
}
if (sLocality != null) {
  nameBuilder.addRDN(BCStyle.L, sLocality);
}
if (sOrganisation != null) {
  nameBuilder.addRDN(BCStyle.O, sOrganisation);
}
if (sOrganisationUnit != null) {
  nameBuilder.addRDN(BCStyle.OU, sOrganisationUnit);
}
if (sCommonName != null) {
  nameBuilder.addRDN(BCStyle.CN, sCommonName);
}

BigInteger serial = new BigInteger(Long.toString(System.currentTimeMillis()));

X500Name subjectName = nameBuilder.build();

// 发行者名称
final X500Name issuerName;
if (rootCer == null) {
  // 如果没有根证书，则默认用当前证书
  issuerName = subjectName;
} else {
  issuerName = new X509CertificateHolder(rootCer.getEncoded()).getSubject();
}

Date notBefore = new Date(System.currentTimeMillis());
Date notAfter = new Date(notBefore.getTime() + ((long) iValidity * 24 * 60 * 60 * 1000));

JcaX509v3CertificateBuilder certBuilder = new JcaX509v3CertificateBuilder(issuerName, serial, notBefore, notAfter, subjectName, keyPair.getPublic());
```

需要注意的设置`RDN`的顺序需要倒序，因为通过查看源码发现在将`RDN`数组拼接成字符串时是倒序拼接的。[源码查看](https://github.com/dmlloyd/openjdk/blob/jdk9/jdk9/jdk/src/java.base/share/classes/sun/security/x509/X500Name.java#L1055)

另一个需要注意的是`issuerName`必须和根证书中的`subName`保持一致，因为在存储证书链时，会检查前一个证书的`issuerName`是否等于后一个证书的`subName`。

填充扩展信息

```java
// 添加扩展信息
final JcaX509ExtensionUtils u = new JcaX509ExtensionUtils();
if (rootCer != null) {
  // Authority key identifier 2.5.29.35
  certBuilder.addExtension(Extension.authorityKeyIdentifier, false, u.createAuthorityKeyIdentifier(rootCer));
}
// Subject key identifier 2.5.29.14
certBuilder.addExtension(Extension.subjectKeyIdentifier, false,
    u.createSubjectKeyIdentifier(keyPair.getPublic()));
// Key usage 2.5.29.15
if (rootCer == null) {
  certBuilder.addExtension(Extension.keyUsage, true,
      new KeyUsage(KeyUsage.digitalSignature | KeyUsage.keyEncipherment | KeyUsage.dataEncipherment
          | KeyUsage.keyAgreement | KeyUsage.nonRepudiation | KeyUsage.cRLSign
          | KeyUsage.keyCertSign));
}
// Basic Constraints 2.5.29.19
certBuilder.addExtension(Extension.basicConstraints, false, new BasicConstraints(rootCer == null));
// Extended key usage 2.5.29.37
certBuilder.addExtension(Extension.extendedKeyUsage, false, new ExtendedKeyUsage(
    new KeyPurposeId[] { KeyPurposeId.id_kp_clientAuth, KeyPurposeId.id_kp_serverAuth }));
// CRL distribution points 2.5.29.31
String SERVER_BASE_REST_PKI_URL = "123";
String CRL_URL = "456";
DistributionPointName distributionPoint = new DistributionPointName(new GeneralNames(new GeneralName(
    GeneralName.uniformResourceIdentifier, SERVER_BASE_REST_PKI_URL + issuerName + CRL_URL)));
DistributionPoint[] distPoints = new DistributionPoint[1];
distPoints[0] = new DistributionPoint(distributionPoint, null, null);
certBuilder.addExtension(Extension.cRLDistributionPoints, false, new CRLDistPoint(distPoints));
```

以上扩展信息基本已包含了常用的信息。其中basicConstraints标识此证书是否是CA证书，即是否可以用此证书签发其他证书；keyUsage表示此证书的用途。

## 使用私钥进行对证书进行签名

```java
ContentSigner signer = new JcaContentSignerBuilder(signatureType.name()).build(rootKey);
X509CertificateHolder certHolder = certBuilder.build(signer);

return new JcaX509CertificateConverter().getCertificate(certHolder);
```

## 将私钥和签过名的证书存储到文件中

```java
KeyStore keyStore = null;
keyStore = KeyStore.getInstance("PKCS #12", "BC");
keyStore.load(null, null);
keyStore.setKeyEntry("aaa", keyPair.getPrivate(), new char[] {}, new Certificate[] { certificate, rootCertificate });
FileOutputStream fos = new FileOutputStream(fKeyStoreFile)
keyStore.store(fos, cPassword);
fos.flush();
fos.close();
```
