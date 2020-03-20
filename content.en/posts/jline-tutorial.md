---
author: "Zeqiang Lai"
date: 2020-01-31
linktitle: JLine Tutorial
menu:
  main:
    parent: tutorials
next: /tutorials/github-pages-blog
prev: /tutorials/automated-deployments
title: JLine Tutorial
weight: 10
categories: ["Tutorials"]
# tags: ["pygments"]
--- 

[JLine](https://github.com/jline/jline3)是一个Java版的`GNU ReadLine`，通过它，我们可以很方便的实现一个多功能的命令行程序，支持历史记录，光标移动，自动补全，多行编辑，高亮等。

<!--more-->

## 基本用法

JLine最基本的类是`LineReader`，如下展示了一个最基本的使用方法:

```java
public class Main {
    public static void main(String[] args) {
        LineReader reader = LineReaderBuilder.builder().build();
        String prompt = "> ";
        while (true) {
            String line = null;
            try {
                line = reader.readLine(prompt);
                System.out.println(line);
            } catch (UserInterruptException e) {
                // Ignore
            } catch (EndOfFileException e) {
                return;
            }
        }
    }
}
```

这个命令行程序只支持左右移动光标，历史记录。通过配置`LineReader`我们可以让它支持更多功能，如自动补全，多行编辑等。

![demo](/img/posts/jline1.gif)

### 多行编辑

我们可以通过添加一个parser让reader支持多行编辑，如下:

- `setEofOnUnclosedBracket`配置允许输入大括号，圆括号，方括号时可以进行多行编辑。
- `setEofOnUnclosedQuote`配置允许输入字符串时可以进行多行编辑。

```java
DefaultParser parser = new DefaultParser();
parser.setEofOnUnclosedBracket(Bracket.CURLY, Bracket.ROUND, Bracket.SQUARE);
parser.setEofOnUnclosedQuote(true);

LineReader reader = LineReaderBuilder.builder().parser(parser).build();
```

如果`DefaultParser`不满足你的要求，可以参考其实现自己重写一个，注意要继承`Parser`。

**配置多行编辑模式下的prompt(secondary prompt)**

```java
LineReader reader = LineReaderBuilder.builder().variable(LineReader.SECONDARY_PROMPT_PATTERN, "%M%P > ")
```

### Virtual Terminal

配置terminal可以让程序面对不同平台时更加鲁棒。

```java
Terminal terminal = TerminalBuilder.builder().build();
LineReader reader = LineReaderBuilder.builder().terminal(terminal)
```

当需要输出内容时，使用:

```java
terminal.writer().println(line);
```

### Windows平台支持

Windows平台需要配置JNA, 首先到[Maven](https://search.maven.org/artifact/net.java.dev.jna/jna/5.5.0/jar)下载jar文件（jar就可以了，source和javadoc可以不用），然后把下载下来的jar文件名改成`jna.jar`。

在你编写的命令行程序的`manifest`里添加`Class-Path`，再把你编写的命令行程序打包成jar。

```
Class-Path: jna.jar
```

**运行时**要求两个jar在同一个文件夹中。

参考资料: https://stackoverflow.com/questions/16499084/java-exception-exception-in-thread-main-java-lang-noclassdeffounderror-net-s

## 扩展内容

### Option & Variable

通过`LineReader.option`和`LineReader.variable`可以对LineReader进行详细的配置。

### 自动缩进

配置自动缩进：

```java
LineReader reader = LineReaderBuilder.builder().variable(LineReader.INDENTATION, 2)
```

## 仿Swift REPL

以下代码可以实现一个仿Swift REPL的命令行程序，支持：

- 光标移动，单行时为左右，多行时为上下左右。
- 多行编辑
- 快捷键复制粘贴
- 自动缩进
- 自动补全括号
- 历史记录
- ...

![demo](/img/posts/jline2.gif)

[GitHub](https://github.com/yan-lang/interactive-shell)