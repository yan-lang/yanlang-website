---
title: 下载安装
type: docs
---

# 下载安装

Yan Foundation是一个以jar包形式发布的Java framework。你可以直接下载jar包导入你的java项目中，也可以使用你熟悉的构建工具，例如Maven和Gradle。

**下载Jar包**

你可以在以下地方下载到预编译的jar包，当前最新版为`0.2-beta`。

1. [Github Release]()
2. [Maven Central]()

**使用构建工具**

{{< tabs "uniqueid" >}}
{{< tab "Maven" >}}
在你的Maven项目中的pom.xml添加以下内容:
```xml
<dependency>
    <groupId>com.github.yan-lang</groupId>
    <artifactId>yan-foundation</artifactId>
    <version>1.0-SNAPSHOT</version>
</dependency>
```

{{< /tab >}}

{{< tab "Gradle" >}}

暂无教程

{{< /tab >}}

{{< /tabs >}}

## 项目模版

如果你只是想要快速体验这个框架，看看它能做些什么，你也可以选择直接下载下面的这些模版。

- 使用Idea的Java项目, 请下载[yan-foundation-idea-java]()项目。
- 使用Idea的Maven项目，请下载[yan-foundation-idea-maven]()项目。