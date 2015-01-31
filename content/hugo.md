---
date: 2014-07-19T08:47:45Z
draft: false
title: "Hugo"
menu: "main"
weight: "4"
---

# 快速建站之静态网站生成篇


建一个网站是一件十分麻烦的事情，即使对于我们这些互联网专业人士也同样。
有些时候，我们想用最快的方式建一个网站，把内容放到互联网上。

CMS系统其实就是为了达到这样的目的。那为什么不用CMS呢？
使用CMS意味着你需要一台server，还要搭建DB，运行环境等，这些都要花时间。
就不能更简单一些么？于是，有了静态网站生成器。它用一个最实际的特性 **简单**，解决了一个建站的最大的实际问题： **懒！**

<!--more-->

使用静态网站生成器，用户一般需要把内容放在不同的文本文件里，格式常见的有markdown，resturectedText等（完全没有db）。然后，通过命令行工具生成网站所有的html文件。这些html文件放到server上即可使用，不需要复杂的后台技术。这意味着随便搞个server几乎都可以部署，发布**简单** ， 空间流量十分 **便宜** 。由于github提供了github pages静态页面服务，每个人几乎都可以 **免费** 搭建自己的站点。

当然并不是所有的项目都适合静态网站生成器。静态网站生成器一般适合多读少写，以内容为主的项目。如：个人博客，github项目介绍，程序API文档等。
使用静态网站生成的有名的站点列举一些，如：http://www.getbootstrap.com,  https://training.github.com/

## hugo

常用的静态网站生成器很多，Jekyll, Octopress最为流行。还有更多可以参考这里的[详细列表](https://www.staticgen.com/)。这里介绍一个优秀的静态网站生成器 -- hugo。

hugo有以下优点：

*   编译后， *只有一个二进制文件。没有任何依赖* 。

    Jekyll, Octopress都是ruby搞的，个人而言，无法忍受安装ruby只为搞这个。其他基于python，nodejs的也都要安装解释器、安装依赖库！

*   Go语言是编译语言，运行效率高。页面生成速度最快。
*   Windows, linux, mac兼容
*   易于配置，功能强大
*   修改内容后支持liveReload

## 快速上手

### 下载
先去[hugo](http://hugo.spf13.com/)网站上下个包吧，三大平台都打包好的一个文件。

下完之后麻烦放到PATH路径下面哦。

### 建立网站

	hugo new site MySite

hugo会为你生成以下的文件结构。

	▸ content/		内容
	▸ layouts/		模版
	▸ static/		静态文件js, css
	▸ archetypes/	针对类型的内容配置
	  config.toml	站点的配置文件，也可以用yaml, json格式

### 添加内容
	hugo new about.md

hugo为你在content目录下生成了一个文件，目前只有这个内容的元数据。

	+++
	draft = true
	title = "about"
	date = 2014-05-20T10:04:31Z
	+++

我们写些内容进去吧! markdown内容跟在文件后面就行了。 [markdown的语法](http://daringfireball.net/projects/markdown/basics)详见这里。

	+++
	draft = true
	title = "about"
	date = 2014-05-20T10:04:31Z
	+++

	# 标题
	Hello, world!

### 安装样式

默认的hugo项目是没有样式的，俺们安装一些预设的样式。

	git clone --recursive https://github.com/spf13/hugoThemes themes

### 运行测试server

	hugo server --theme=hyde --buildDrafts
	2 pages created
	0 tags created
	0 categories created
	in 5 ms
	Serving pages from exampleHugoSite/public
	Web Server is available at http://localhost:1313
	Press ctrl+c to stop

恭喜！你的站点好了。用浏览器打开http://localhost:1313 看下吧

## 进阶

现在制作的hugo站点是默认的样式，要在实际项目中用，学习自定义模版是必须的。


hugo生成页面的时候有以下一些流程:

1.  copy静态资源到public文件夹，包括：
    copy theme/static/* 到 public/下; copy static/* 到 public/下

2.  遍历所有content/下所有文件
	获取文件头信息，并把正文markdown转换为html

    注：rst格式使用rst2html.py转换rst，使用rst需要安装python docutils

3.  渲染生成每个页面对应的正文页面，生成文件夹对应的列表页面，生成分类页面，生成主页


每个静态页面生成的时候需要两个东西，一个模版，一个结构体数据。

### 模版选择

hugo会从layouts文件夹下一系列路径寻找第一个文件作为模版，这样方便对不同的页面模版进行定制。

例如：正文渲染会依次从以下一些位置寻找模版。首先是layouts文件夹，然后再在themes下面的layouts文件夹寻找。

* /layouts/`TYPE` or `SECTION`/`LAYOUT`.html
* /layouts/`TYPE` or `SECTION`/single.html
* /layouts/\_default/single.html
* /themes/`THEME`/layouts/`TYPE` or `SECTION`/`LAYOUT`.html
* /themes/`THEME`/layouts/`TYPE` or `SECTION`/single.html
* /themes/`THEME`/layouts/\_default/single.html

我们最常使用的模版是以下三个：

* 正文页：/layouts/\_default/single.html
* 列表页：/layouts/\_default/list.html
* 主页：/layouts/index.html


#### 正文

hugo使用的模版语言是go自带的template系统。详细的语法可以参考[这里](http://hugo.spf13.com/templates/go-templates)。

正文渲染使用hugolib/page.go中的Page结构体。Page结构体主要的属性包括：
- Title标题
- Summary摘要
- Content正文
- TableOfContents目录
- ...

有兴趣的同学可以看下这个文件。

例如：一个很简单的正文片段如下。我们把文件放到/layouts/_default/single.html。

	{{ template "partial/header.html" . }}
	{{ template "partial/menu.html" . }}

	<div class="content container">
	<div class="post">
		<h1>{{ .Title }}</h1>
			<span class="post-date">{{ .Date.Format "Mon, Jan 2, 2006" }}</span>
			{{ .Content }}
		</div>
	</div>

	{{ template "partial/footer.html" . }}

注意，这里使用template语句把常用的模块分散，如页头，页尾，菜单。

#### 列表页


对于每个文件夹，hugo会生成列表页，显示下面的所有内容的列表。列表页使用 `hugolib/node.go` 文件中定义的Node结构体渲染页面。

该结构体有两个属性：

-   Site代表站点配置信息，是位于hugolib/site.go中定义的SiteInfo结构体。
-   Data是节点数据，其Pages属性表示子页面的列表。

如下的/layouts/_default/list.html文件对Data.Pages进行了循环，渲染了该文件夹中所有的文件。


	{{ template "partial/header.html" . }}
	{{ template "partial/menu.html" . }}

	<div class="content container">
		<ul class="posts">
		{{ range .Data.Pages }}
		<li>
		<a href="{{ .Permalink }}"><h4>{{ .Title }}</h4></a><time class="pull-right post-list">{{ .Date.Format "Mon, Jan 2, 2006" }}</time>
		</li>
		{{ end }}
		</ul>
	</div>

	{{ template "partial/footer.html" . }}



#### 主页

主页其实也是一个列表文件。渲染数据和列表页结构相同，都是Node结构体。只是模版优先选择`/layouts/index.html`文件
这里就不再赘述了。


### 分类
hugo除了正文，列表，主页三种页面。还支持分类页面tags, categories。

使用时简单的在正文元数据定义下ok。

    tags:
        - "javascript"
        - "python"
        - "go"
    categories:
        - "hugo"
        - "fun"
        - "test"


### 代码高亮

码农必备！hugo使用的python的pygment进行语法高亮的。需要先安装python pygment。


    {{ % highlight javascript %}}
    var b = function () {
    	console.log(123);
    };
    {{ % /highlight %}}

这语法时hugo的shortcode，还可以搞其他的东西，相当于一个内容插件。

### 菜单

hugo的菜单系统支持菜单有两种使用方式：

#### 1a. 在站点配置文件中声明菜单。

如下的yaml文件定义了一个main菜单，和两个菜单条目。

    menu:
        main:
          - name: "about hugo"
            pre: "<i class='ico ico-heart'></i>"
            weight: -110
            identifier: "about"
          - name: "getting-started"
            pre: "<i class='ico ico-road'></i>"
            weight: -110
            url: http://www.bing.com

#### 1b. 在正文的元数据中声明内容所属的菜单。

加入到单个菜单

    ---
    menu = "main"
    ---

加入到多个菜单

    ---
    menu: ["main", "footer"]
    ---


菜单定义好了以后就可以在模版中通过循环显示菜单列表。

    {{ $currentNode := . }}
    <ul class="nav navbar-nav">
    {{ range .Site.Menus.main }}
    <li {{if or ($currentNode.HasMenuCurrent "main" .) ($currentNode.IsMenuCurrent "main" .) }}class="active"{{end}}><a href="{{.Url}}">{{ .Name }}</a></li>
    {{end}}
    </ul>



### 发布到Github Pages

使用静态网站生成器一个很重要的优点是可以免费部署在github!
接下来我们就来看下。

Github pages一般有两种使用。一个是项目的主页，另一种是个人/机构的主页，这种一个用户只能建一个。
作为项目的主页，项目的版本库需要一个命名为gh-pages的分支。这个分支是网站根目录。
作为个人主页，个人需要有一个以自己的名字命名的仓库，其master分支是跟目录。例如：用户名gliheng,仓库gliheng.github.io将是主页的仓库。

这样我们只需要把编译出来的静态文件（/public文件夹）丢到某个仓库的某个分支即可。

我们来试试个人博客。

#### 先去github点Create Repository建立新点仓库xxx。

记得勾上`Initialize this repository with a README`

#### 回到本地

把本地的source分支推到remote的source分支。
把public文件夹的文件推到remote的master分支。

    hugo new site xxx
    cd xxx
    
    git init
    git checkout --orphan source
    
    git add .
    # 把代码提交到source分支吧
    git commit -m "Initial submit"
    
    git remote add origin git@github.com:gliheng/xxx.git
    git pull
    
    # 建立本地master分支
    git branch master
    # 把master作为source分支的子目录了
    git subtree add --prefix public git@github.com:gliheng/xxx.git master --squash
    
    # 添加一个文章，编辑一下
    hugo new blog/first.md
    # 生成public下的站点
    hugo
    
    # 把source分支推到github的source分支
    git push origin source
    # 把public文件夹推到github的master分支
    git subtree push --prefix=public git@github.com:gliheng/xxx.git master

搞定！访问yourname.github.io看下吧。
