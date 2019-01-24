---
layout: post
title:  "为博客添加$\\LaTeX$支持"
html_title: "为博客添加LaTeX支持"
date:   2019-01-23 02:04:47
author: Zixu Wang
categories: Jekyll
tags: blog 博客 Jekyll LaTeX MathJax
lang: zh
ref: support-latex-in-jekyll-blog
---

在[上一篇文章](/zh/jekyll/2019/01/21/blog-jekyll-github-pages/){:target='_blank'}里我们简单学习了使用Jekyll搭建博客的基础方法，那么这一篇以及接下来的几篇文章都会稍加深入，介绍一些扩充功能、给你的博客锦上添花的方法。这些进阶的介绍都会假设读者已经了解Jekyll的基本概念和使用，最好也已经动手试验甚至搭建出了自己的博客。如果有需要的话，可以随时返回参考[上一篇文章](/zh/jekyll/2019/01/21/blog-jekyll-github-pages/){:target='_blank'}及其参考资料。在这一篇短文里，我会简单介绍一下我是如何使用[MathJax](https://www.mathjax.org/){:target='_blank'}为这个博客提供$\LaTeX$支持的。

> **小知识**：$\LaTeX$是[莱斯利·兰波特（Leslie Lamport）](https://en.wikipedia.org/wiki/Leslie_Lamport){:target='_blank'}在$\TeX$的基础上开发的[^1]，$\LaTeX$中的 “La” 指的就是他（我个人是非常崇拜Lamport的，鼎鼎大名的[Lamport clock](https://en.wikipedia.org/wiki/Lamport_timestamps){:target='_blank'}、[拜占庭将军问题](https://en.wikipedia.org/wiki/Byzantine_fault_tolerance){:target='_blank'}、和[Paxos算法](https://en.wikipedia.org/wiki/Paxos_(computer_science)){:target='_blank'}也都出自他之手）。$\TeX$又是我们熟悉的[高德纳（Donald Knuth）](https://en.wikipedia.org/wiki/Donald_Knuth){:target='_blank'}开发的。Knuth指出[^2]，$\TeX$意指希腊字母 &Tau; (Tau)、	&Epsilon; (Epsilon)、和 &Chi; (Chi)，源自希腊语单词 &tau;έ&chi;&nu;&eta;（发音：/ˈtexni/，大意：艺术，技术），所以正确发音应为 /tɛx/（近似中文“泰赫”），英语里也发音为 /ˈtɛk/（近似中文“泰克”）。书写时应写为 “$\TeX$”，如果书写系统不支持则应写为 “TeX”。同理，$\LaTeX$发音应为 /ˈlɑːtɛx(k)/（“拉泰赫（克）”） 或者
/ˈleɪtɛx(k)/（“雷泰赫（克）”）；书写应为 “$\LaTeX$” 或 “LaTeX” [^1]（Lamport本人表示怎么读无所谓[^1]，你喜欢就好 :smiley:）。

作为一个~~严谨认真的学术交流博客~~，免不了时常会有插入一些简单或复杂的公式的需求。然而我们使用的Markdown或者HTML这些标记语言都没有对公式编辑的原生支持，于是我们就只能使用$\LaTeX$或其他公式编辑语言。一种简单直接的解决方案是使用外部的公式编辑器软件或者网站把我们想要插入的每一条公式提前渲染出来，保存成图片然后插入到文章的相应位置。但是一来这些操作费时费力，出现错误也无法做到灵活编辑；二来当博客规模渐渐增长、文章渐渐丰富之后，这些图片会占据比较多的空间，也会给网页加载增加额外的负担。如果我们能自行整合这个渲染的过程就好了，这样我们就可以直接在Markdown中书写$\LaTeX$代码块，不仅方便编辑还节省空间。我们的答案就是：MathJax.

## MathJax
<img src="https://www.mathjax.org/badge/badge-square.png" title="MathJax" alt="MathJax" class="img-as-is"/>

[MathJax](https://docs.mathjax.org/en/latest/mathjax.html#what-is-mathjax){:target='_blank'}是一个开源的基于JavaScript的数学环境渲染引擎，支持的语言包括$\LaTeX$、MathML、和AsciiMath。[^3] 它不需要我们安装任何的插件，对浏览网页的用户设备和浏览器也没有任何多余要求。只需要在网页上引入MathJax，魔法就自然而然地发生了。本页面就就经过了MathJax的渲染，读者可以直接通过本页查看渲染的效果，或者访问官方给出的[例子](https://www.mathjax.org/#samples){:target='_blank'}和[live demo](https://www.mathjax.org/#demo){:target='_blank'}.

### 使用MathJax

官方推荐的使用方法是通过CDN引入MathJax的JavaScript文件[^4]：
{% highlight html %}
{% raw %}
<script type="text/javascript" async
  src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.5/MathJax.js?config=TeX-MML-AM_CHTML">
</script>
{% endraw %}
{% endhighlight %}
将这段代码放入网页的 `<head>` 段中就可以了。这段代码会加载MathJax的2.7.5版本，并且通过 `?config=TeX-MML-AM_CHTML` 进行了一些配置。这段配置选项具体说的是：

- `TeX`：分辨并支持$\TeX$
- `MML`：分辨并支持MathML
- `AM`：分辨并支持AsciiMath
- `CHTML`：使用CommonHTML作为渲染输出

MathJax使用了很多类似这样的 “[*combined configuration files*](https://docs.mathjax.org/en/latest/config-files.html#common-configurations){:target='_blank'}” [^5] 来方便我们根据需要配置脚本。引入上面的这个combined configuration file和运行下面的配置代码是等价的[^5]：
{% highlight javascript %}
MathJax.Hub.Config({
  jax: ["input/TeX","input/MathML","input/AsciiMath","output/CommonHTML"],
  extensions: ["tex2jax.js","mml2jax.js","asciimath2jax.js","MathMenu.js","MathZoom.js","AssistiveMML.js", "a11y/accessibility-menu.js"],
  TeX: {
    extensions: ["AMSmath.js","AMSsymbols.js","noErrors.js","noUndefined.js"]
  }
});
{% endhighlight %}
我在本站使用的配置是：
{% highlight html %}
{% raw %}
<script type="text/x-mathjax-config">
  MathJax.Hub.Config({
    tex2jax: {
      skipTags: ['script', 'noscript', 'style', 'textarea', 'pre', 'code'],
      inlineMath: [['$','$'], ['\\(','\\)']],
      processEscapes: true
    },
    TeX: {
      equationNumbers: {
        autoNumber: "AMS"
      }
    }
  });
</script>

<script type="text/javascript" async
  src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.5/latest.js?config=TeX-AMS_CHTML">
</script>
{% endraw %}
{% endhighlight %}
先不管上面的一大段具体配置，可以看到我使用了 `?config=TeX-AMS_CHTML` 的配置文件。意思就是我只需要使用$\TeX$并且希望输出尽可能与$\TeX$的输出相同，然后使用CommonHTML作为渲染结果。细心的读者可能还注意到了我使用了 `.../latest.js` 而不是前文提到的 `.../MathJax.js`，这样的话我就可以保证使用最新版本的MathJax脚本。[^6] 读者可以参考[官方文档](https://docs.mathjax.org/en/latest/config-files.html#common-configurations){:target='_blank'}[^5] 来选择最适合自己需要的配置文件。

### 配置MathJax脚本

成功引入了MathJax的脚本，现在我们回头来看看上文中出现的 `MathJax.Hub.Config()` 配置。

`MathJax.Hub.Config()` 这个函数接收一个JSON格式的参数，给我们提供了使用现有的配置文件之外的配置选项，这些配置选项可以被归为七类：[^7]

1. [核心选项](https://docs.mathjax.org/en/latest/options/hub.html){:target='_blank'}
2. [预处理选项](https://docs.mathjax.org/en/latest/options/preprocessors/index.html){:target='_blank'}
3. [输入处理器选项](https://docs.mathjax.org/en/latest/options/input-processors/index.html){:target='_blank'}
4. [输出处理器选项](https://docs.mathjax.org/en/latest/options/output-processors/index.html){:target='_blank'}
5. [扩展选项](https://docs.mathjax.org/en/latest/options/extensions/index.html){:target='_blank'}
6. [其他选项](https://docs.mathjax.org/en/latest/options/other/index.html){:target='_blank'}
7. [第三方扩展选项](https://docs.mathjax.org/en/latest/options/ThirdParty.html){:target='_blank'}

限于篇幅，我在这里只介绍比较常用、我使用到的一些配置选项，其余选项有兴趣的读者可以自行研究。

`skipTags: ['script', 'noscript', 'style', 'textarea', 'pre']`：这是一个 `tex2jax` 预处理器的选项，可以使用这个选项列出我们希望MathJax忽略的HTML元素。相关的选项还有 `ignoreClass` 和 `processClass`.

`inlineMath: [['$','$'], ['\\(','\\)']]`：这也是一个 `tex2jax` 预处理器的选项。这个选项用于指定在行中插入公式的标识符，比如 `$some expression$` 和 `\(some expression\)`.

`processEscapes: true`：这同样是 `tex2jax` 预处理器的选项，如果我们把 `processEscapes` 设置为 `true`，我们就可以使用反斜线（`\`）对用作数学环境标识符的美元符号进行转义。虽然我们已经通过 `skipTags` 选项使代码块中的 `$` 不会被MathJax处理，但是如果我们想在文中使用这个符号呢？比如书写价格的时候可能会写 “\\$23.33”，这时候我们就需要对 “\\$” 进行转义。注意！如果是在HTML文件中，`\$` 就可以完成转义，但是在Markdown文件中，因为 `\` 本身就是代表转义的特殊符号，所以我们需要先将其转义成实际字符 “\\” 才能转义成功。可能有点绕，我们来捋一下。第一步，在Markdown中的 `\\$` 会被Jekyll的Markdown处理器（比如kramdown）转义一次，生成相应的HTML `\$`；然后MathJax预处理器 `tex2jax` 会扫描HTML文件，读到 `\$` 然后将 `$` 转义输出为美元符号 “\\$”。

`equationNumbers: { autoNumber: "AMS" }`：这是一个$\TeX$输入处理器的配置选项。它控制我们的公式编号。`"AMS"` 就是采用AMSmath的编号风格。比如环境 `align`：
{% highlight latex %}
$$
\begin{align}
  \nabla\times\vec{\mathbf{B}}-\frac{1}{c}\frac{\partial\vec{\mathbf{E}}}{\partial t} &= \frac{4\pi}{c}\vec{\mathbf{j}} \\
  \nabla\cdot\vec{\mathbf{E}} &= 4\pi\rho \\
  \nabla\times\vec{\mathbf{E}}+\frac{1}{c}\frac{\partial\vec{\mathbf{B}}}{\partial t} &= \vec{\mathbf{0}} \\
  \nabla\cdot\vec{\mathbf{B}} &= 0
\end{align}
$$
{% endhighlight %}
会被渲染成

$$
\begin{align}
  \nabla\times\vec{\mathbf{B}}-\frac{1}{c}\frac{\partial\vec{\mathbf{E}}}{\partial t} &= \frac{4\pi}{c}\vec{\mathbf{j}} \\
  \nabla\cdot\vec{\mathbf{E}} &= 4\pi\rho \\
  \nabla\times\vec{\mathbf{E}}+\frac{1}{c}\frac{\partial\vec{\mathbf{B}}}{\partial t} &= \vec{\mathbf{0}} \\
  \nabla\cdot\vec{\mathbf{B}} &= 0
\end{align}
$$

公式会被自动编号；而环境 `align*`：
{% highlight latex %}
$$
\begin{align*}
  \dot{x} &= \sigma(y - x) \\
  \dot{y} &= \rho x - y - xz \\
  \dot{z} &= -\beta z + xy
\end{align*}
$$
{% endhighlight %}
会被渲染成

$$
\begin{align*}
  \dot{x} &= \sigma(y - x) \\
  \dot{y} &= \rho x - y - xz \\
  \dot{z} &= -\beta z + xy
\end{align*}
$$

公式没有编号。

<br>

## 更多效果展示

{% highlight latex %}
$$
\frac{1}{\Bigl(\sqrt{\phi \sqrt{5}}-\phi\Bigr) e^{\frac25 \pi}} \equiv 1+\frac{e^{-2\pi}} {1+\frac{e^{-4\pi}} {1+\frac{e^{-6\pi}} {1+\frac{e^{-8\pi}} {1+\cdots} } } }
$$
{% endhighlight %}

$$
\frac{1}{\Bigl(\sqrt{\phi \sqrt{5}}-\phi\Bigr) e^{\frac25 \pi}} \equiv 1+\frac{e^{-2\pi}} {1+\frac{e^{-4\pi}} {1+\frac{e^{-6\pi}} {1+\frac{e^{-8\pi}} {1+\cdots} } } }
$$

<br>
{% highlight latex %}
$$
\left( \sum_{k=1}^n a_k b_k \right)^2 \leq \left( \sum_{k=1}^n a_k^2 \right) \left( \sum_{k=1}^n b_k^2 \right)
$$
{% endhighlight %}

$$
\left( \sum_{k=1}^n a_k b_k \right)^2 \leq \left( \sum_{k=1}^n a_k^2 \right) \left( \sum_{k=1}^n b_k^2 \right)
$$

<br>
{% highlight latex %}
$$
\Gamma\ \Delta\ \Theta\ \Lambda\ \Xi\ \Pi\ \Sigma\ \Upsilon\ \Phi\ \Psi\ \Omega
$$
{% endhighlight %}

$$
\Gamma\ \Delta\ \Theta\ \Lambda\ \Xi\ \Pi\ \Sigma\ \Upsilon\ \Phi\ \Psi\ \Omega
$$

<br>
{% highlight latex %}
$$
\omicron\ \pi\ \rho\ \sigma\ \tau\ \upsilon\ \phi\ \chi\ \psi\ \omega\ \varepsilon\ \vartheta\ \varpi\ \varrho\ \varsigma\ \varphi
$$
{% endhighlight %}

$$
\omicron\ \pi\ \rho\ \sigma\ \tau\ \upsilon\ \phi\ \chi\ \psi\ \omega\ \varepsilon\ \vartheta\ \varpi\ \varrho\ \varsigma\ \varphi
$$

<br>
{% highlight latex %}
$$
\alpha\ \beta\ \gamma\ \delta\ \epsilon\ \zeta\ \eta\ \theta\ \iota\ \kappa\ \lambda\ \mu\ \nu\ \xi
$$
{% endhighlight %}

$$
\alpha\ \beta\ \gamma\ \delta\ \epsilon\ \zeta\ \eta\ \theta\ \iota\ \kappa\ \lambda\ \mu\ \nu\ \xi
$$

<br>
{% highlight latex %}
$$
\gets\ \to\ \leftarrow\ \rightarrow\ \uparrow\ \Uparrow\ \downarrow\ \Downarrow\ \updownarrow\ \Updownarrow
$$
{% endhighlight %}

$$
\gets\ \to\ \leftarrow\ \rightarrow\ \uparrow\ \Uparrow\ \downarrow\ \Downarrow\ \updownarrow\ \Updownarrow
$$

<br>
{% highlight latex %}
$$
\Leftarrow\ \Rightarrow\ \leftrightarrow\ \Leftrightarrow\ \mapsto\ \hookleftarrow
$$
{% endhighlight %}

$$
\Leftarrow\ \Rightarrow\ \leftrightarrow\ \Leftrightarrow\ \mapsto\ \hookleftarrow
$$

<br>
{% highlight latex %}
$$
\leftharpoonup\ \leftharpoondown\ \rightleftharpoons\ \longleftarrow\ \Longleftarrow\ \longrightarrow
$$
{% endhighlight %}

$$
\leftharpoonup\ \leftharpoondown\ \rightleftharpoons\ \longleftarrow\ \Longleftarrow\ \longrightarrow
$$

<br>
{% highlight latex %}
$$
\Longrightarrow\ \longleftrightarrow\ \Longleftrightarrow\ \longmapsto\ \hookrightarrow\ \rightharpoonup
$$
{% endhighlight %}

$$
\Longrightarrow\ \longleftrightarrow\ \Longleftrightarrow\ \longmapsto\ \hookrightarrow\ \rightharpoonup
$$

<br>
{% highlight latex %}
$$
\rightharpoondown\ \leadsto\ \nearrow\ \searrow\ \swarrow\ \nwarrow
$$
{% endhighlight %}

$$
\rightharpoondown\ \leadsto\ \nearrow\ \searrow\ \swarrow\ \nwarrow
$$

<br>
{% highlight latex %}
$$
\bigtriangledown\ \dagger\ \diamond\ \star\ \triangleleft\ \triangleright\ \angle\ \infty\ \prime\ \triangle
$$
{% endhighlight %}

$$
\bigtriangledown\ \dagger\ \diamond\ \star\ \triangleleft\ \triangleright\ \angle\ \infty\ \prime\ \triangle
$$

<br>
{% highlight latex %}
$$
f(x) = \int_{-\infty}^\infty \hat f(\xi)\,e^{2 \pi i \xi x}
$$
{% endhighlight %}

$$
f(x) = \int_{-\infty}^\infty \hat f(\xi)\,e^{2 \pi i \xi x}
$$

<br>
{% highlight latex %}
$$
\mathbf{V}_1 \times \mathbf{V}_2 =
\begin{vmatrix}
  \mathbf{i} & \mathbf{j} & \mathbf{k} \\
  \frac{\partial X}{\partial u} & \frac{\partial Y}{\partial u} & 0 \\
  \frac{\partial X}{\partial v} & \frac{\partial Y}{\partial v} & 0
\end{vmatrix}
$$
{% endhighlight %}

$$
\mathbf{V}_1 \times \mathbf{V}_2 =
\begin{vmatrix}
  \mathbf{i} & \mathbf{j} & \mathbf{k} \\
  \frac{\partial X}{\partial u} & \frac{\partial Y}{\partial u} & 0 \\
  \frac{\partial X}{\partial v} & \frac{\partial Y}{\partial v} & 0
\end{vmatrix}
$$

<br>

## 后记

虽然我选用了MathJax来进行公式渲染，但是这只是众多网页公式渲染引擎之一。其他引擎，比如[KaTeX](https://katex.org/){:target='_blank'}，在渲染速度和渲染效果上各有优劣。KaTeX在速度上比MathJax快很多，但是指令集不足，渲染效果略逊于MathJax。在网上也可以找到KaTeX和MathJax对比的[live demo](https://www.intmath.com/cg5/katex-mathjax-comparison.php){:target='_blank'}[^8]，本文就不多赘述了。

<hr>
## 参考资料
[^1]: [$\LaTeX$ - Wikipedia](https://en.wikipedia.org/wiki/LaTeX){:target='_blank'}
[^2]: [$\TeX$ Pronunciation and spelling - Wikipedia](https://en.wikipedia.org/wiki/TeX#Pronunciation_and_spelling){:target='_blank'}
[^3]: [What is MathJax?](https://docs.mathjax.org/en/latest/mathjax.html#what-is-mathjax){:target='_blank'}
[^4]: [MathJax - Getting Started](https://docs.mathjax.org/en/latest/start.html){:target='_blank'}
[^5]: [MathJax - Combined Configurations](https://docs.mathjax.org/en/latest/config-files.html#common-configurations){:target='_blank'}
[^6]: [MathJax - Loading and Configuring MathJax](https://docs.mathjax.org/en/latest/configuration.html){:target='_blank'}
[^7]: [MathJax - Configuration Options](https://docs.mathjax.org/en/latest/options/index.html){:target='_blank'}
[^8]: [KaTeX and MathJax Comparison Demo](https://www.intmath.com/cg5/katex-mathjax-comparison.php){:target='_blank'}
