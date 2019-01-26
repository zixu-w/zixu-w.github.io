---
layout: post
title:  "Add $\\LaTeX$ support to your blog"
html_title: "Add LaTeX support to your blog"
date:   2019-01-23 02:04:47
author: Zixu Wang
categories: Jekyll
tags: blog Jekyll LaTeX MathJax
lang: en
ref: support-latex-in-jekyll-blog
---

In
[the last post](/en/jekyll/2019/01/21/blog-jekyll-github-pages/){:target='_blank'}
we briefly introduced how to build a blog with Jekyll. This post and several
following posts will dive in a little bit deeper and introduce some methods to
extend and improve your blog. I will assume that readers have already become
familiar with concepts and features of Jekyll. You can always go back and refer
to
[the last post](/en/jekyll/2019/01/21/blog-jekyll-github-pages/){:target='_blank'}
if needed. In this short post, I will introduce how to add $\LaTeX$ support to
this blog using [MathJax](https://www.mathjax.org/){:target='_blank'}.

> **Fun Fact**：$\LaTeX$ was originally developed by
[Leslie Lamport](https://en.wikipedia.org/wiki/Leslie_Lamport){:target='_blank'}
based on $\TeX$[^1]. The "La" in $\LaTeX$ refers to Lamport. $\TeX$ in turn, was
developed by
[Donald Knuth](https://en.wikipedia.org/wiki/Donald_Knuth){:target='_blank'}.
Knuth pointed out that $\TeX$ stands for Greek capital letters &Tau; (Tau),
&Epsilon; (Epsilon), and &Chi; (Chi), as the name derives from the Greek:
&tau;έ&chi;&nu;&eta; (skill, art, technique).[^2] Therefore, the pronunciation
should be /tɛx/ or /ˈtɛk/. Similarly, $\LaTeX$ should be pronounced as
/ˈlɑːtɛx(k)/ or /ˈleɪtɛx(k)/.[^1]

As a ~~serious academic~~ blog, we sometimes want to include mathematical
equations and formulas in posts. However, the Markdown or HTML language we use
does not directly support formula editing. One simple solution is to use
external tools to generate images of formulas and insert those images to our
posts. This method does not allow us to easily edit the formulas. What if I find
something wrong after inserting all the images? The excessive use of images also
burdens our website and page loading. We want to render equations locally using
Jekyll, for example writing $\LaTeX$ codes directly in Markdown. The answer to
this is MathJax.

## MathJax
<img src="https://www.mathjax.org/badge/badge-square.png" title="MathJax" alt="MathJax" class="img-as-is"/>

[MathJax](https://docs.mathjax.org/en/latest/mathjax.html#what-is-mathjax){:target='_blank'} is an open-source mathematical environment rendering engine in
JavaScript, supporting multiple languages such as $\LaTeX$, MathML, and
AsciiMath.[^3] No plugin is required. No installation on the user side is
needed. All you need is to include MathJax in your web page, then magic happens.
This page was processed by MathJax, you can check the outcome directly here, or
check the [samples](https://www.mathjax.org/#samples){:target='_blank'} and
[live demo](https://www.mathjax.org/#demo){:target='_blank'} given by MathJax.

### How to use MathJax

It is recommended to use CDN to include MathJax[^4]:
{% highlight html %}
{% raw %}
<script type="text/javascript" async
  src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.5/MathJax.js?config=TeX-MML-AM_CHTML">
</script>
{% endraw %}
{% endhighlight %}
Just put this code snippet into the `<head>` section. It will load MathJax
version 2.7.5, and configure the script with `?config=TeX-MML-AM_CHTML`. This
configuration says

- `TeX`: recognize and supprot $\TeX$
- `MML`: recognize and supprot MathML
- `AM`: recognize and supprot AsciiMath
- `CHTML`: use CommonHTML to render output

MathJax uses a lot of "[*combined configuration files*](https://docs.mathjax.org/en/latest/config-files.html#common-configurations){:target='_blank'}"[^5] like this to provide different configurations for us to
include. The combined configuration file above is equivalent to the following
configuration code[^5]:
{% highlight javascript %}
MathJax.Hub.Config({
  jax: ["input/TeX","input/MathML","input/AsciiMath","output/CommonHTML"],
  extensions: ["tex2jax.js","mml2jax.js","asciimath2jax.js","MathMenu.js","MathZoom.js","AssistiveMML.js", "a11y/accessibility-menu.js"],
  TeX: {
    extensions: ["AMSmath.js","AMSsymbols.js","noErrors.js","noUndefined.js"]
  }
});
{% endhighlight %}
The configuration I used for this website is:
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
Just ignore the configuration code above for now. I used the `TeX-AMS_CHTML`
configuration file, which means I only need to use $\TeX$ and use CommonHTML as
the rendering output. You may also find that I used `.../latest.js` instead of
`.../MathJax.js` mentioned above. This ensures that the latest version is
used.[^6] Please refer to the
[offical docs](https://docs.mathjax.org/en/latest/config-files.html#common-configurations){:target='_blank'}[^5]
to find the right combined configuration file for you.

### Configuring MathJax

Now that we have successfully included MathJax, let's go back and review the
`MathJax.Hub.Config()` function.

`MathJax.Hub.Config()` receives a configuration parameter in JSON format and
provides us with a rich set of configuration options, which can be categorized
into 7 groups:[^7]

1. [The core options](https://docs.mathjax.org/en/latest/options/hub.html){:target='_blank'}
2. [Preprocessor options](https://docs.mathjax.org/en/latest/options/preprocessors/index.html){:target='_blank'}
3. [Input processor options](https://docs.mathjax.org/en/latest/options/input-processors/index.html){:target='_blank'}
4. [Output processor options](https://docs.mathjax.org/en/latest/options/output-processors/index.html){:target='_blank'}
5. [Extension options](https://docs.mathjax.org/en/latest/options/extensions/index.html){:target='_blank'}
6. [Other options](https://docs.mathjax.org/en/latest/options/other/index.html){:target='_blank'}
7. [Third-party extensions](https://docs.mathjax.org/en/latest/options/ThirdParty.html){:target='_blank'}

Limited by the length of this post, I will only introduce common options that
I used.

`skipTags: ['script', 'noscript', 'style', 'textarea', 'pre']`: This is an
option of the `tex2jax` preprocessor. It lists HTML tags that we'd like MathJax
to ignore. Similar options include `ignoreClass` and `processClass`.

`inlineMath: [['$','$'], ['\\(','\\)']]`: This is also a `tex2jax` option. It
specifies delimiters used in inserting inline equations. For example
`$some expression$` or `\(some expression\)`.

`processEscapes: true`: Another `tex2jax` option. If it is set to `true`, we can
use backslashes to escape the dollar sign. Note that in HTML, `\$` is sufficient
to output a "\\$" symbol. However, if you are writing in Markdown, you need to
use `\\$` as `\` itself is a special character used by Markdown to denote
"escaping."

`equationNumbers: { autoNumber: "AMS" }`: This is an option for the $\TeX$ input
processor. It controls the numbering of equations. `"AMS"` means using AMSmath
style numbering. For example, the environment `align`:
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
will be rendered as

$$
\begin{align}
  \nabla\times\vec{\mathbf{B}}-\frac{1}{c}\frac{\partial\vec{\mathbf{E}}}{\partial t} &= \frac{4\pi}{c}\vec{\mathbf{j}} \\
  \nabla\cdot\vec{\mathbf{E}} &= 4\pi\rho \\
  \nabla\times\vec{\mathbf{E}}+\frac{1}{c}\frac{\partial\vec{\mathbf{B}}}{\partial t} &= \vec{\mathbf{0}} \\
  \nabla\cdot\vec{\mathbf{B}} &= 0
\end{align}
$$

Note that the equations are automatically numbered. While the `align*`
environment:
{% highlight latex %}
$$
\begin{align*}
  \dot{x} &= \sigma(y - x) \\
  \dot{y} &= \rho x - y - xz \\
  \dot{z} &= -\beta z + xy
\end{align*}
$$
{% endhighlight %}
will be rendered as

$$
\begin{align*}
  \dot{x} &= \sigma(y - x) \\
  \dot{y} &= \rho x - y - xz \\
  \dot{z} &= -\beta z + xy
\end{align*}
$$

Note the equations are not numbered.

<br>

## More examples

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

## Afterword

Though I adopted MathJax to render formulas, it is only one of many similar
JavaScript math rendering engines. For example,
[KaTeX](https://katex.org/){:target='_blank'} is faster than MathJax, but
limited in command sets and rendering effect. You may find a
[live demo](https://www.intmath.com/cg5/katex-mathjax-comparison.php){:target='_blank'}[^8]
that compares the two.

<hr>
## References
[^1]: [$\LaTeX$ - Wikipedia](https://en.wikipedia.org/wiki/LaTeX){:target='_blank'}
[^2]: [$\TeX$ Pronunciation and spelling - Wikipedia](https://en.wikipedia.org/wiki/TeX#Pronunciation_and_spelling){:target='_blank'}
[^3]: [What is MathJax?](https://docs.mathjax.org/en/latest/mathjax.html#what-is-mathjax){:target='_blank'}
[^4]: [MathJax - Getting Started](https://docs.mathjax.org/en/latest/start.html){:target='_blank'}
[^5]: [MathJax - Combined Configurations](https://docs.mathjax.org/en/latest/config-files.html#common-configurations){:target='_blank'}
[^6]: [MathJax - Loading and Configuring MathJax](https://docs.mathjax.org/en/latest/configuration.html){:target='_blank'}
[^7]: [MathJax - Configuration Options](https://docs.mathjax.org/en/latest/options/index.html){:target='_blank'}
[^8]: [KaTeX and MathJax Comparison Demo](https://www.intmath.com/cg5/katex-mathjax-comparison.php){:target='_blank'}
