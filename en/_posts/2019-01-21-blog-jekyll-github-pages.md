---
layout: post
title:  "Build a Blog with Jekyll and GitHub Pages"
date:   2019-01-21 21:20:39
author: Zixu Wang
categories: Jekyll
tags: blog
lang: en
ref: blog-jekyll-github-pages
---

As a computer science student, I often want to record my learning notes and
experiences in blog posts for sharing, as well as for my own reference. However,
all of my previous countless attempts to build a blog ended up in failure. I
just couldn't persist in maintaining them mainly for two reasons: either I used
free blog platforms such as CSDN and Zhihu, and their limits and poor UI designs
deterred me from writing posts frequently. Or I tried to build a blog space from
scratch with a rented server and got distracted by cumbersome coding in HTML/CSS
and JavaScript.

A few days ago, one of my friends started writing a
[GitBook](https://jizhuoran.gitbook.io){:target='_blank'}, which intrigued my
interest in writing blog posts again. Having learned from previous failures, I
came up with a list of requirements for the blog platform to be used this time:

1. Minimum cost. Avoid dedicated servers;
2. Maximum freedom. Allow me to customize UI and functionalities;
3. Minimum details. Avoid complicated site management so that I can concentrate
on creating contents.

After some research, I found that the combination of
[Jekyll](https://jekyllrb.com/){:target='_blank'} and
[GitHub Pages](https://pages.github.com/){:target='_blank'} perfectly suits my
needs.

## GitHub Pages
<img src="/assets/imgs/Octocat.png" title="GitHub" alt="GitHub" class="img-as-is"/>

[GitHub Pages](https://pages.github.com/){:target='_blank'} is a static-site
hosting service provided by GitHub, intending to help projects and organizations
on GitHub build doc sites.[^1] It is very simple to use GitHub Pages to build
a website. All you need to do is to create a repository named as
`<username>.github.io` (where `<username>` is your GitHub username), and the
files in this repo will be automatically published on
`https://<username>.github.io` by GitHub.[^2]

For example, let's create a file `index.html`：
{% highlight html %}
<!-- index.html -->
<!DOCTYPE html>
<html>
  <head>
    <title>Hello GitHub Pages!</title>
  </head>
  <body>
    <h1>Hello GitHub Pages!</h1>
    <p>This page is hosted on GitHub Pages.</p>
  </body>
</html>
{% endhighlight %}
And push to the `<username>.github.io` repo that we just created. Now access
`https://<username>.github.io` in your web browser and we can see the web page
immediately:

<img src="/assets/imgs/index-example.png" title="Example webpage" alt="Example webpage" class="img-as-is"/>

As you can see, GitHub Pages manages all the trivial details of website servers.
We can rest ourselves and write frontend web pages to build a static website.
The use of this service does come with several limits. For example the size of
the website cannot exceed 1 GB; there is a soft bandwidth limit of 100 GB per
month etc.[^3] Of course illegal uses and contents prohibited by the terms of
service are not allowed. It is also mentioned that GitHub Pages is not intended
for or allowed to run business or other commercial websites etc. However, it is
good enough for our purpose of building a personal blog.

In summary, GitHub Pages provides us with free hosting space for a static
website and a free domain name. We can use <tt>git</tt> to manage our contents
and publish the website easily with a push to the GitHub repo. If wanted, we can
also use custom domain names.[^4] However, despite all the benefits, we still
need to implement the whole frontend by ourselves. It will be a massive amount
of work if we want a beautiful user interface (been there, tried that, gave up
:smiley:). Furthermore, every post must be written as a complete web page using
HTML, which violates our intention to separate content creation and website
management. To this end, we need Jekyll to our rescue.

## Jekyll
<img src="/assets/imgs/jekyll-logo-light-transparent.png" title="Jekyll" alt="Jekyll" class="img-as-is"/>

[Jekyll](https://jekyllrb.com/){:target='_blank'} is a static-site generator
with templates and support for markup languages. This is a typical structure
for a Jekyll project:
{% highlight html %}
{% raw %}
├── 404.html                          # Custom 404 page
├── _config.yml                       # Jekyll configuration file
├── _data                             # Data files
├── _includes                         # Include files
├── index.html                        # Index page
├── _layouts                          # Layout templates
│   ├── default.html                  # Default template
│   └── post.html                     # Post page template
├── _drafts                           # Drafts
│   └── blog-jekyll-github-pages.md   # Unpublished draft
└── _posts                            # Posts
    └── 2019-01-19-hello-world.md     # Published post
{% endraw %}
{% endhighlight %}
We can think of Jekyll as a "compiler" that takes in contents written in markup
languages (Markdown, HTML, etc), and outputs a static website. It converts our
posts (for example `_posts/2019-01-19-hello-world.md`) into corresponding web
pages according to predefined layouts. Then all pages are organized in the
website structure. This makes our life much simpler because Markdown is easier
to write than HTML. If you don't know Markdown, it is definitely worth
learning.[^6] The use of page layout templates also nicely isolates contents
from formats by reusing codes. We only need to write templates in HTML/CSS once,
and focus on writing posts that adopt those templates later. The following is an
example of a layout template:
{% highlight html %}
{% raw %}
<!-- _layouts/default.html -->
<!DOCTYPE html>
<html>
  <head>
    <title>{{ page.title }}</title>
  </head>
  <body>
    <div class="page-content">
      {{ content }}
    </div>
  </body>
</html>
{% endraw %}
{% endhighlight %}
This simple layout file describes an HTML page. The double braces
`{% raw %}{{ }}{% endraw %}` in the layout file is the
[Liquid template language](https://jekyllrb.com/docs/liquid/){:target='_blank'}
used by Jekyll (for more info please refer to the
[official docs](https://shopify.github.io/liquid/){:target='_blank'}[^7]). Now
if we write another file `hello-jekyll.md` that uses this layout:
{% highlight markdown %}
---
layout: default
title: 'Hello Jekyll!'
---

## Hello Jekyll!

This page uses the default layout.
{% endhighlight %}
Then Jekyll will generate a corresponding HTML file:
{% highlight html %}
{% raw %}
<!-- hello-jekyll.html -->
<!DOCTYPE html>
<html>
  <head>
    <title>Hello Jekyll!</title>
  </head>
  <body>
    <div class="page-content">
      <h2 id="hello-jekyll">Hello Jekyll!</h2>
      <p>This page uses the default layout.</p>
    </div>
  </body>
</html>
{% endraw %}
{% endhighlight %}
The README file of Jekyll introduces its philosophy, which I think is very close
to our requirements mentioned above:

> *Jekyll does what you tell it to do -- no more, no less. It doesn't try to outsmart users by making bold assumptions, nor does it burden them with needless complexity and configuration. Put simply, Jekyll gets out of your way and allows you to concentrate on what truly matters: your content.*

To make things even better, GitHub Pages uses Jekyll as its pre-process engine.
Every file we upload to the GitHub Pages repo will be automatically processed by
Jekyll before it is published. [^9] One thing to note, the Jekyll used by GitHub
Pages does not behave exactly the same as the ones installed locally on our
computers. For example, custom plugins are not allowed on GitHub Pages for
security reasons. [^10] There is
[a list of allowd plugins](https://pages.github.com/versions/){:target='_blank'}
that you can use though.[^11]

## Install Jekyll locally

When developing the website or writing posts, we may want to check live results
on our local machines. This section briefly introduces how to use Jekyll locally
(using Ubuntu).

First of all, Jekyll is written in Ruby, therefore we need to install the
runtime for Ruby and the package manager, RubyGem:
{% highlight bash %}
 sudo apt update
 sudo apt install ruby-full
 sudo apt install gem
 sudo gem update --system
{% endhighlight %}
Then install Jekyll and Bundler via RubyGem:
{% highlight bash %}
 sudo gem install jekyll bundler
{% endhighlight %}
After successful installation, we can use Jekyll to create a new blog:
{% highlight bash %}
 jekyll new myblog
{% endhighlight %}
This command will initialize a basic Jekyll project under `./myblog/`:
{% highlight html %}
├── 404.html
├── about.md
├── _config.yml
├── Gemfile
├── Gemfile.lock
├── index.md
└── _posts
    └── 2019-01-21-welcome-to-jekyll.markdown
{% endhighlight %}
We can build and host the website with Jekyll in `./myblog/`. Run
{% highlight bash %}
 cd myblog
 bundle exec jekyll serve
{% endhighlight %}
and access `localhost:4000` in your web browser, then you should be able to
see the website.

<img src="/assets/imgs/jekyll-example.png" title="myblog" alt="myblog" class="img-as-is"/>

If you are new to Jekyll, you can read the
[official docs](https://jekyllrb.com/docs/){:target='_blank'}[^5] and try some
of the features in this basic project. You may push the project to the GitHub
Pages repo created earlier to publish the website.

## Use existing themes

If you don't even bother writing the templates and styles on your own, you can
always find many high-quality open-source themes online. For example on
[JekyllThemes.org](http://jekyllthemes.org/){:target='_blank'} or
[JekyllThemes.io](https://jekyllthemes.io/){:target='_blank'}. This blog you are
currently viewing is modified from a great theme
[Centrarium](https://github.com/bencentra/centrarium){:target='_blank'}. Since
I have made many modifications and added some other functionalities, I also
published my theme on GitHub:
[Jekyll-Theme-HW311](https://github.com/zixu-w/jekyll-theme-hw311){:target='_blank'}.
If you like this theme or want to use it, you are welcomed to fork and star it.
The main features include (including original features from Centrarium):

- In-site searching
- Basic support for multilanguage without plugins (GitHub Pages friendly)
- Easily customizable fonts and colors
- Cover images for your homepage and blog posts
- Archiving of posts by categories
- Syntax highlighting for code snippets
- Disqus integration for post comments
- Lightbox for viewing full-screen photos and albums
- Google Analytics with custom page name tracking
- Social media integration (Twitter, Facebook, LinkedIn, GitHub, and more)

To use this theme, fork it on GitHub and rename it to `<username>.github.io`
(substitute `<username>` with your GitHub username).

Before using this theme, you also need to edit `_config.yml` to configure the
blog to your settings. Entries that needs configuring are:
{% highlight yaml %}
title: '<website title>'
subtitle: '<website subtitle>'
email: '<your email>'
name: '<your name>'
description: '<website description>'
url: '<website url>'                  # e.g. https://<username>.github.io
cover: '<path to cover image>'
logo: '<path to logo>'
disqus_shortname: '<disqus shortname>'
ga_tracking_id: '<ga tracking id>'
social:
  - ...                               # Social networks and sharing settings
protocols:
  ...                                 # OS protocol settings
{% endhighlight %}
After configuring these settings and pushing to GitHub, you can now view your
own blog on `<username>.github.io`! :tada:

To write posts, create `YYYY-MM-DD-<post-title>.md` files under `en/_posts` or
`zh/_posts`. You may find some examples to start with there.

Data files such as multilanguage settings, translations, and post categories etc
can by found under `_data/`.

Please refer to Jekyll's
[official docs](https://jekyllrb.com/docs/){:target='_blank'}[^5] for usage of
Jekyll's features. If you have specific questions about this theme, please leave
comments [below](#disqus_thread) or in the
[GitHub repo](https://github.com/zixu-w/jekyll-theme-hw311){:target='_blank'}.

## Use a custom domain name

If you don't like the `github.io` domain name provided by GitHub, you can
purchase your own domain name and use it for your GitHub Page. Please check the
[tutorial](https://help.github.com/articles/using-a-custom-domain-with-github-pages/){:target='_blank'}
provided by GitHub. [^4]

First, configure the custom domain name in your GitHub Pages repo. You can add
a domain name record via either `Settings -> GitHub Pages -> Custom domain`; or
directly create a `CNAME` file containing your domain name (without protocol
name, for example `hw311.me`, but not
<code>https://<span></span>hw311.me</code>) under the project root directory.

Then configure `A` records with your DNS provider. Set the IP addresses to the
following:[^12]
{% highlight html %}
185.199.108.153
185.199.109.153
185.199.110.153
185.199.111.153
{% endhighlight %}
These IP addresses were obtained from the GitHub Pages
[official docs](https://help.github.com/articles/setting-up-an-apex-domain/#configuring-a-records-with-your-dns-provider){:target='_blank'}[^12]
at the time this post was written. Please check again when configuring.

## Afterword

By now, we have built a personal blog using Jekyll and GitHub. This solution may
have some shortages, for example we can only build static websites; plugins are
limited by GitHub Pages etc., it fits our requirements nevertheless. If you want
to focus on writing posts, you can start right away using templates and layouts.
If you are also interested in tweaking the website and developing new features
like me, hopefully this post and its references may come to your help.

And hopefully I can keep on going this time. (laugh)

<hr>
## References
<ul lang="zh">
  <li><p>
    <a target="_blank" href="http://www.ruanyifeng.com/blog/2012/08/blogging_with_jekyll.html" title="搭建一个免费的，无限流量的Blog----github Pages和Jekyll入门">搭建一个免费的，无限流量的Blog----github Pages和Jekyll入门</a>
  </p></li>
  <li><p>
    <a target="_blank" href="https://www.jianshu.com/p/88c9e72978b4" title="用Jekyll搭建的Github Pages个人博客">用Jekyll搭建的Github Pages个人博客</a>
  </p></li>
</ul>
[^1]: [What is GitHub Pages?](https://help.github.com/articles/what-is-github-pages/){:target='_blank'}
[^2]: [Configuring a publishing source for GitHub Pages](https://help.github.com/articles/configuring-a-publishing-source-for-github-pages/){:target='_blank'}
[^3]: [GitHub Pages - Usage limits](https://help.github.com/articles/what-is-github-pages/#usage-limits){:target='_blank'}
[^4]: [Using a custom domain with GitHub Pages](https://help.github.com/articles/using-a-custom-domain-with-github-pages/){:target='_blank'}
[^5]: [Jekyll Documentation](https://jekyllrb.com/docs/){:target='_blank'}
[^6]: [The Markdown Guide](https://www.markdownguide.org/){:target='_blank'}
[^7]: [Liquid: Safe, customer-facing template language for flexible web apps](https://shopify.github.io/liquid/){:target='_blank'}
[^8]: [Jekyll - Philosophy](https://github.com/jekyll/jekyll#philosophy){:target='_blank'}
[^9]: [Using Jekyll as a static site generator with GitHub Pages](https://help.github.com/articles/using-jekyll-as-a-static-site-generator-with-github-pages/){:target='_blank'}
[^10]: [Adding Jekyll plugins to a GitHub Pages site](https://help.github.com/articles/adding-jekyll-plugins-to-a-github-pages-site/){:target='_blank'}
[^11]: [List of gems used by GitHub Pages](https://pages.github.com/versions/){:target='_blank'}
[^12]: [GitHub Pages - Configuring A records with your DNS provider](https://help.github.com/articles/setting-up-an-apex-domain/#configuring-a-records-with-your-dns-provider){:target='_blank'}
