---
author: "Zeqiang Lai"
date: 2020-03-23
linktitle: Maven Tutorial
menu:
  main:
    parent: tutorials
title: Maven Tutorial
weight: 10
categories: ["Tutorials"]
--- 

Maven真的是看上去难，学了之后感觉还行，还行之后又觉得难。被Maven的发布(Deployment)整的晕头转向，赶紧在这里记录一下过程，免得到时候忘了。

<!--more-->

## 简介

Maven这个东西可以用的很复杂，但是我们没这需求，所以对这东西，我们把他当成一个编译工具就好（实际也是233）。

想象一下我们写了一个Java项目，我们希望能够编译，测试，打包，发布这个项目。不用构建工具的话，通常编译是比较简单的，一般用IDE按个按钮就行了，打包就稍微麻烦一点，测试没试过不知道，发布就很麻烦了，想象一下，我们每次发布都要手动执行编译，测试，打包，上传到某个网站，真的很麻烦。

另外，如果我们的项目有很多依赖的话也是很麻烦的，不用构建工具的话，你得自己去下载jar包，导到你用的IDE里，如果要和别人合作的话，另一个人也得弄一遍，实在是麻烦。

Maven就是自动化这些过程的一个工具。

## 基础用法

最简单的用法就是用Idea新建一个Maven项目，它会自动帮你建好Maven需要的目录结构。

{{< hint info >}}
Maven对目录结构是有一个默认要求的，一般来说是根目录有个pom.xml（maven的配置文件），然后代码放在src里，src里还有有源码和测试代码等等，具体不说了。
{{< /hint >}}

一般来说，你就是要在`pom.xml`写一些配置，指明你的项目的名字版本，依赖有啥，构建的时候有什么要求之类的。

当然，你也可以创建submodule（就是子项目），比如你有一个很大的项目，你想把公共代码提取出来作为一个模块，这时submodule就有用了。

具体的用法以后再补充了，下面先说怎么发布你的项目到中央仓库（这样别人就可以下载你的库/程序）。

## 发布

### 发布到Central

### 发布到Github Package

在你能够发布到Central之后，基本只需要简单配置一下就可以发布到Github Package了。

参考链接:
https://help.github.com/en/packages/using-github-packages-with-your-projects-ecosystem/configuring-apache-maven-for-use-with-github-packages

如果要用Github Package的话，在你的pom.xml里加入类似的配置:

```xml
<repositories>
    <repository>
        <id>github</id>
        <name>GitHub Yan Apache Maven Packages</name>
        <url>https://maven.pkg.github.com/yan-lang/yan</url>
        <releases><enabled>true</enabled></releases>
        <snapshots><enabled>true</enabled></snapshots>
    </repository>
</repositories>

<dependencies>
    <dependency>
        <groupId>com.github.yan-lang</groupId>
        <artifactId>yan-foundation</artifactId>
        <version>0.1.2</version>
    </dependency>
</dependencies>
```

除此之外，还要在`.m2/settings.xml`中配置GITHUB_TOKEN。

```xml
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0
                      http://maven.apache.org/xsd/settings-1.0.0.xsd">
  <servers>
    <server>
      <id>github</id>
      <username>你的Github用户名</username>
      <password>你的Github Personal access Token</password>
    </server>
  </servers>
</settings>
```

## 问题

### Javadoc unnamed module

发布的时候一定要有Javadoc，但是老是遇到这个问题:

```shell
[ERROR] Failed to execute goal org.apache.maven.plugins:maven-javadoc-plugin:3.0.1:jar (attach-javadocs) on project ldp-testsuite: MavenReportException: Error while generating Javadoc: 
[ERROR] Exit code: 1 - javadoc: error - The code being documented uses modules but the packages defined in https://docs.oracle.com/javase/8/docs/api/ are in the unnamed module.
```

找了一天快崩溃了，终于在Github上找到解决方案:
- https://github.com/w3c/ldp-testsuite/issues/234#issuecomment-517231144
- https://github.com/assertj/assertj-parent-pom/commit/9b521bc33ac81b9a809de39c00599a9f928934d8

但是！事实上是我submodule的parent的version写错了！！！

### Javadoc Enable-Preview

Maven生成javadoc的时候，对java13的switch expression老是报错，叫我们加enable-preview。
一开始不知道怎么加，后面终于找到解决方案，如下。

```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-javadoc-plugin</artifactId>
    <version>3.1.1</version>
    <configuration>
        <additionalJOptions>
            <additionalJOption>--enable-preview</additionalJOption>
            <additionalJOption>--release</additionalJOption>
            <additionalJOption>13</additionalJOption>
        </additionalJOptions>
    </configuration>
</plugin>
```

参考资料:
- http://maven.apache.org/plugins/maven-javadoc-plugin/javadoc-mojo.html#additionalOptions
- https://docs.oracle.com/en/java/javase/11/tools/javadoc.html