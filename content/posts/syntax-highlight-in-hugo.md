---
author: "赖泽强"
date: 2020-01-28
linktitle: 为Hugo添加Yan语言代码高亮支持
menu:
  main:
    parent: tutorials
next: /tutorials/github-pages-blog
prev: /tutorials/automated-deployments
title: 为Hugo添加Yan语言代码高亮支持
weight: 10
# categories: ["Plugins"]
# tags: ["pygments"]
---

为了能让文档阅读起来更顺畅，更美观，我们通常会高亮文档中出现的代码。Hugo使用高亮器[chroma]()来对代码进行分析实现代码高亮，但是chroma不支持我们自己定义的语言Yan。

{{< hint info >}}
另一种高亮器是Pygments, 但是Hugo自0.6版本后就删除了使用pygments作为高亮器的选项。
{{< /hint >}}

为了能让Hugo支持高亮Yan语言，我们可以修改chroma或Pygments让它们支持高亮Yan语言，再重新编译Hugo，安装，但是，这种方法太麻烦了。因此，这里我们采用另一种方式进行workaround。

<!--more-->

## Workaround

### 1.扩展Pygments

我们首先扩展pygments，让其支持高亮Yan语言，详见[pgments-yanlexer]()。

扩展之后，我们可以使用下列指令获取高亮后的html的代码,以及对应的css代码:

```shell
// 对power.yan进行高亮输出html
pygmentize -f html -o power.html power.yan

// 获取css代码
pygmentize -f html -a .highlight -S trac > highlight.css
```

### 2.复制HTML代码

我们知道markdown是支持直接渲染HTML代码，因此我们可以直接把转换后的HTML代码替换markdown里的代码块。

### 3.复制CSS代码

为了让Hugo懂得如何渲染代码区域的HTML代码，我们需要把`highlight.css`里的所有内容复制到`/assets/_custom.scss`中。
再稍微调整一下字体，行距等:

```css
.highlight .yan{
    border-radius: .15rem;
    font-size: .8em;
    line-height: 2em;
    font-family: "roboto mono",monospace;
}
.highlight pre {
    background-color: #f8f9fa !important;
}
```

{{< hint info >}}
Hugo自己的代码块的trac风格背景色是纯白的，这样会不太好看，因此使用`.highlight pre`强制覆盖pre原来的背景色。
{{< /hint >}}

## 效果

{{< columns >}}
### HTML代码
```html
<div class="highlight"><pre class='yan'><span></span><span class="k">func</span> <span class="nf">power</span><span class="p">(</span><span class="n">base</span><span class="p">:</span> <span class="kt">int</span><span class="p">,</span> <span class="n">e</span><span class="p">:</span> <span class="kt">int</span><span class="p">)</span> <span class="o">-&gt;</span> <span class="kt">int</span> <span class="p">{</span>
    <span class="k">var</span> <span class="n">i</span> <span class="o">=</span> <span class="mf">0</span><span class="p">;</span>
    <span class="k">var</span> <span class="n">result</span> <span class="o">=</span> <span class="mf">1</span><span class="p">;</span>
    <span class="k">while</span><span class="p">(</span><span class="n">i</span> <span class="o">&lt;</span> <span class="n">e</span><span class="p">)</span> <span class="p">{</span>
        <span class="n">result</span> <span class="o">*=</span> <span class="n">base</span><span class="p">;</span>
        <span class="n">i</span> <span class="o">+=</span> <span class="mf">1</span><span class="p">;</span>
    <span class="p">}</span>
    <span class="k">return</span> <span class="n">result</span><span class="p">;</span>
<span class="p">}</span>
</pre></div>
```
<--->

### 效果

<div class="highlight"><pre class='yan'><span></span><span class="k">func</span> <span class="nf">power</span><span class="p">(</span><span class="n">base</span><span class="p">:</span> <span class="kt">int</span><span class="p">,</span> <span class="n">e</span><span class="p">:</span> <span class="kt">int</span><span class="p">)</span> <span class="o">-&gt;</span> <span class="kt">int</span> <span class="p">{</span>
    <span class="k">var</span> <span class="n">i</span> <span class="o">=</span> <span class="mf">0</span><span class="p">;</span>
    <span class="k">var</span> <span class="n">result</span> <span class="o">=</span> <span class="mf">1</span><span class="p">;</span>
    <span class="k">while</span><span class="p">(</span><span class="n">i</span> <span class="o">&lt;</span> <span class="n">e</span><span class="p">)</span> <span class="p">{</span>
        <span class="n">result</span> <span class="o">*=</span> <span class="n">base</span><span class="p">;</span>
        <span class="n">i</span> <span class="o">+=</span> <span class="mf">1</span><span class="p">;</span>
    <span class="p">}</span>
    <span class="k">return</span> <span class="n">result</span><span class="p">;</span>
<span class="p">}</span>
</pre></div>

{{< /columns >}}
