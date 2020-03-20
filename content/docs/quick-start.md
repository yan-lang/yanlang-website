---
title: 快速开始
type: docs
---

# 快速开始

假设我们有一个语言**Simple**，这个语言只能定义变量，定义变量的规则是先写一个`var`关键字，然后空格接一个`标识符`，标识符只能有字母和数字组成，然后可以有一个初始值`number`，初始值必须是个整数。无需声明类型。

```
'var' ID '=' number
```

现在我们利用Foundation框架，为这个语言编写一个简单的词法分析器和语法分析器。 Let's Start!

## 新建一个项目

下面详细阐述如何将框架导入你的Java项目之中，如果你之前有过使用Java第三方库的经历，你可以选择跳过这一章节，从[下载安装]({{< relref "/docs/download" >}})页面直接下载jar包自行导入。

{{< expand "Yan-Foundation Jar导入教程" "展开" >}}

推荐使用Intellij Idea作为开发环境，当然你也可以使用Eclipse，本文以Idea为例。

1. **打开Idea，新建一个Java项目，选择Java SDK。**
{{< hint info >}}
Foundation框架使用Java11构建，因此你需要使用Java11或更高版本。当前Oracle发布的最新版本是Java13，如果你的版本低于11，你可以前往Oracle官网下载最新版本的Java SDK。
{{< /hint >}}

![create new idea project](/img/quick-start/new-project.png)

一直点`Next`直到创建好项目，你将会得到类似下图的项目结构。如果你勾选了`Create Project from Template`，你将会和我一样获得一个Main文件。如果没有，你可以自己创建一个。

![basic idea project structure](/img/quick-start/idea-project-structure.png)

2. **导入Foundation依赖**

> 对Java不熟悉的同学，可以把这一步想成C++/C的`include`，或者是Python的`pip install`。

在这里，我们使用jar包([点击下载]())的形式导入foundation框架。依照下图步骤操作，在第四步后会弹出一个文件选择界面，选择刚刚下载的jar包，然后一直点击`OK`即可。

![import jar dependency](/img/quick-start/import.png)

{{< /expand >}}

## 最开始的开始

{{< hint info >}}
创建一个可以执行的解释器还有命令行程序需要多少行代码？100？1000？
{{< /hint >}}

使用foundation框架，你只需要在你的main函数中添加如下两行代码即可。

```java
package yan.demo.simple;

import yan.foundation.Language;
import yan.foundation.Launcher;

public class Main {

    public static void main(String[] args) {
        Language language = new Language();
        Launcher.launch(language, args);
    }
}
```

### 解释器

如果你使用`Idea`，点击main函数旁边的绿色三角形即可启动程序。启动之后，你将看到类似下图所示的一个交互式解释器。

![repl demo](/img/quick-start/repl-demo.gif)


你可以在这里输入代码，或者一些内建指令。内建指令以冒号`:`开头，对于其他不以冒号开头的字符串，解释器将其视为`代码`，当你按下回车时，解释器为调用你编写的Phase(如词法分析器)等对代码进行分析，并把结果显示出来（图中展示的例子没有显示任何东西，因为你还没有为你的语言编写Phase！）。

{{< hint info >}}

1. 最常用的内建指令是`:help`，你可以通过这个指令获取解释器支持哪些内建指令。
2. 怎么添加Phase(词法分析器/语法分析器)？后面会讲。

{{< /hint >}}

### 命令行程序

事实上，在获得一个交互式解释器的同时，你还获得了一个Simple语言的命令行程序，你可以像下面这样在命令行中指定输入文件，输出文件，其他参数等，就像使用Python或者GCC一样。

```shell
// 你需要将程序打包成simple.jar
java -jar simple.jar input.simple -o out.txt
```

如果你输入`java -jar simple.jar --help`，你还会看到如下的帮助信息。

```shell
Usage: <main class> [-hV] [-o=<outputFile>] [-t=<target>] <inputFile>
      <inputFile>         The file to be processed.
  -h, --help              Show this help message and exit.
  -o, --output=<outputFile>
                          Output file for result
  -t, --target=<target>   The stage you want to compile to. Valid values: .
                            Default: null
  -V, --version           Print version information and exit.
```

{{< hint info >}}

细心的同学可能会注意到, 这里有一个参数target, 这个参数实际上指定的是你编写的编译器要编译到哪个阶段。因为你还没有为编译器编写任何一个阶段，所以现在它的Valid values和default都是空或者null。

当你编写好词法分析器和语法分析器时，valid value就会变成类似`lex, parse`之类的，到时候你就可以使用命令行参数进行指定，获取中间结果。

类似的，使用我们的框架，这些功能都是免费送的，你几乎不需要编写任何代码。

{{< /hint >}}

### 一些概念

在正式开始编写词法分析器之前，我们先了解一个概念`Phase`。

**Phase**

相信在编译原理的前几节课，老师们都会跟你们讲编译器的组成，通常来说，编译器由5或6个阶段构成，例如词法分析器，语法分析器，语义分析器，中间代码生成，代码优化和目标代码生成。

事实上，当我们把这些过程抽象出来，我们会发现，**每个过程其实就是将程序的一种表示转换成另一种**。在我们的foundation框架中，这个过程被定义为一个`Phase`。例如，词法分析器`Phase<String, List<Token>>`就是从string到一个token列表，语法分析器`Phase<List<Token>, Tree.TopLevel>`就是从Token列表转换成一棵抽象语法树。

在我们的框架中，创建Phase和添加Phase都十分简单，请接着往下看。

## 词法分析器

现在我们来为了我们的Simple语言编写一个词法分析器（Lexer Phase）。

1. **首先新建一个文件`Lexer.java`，修改里面的内容成如下所示**。
    - [AbstractLexer]()是foundation框架中提供的一个Lexer模版，该类继承自`Phase<String, List<Token>>`，并提供许多你在词法分析中可以使用的实用函数（Helper Function）。对于该类你只需要实现`nextToken`函数。

    - [Token]()是foundation框架中定义的单词(Token)的数据结构。

```java
import yan.foundation.compiler.frontend.lex.AbstractLexer;
import yan.foundation.compiler.frontend.lex.Token;

public class Lexer extends AbstractLexer {
    @Override
    public Token nextToken() {
        return null;
    }
}
```

2. **在`Main.java`中添加我们刚刚编写的词法分析器**。

    - 向你的语言中添加一个词法分析器特别简单，只需要调用`语言对象.addPhase()`即可。
    - `addPhase()`包含两个参数：
        - 第一个参数接受一个`Phase<In, Out>`即一个编译阶段。（这就是为什么AbstractLexer需要继承自`Phase<String, List<Token>>`。
        - 第二个参数是一个字符串`target`，这个字符串会作为命令行选项`--target`的可选值。

```java
public static void main(String[] args) throws Exception { // addPhase可能会抛出异常
    Language language = new Language();
    language.addPhase(new Lexer(), "lex"); // 新增
    Launcher.launch(language, args);
}
```

虽然我们还没有真正的编写分析逻辑，不过没关系，现在你可以点击三角箭头，运行看看吧。

## 语法分析器

test