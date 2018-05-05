---
title: "Shiro学习记录"
date: 2017-10-15T22:02:11+08:00
lastmod: 2018-05-05T15:29:56+08:00
draft: true
categories: ["Java"]
tags: ["shiro"]
---

安全框架Shiro学习记录
<!--more-->

Shiro是一个Java的安全框架，主要功能包括：身份认证（Authentication）、授权（Authorization）、加密、会话管理等。

对于应用程序来说，Shiro使用流程为：

Application Code -> Subject（主体） -> Security Manager -> Realm（领域）

# 身份认证

## 一个简单的例子

依赖：

```gradle
dependencies {
    testCompile group: 'junit', name: 'junit', version: '4.12'
    compile 'commons-logging:commons-logging:1.1.3'
    compile 'org.apache.shiro:shiro-core:1.2.2'
}
```

登录凭据：

```ini
[users]
zhang=123
wang=123
```

测试代码：

```java
public class LoginLogoutTest {
    @Test
    public void testHelloword() {
        // 获取SecurityManagerFactory，读ini配置文件
        Factory<SecurityManager> factory = new IniSecurityManagerFactory("classpath:shiro.ini");

        // 根据SecurityManagerFactory获取SecurityManager实例
        SecurityManager securityManager = factory.getInstance();
        // 将SecurityManager绑定到SecurityUtils中
        SecurityUtils.setSecurityManager(securityManager);

        // 获取主体
        Subject subject = SecurityUtils.getSubject();

        // 获取用户名/密码的Token
        UsernamePasswordToken token = new UsernamePasswordToken("zhang", "123");

        try {
            subject.login(token);
        } catch (AuthenticationException e) {

        }

        Assert.assertEquals(true, subject.isAuthenticated());

        subject.logout();
    }
}
```

参考文档：

[跟我学Shiro目录贴](http://jinnianshilongnian.iteye.com/blog/2018398)