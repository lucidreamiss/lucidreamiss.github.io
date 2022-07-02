---
title: Hexo博客写作技巧
date: 2022-01-03 21:12:48
tags: [Hexo, Markdown]
description: 本文介绍Hexo博客的写作技巧。
---

## Markdown基本语法

Markdown作为一种标记语言，语法简单且易阅读易编写，可完全脱离鼠标写出样式丰富的文档，是日常工作以及写技术博客必备的技能之一。

| 元素     | Markdown语法                              | 效果预览                                               |
| :--------: | :-----------------------------------------: | :-------------- |
| 标题     | `# 标题1`<br>`## h2`<br>`### h3`          | <div style="font-weight: bold"><div style="font-size: 24px">标题一</div><div style="font-size: 22px">标题二</div><div style="font-size: 20px">标题三</div></div>  |
| 加粗     | `**文字加粗**`                            | **文字加粗**                                           |
| 引用     | `> 引用文字`                              | <blockquote>引用文字</blockquote>                      |
| 有序列表 | `1. 第一项`<br>`2. 第二项`<br>`3. 第三项` | <ol><li>第一项</li><li>第二项</li><li>第三项</li></ol> |
| 无序列表 | `- 第一项`<br>`- 第二项`<br>`- 第三项`    | <ul><li>第一项</li><li>第二项</li><li>第三项</li></ul> |
| 链接     | `[链接](url)`         | [链接](url)                        |
| 图片     | `![图片](image.jpg)`                      |          <i class="fa fa-image"></i>                                             |
| 水平线   | `---`                                     | <hr>                                                   |
| 代码     | <code>\`code\`</code>                     | `code`                                                 |
| 代码块     | <code>\`\`\`code snippet\`\`\`</code>                     |              <figure class="highlight"><table><tbody><tr><td class="code"><pre>code snippet</pre></td></tr></tbody></table></figure>                                   |

{% note info %}
更多语法请参考 [基础语法 | Markdown Guide](https://www.markdownguide.org/basic-syntax) 和 [扩展语法 | Markdown Guide](https://www.markdownguide.org/extended-syntax)
{% endnote %}

## Hexo内置标签

Markdown满足了最基本的文档编写需求，`标签(Tag Plugin)`是Hexo提供的一种快速生成特定内容的方式，用来提供更多样式和功能，例如，标准的Markdown语法中，我们无法制定图片的大小，这是我们可以使用`标签`来解决。值得一提的是，`标签(Tag Plugin)`有Plugin的属性，你可以自主编写独特的标签来满足你的需求。

### 1.文本居中的引用

{% centerquote %} 看，`centerquote`是这样的，俺觉得这个用处不大{% endcenterquote %}

```html 标签语法
<!-- HTML方式: 直接在 Markdown 文件中编写 HTML 来调用 -->
<!-- 其中 class="blockquote-center" 是必须的 -->
<blockquote class="blockquote-center">blah blah blah</blockquote>

<!-- 标签方式 -->
{% centerquote %}blah blah blah{% endcenterquote %}

<!-- 标签别名 -->
{% cq %} blah blah blah {% endcq %}
```

### 2.代码块的进阶用法

```yml _config.xml https://github.com/lucidreamiss/ 链接地址
title: 清明梦
subtitle: '一起变好'
description: '博客'
author: 涛桑
language: zh-CN
timezone: 'Asia/Shanghai'
```

代码块进阶语法规则：

```markdown
    > ```[language] [title] [url] [link text]
    >    code snippet
    > ```    
```

其中，各参数意义如下：

- langugae：语言名称，引导渲染引擎正确解析并高亮显示关键字
- title：代码块标题，将会显示在左上角
- url：链接地址，如果没有指定 link text 则会在右上角显示 link
- link text：链接名称，指定 url 后有效，将会显示在右上角

url 必须为有效链接地址才会以链接的形式显示在右上角，否则将作为标题显示在左上角。以 url 为分界，左侧除了第一个单词会被解析为 language，其他所有单词都会被解析为 title，而右侧的所有单词都会被解析为 link text。

如果不想填写 title，可以在 language 和 url 之间添加至少三个空格。

可以在站点配置文件中设置 `highlight.auto_detect: true` 来开启自动语言检测高亮。

如果设置语言为 diff，可以在代码前添加 + 和 - 来使用如上所示的高亮增删行提示效果，在展示代码改动痕迹时比较实用。

```diff _config.yml
 highlight:
   enable: true
   line_number: false
-  auto_detect: false
+  auto_detect: true
   tab_replace:
```

### 3.note标签

通过 note 标签可以为段落添加背景色，语法如下：

```txt
{% note [class] %}
文本内容 (支持行内标签)
{% endnote %}
```

支持的 class 种类包括 `default` `primary` `success` `info` `warning` `danger`，也可以不指定 class。

{% note primary %}
primary note tag
{% endnote %}

{% note success %}
success note tag
{% endnote %}

{% note info %}
info note tag
{% endnote %}

{% note warning %}
warning note tag
{% endnote %}

{% note danger %}
danger note tag
{% endnote %}

{% note %}
undefined class note tag
{% endnote %}

样式配置可在主题配置文件中进行设置

```yml themes/next/_config.yml
note:
  # Note 标签样式预设
  style: modern  # simple | modern | flat | disabled
  icons: false  # 是否显示图标
  border_radius: 3  # 圆角半径
  light_bg_offset: 0  # 默认背景减淡效果，以百分比计算
```

### 4.label标签

使用label标签可以为文字添加背景色，语法如下：

```txt
{% label [class]@text %}
```

支持的class种类与`note`一致。

示例如下：

{% quote %}
I heard the echo, {% label default@from the valleys and the heart %}
Open to the lonely soul of {% label info@sickle harvesting %}
Repeat outrightly, but also repeat the well-being of
Eventually {% label warning@swaying in the desert oasis %}
{% label success@I believe %} I am
{% label primary@Born as the bright summer flowers %}
Do not withered undefeated fiery demon rule
Heart rate and breathing to bear {% label danger@the load of the cumbersome %}
Bored
{% endquote %}

可在主题配置文件中设置 `label: false` 来取消label标签默认CSS样式。

### 5.button按钮

通过button标签可以快速添加带有主题样式的按钮，语法如下：

```
{% button /path/to/url/, text, icon [class], title %}
```

也可简写为：

```
{% btn /path/to/url/, text, icon [class], title %}
```

其中， 图标ID来源于 [FontAwesome](https://fontawesome.com/v4.7.0/icons/) 。

使用示例如下：

```
{% btn #, 文本 %}
{% btn #, 文本 & 标题,, 标题 %}
{% btn #, 文本 & 图标, home %}
{% btn #, 文本 & 大图标 (固定宽度), home fa-fw fa-lg %}
```

<p>{% btn #, 文本 %}</p>
<p>{% btn #, 文本 & 标题,, 标题 %}</p>
<p>{% btn #, 文本 & 图标, home %}</p>
<p>{% btn #, 文本 & 大图标 (固定宽度), home fa-fw fa-lg %}</p>

### 6.tab标签

tab标签用于快速创建tab选项卡，语法如下

``` html
{% tabs [Unique name], [index] %}
  <!-- tab [Tab caption]@[icon] -->
  标签页内容（支持行内标签）
  <!-- endtab -->
{% endtabs %}
```

其中，各参数意义如下：

- Unique name: 全局唯一的Tab名称，将作为各个标签页的id属性前缀
- index: 当前激活的标签页索引，如果未定义则默认选中显示第一个标签页，如果设为-1则默认隐藏所有标签页
- Tab caption: 当前标签页的标题，如果不指定则会以Unique name加上索引作为标题
- icon: 在标签页标题中添加Font awesome图标

使用示例如下：

``` plain
{% tabs Tab标签列表 %}
  <!-- tab 标签页1 -->
    标签页1文本内容
  <!-- endtab -->
  <!-- tab 标签页2 -->
    标签页2文本内容
  <!-- endtab -->
  <!-- tab 标签页3 -->
    标签页3文本内容
  <!-- endtab -->
{% endtabs %}
```

{% tabs Tab标签列表 %}
  <!-- tab 标签页1 -->
    标签页1文本内容
  <!-- endtab -->
  <!-- tab 标签页2 -->
    标签页2文本内容
  <!-- endtab -->
  <!-- tab 标签页3 -->
    标签页3文本内容
  <!-- endtab -->
{% endtabs %}

### 7.引用站内链接

可以通过如下语法引入站内文章的地址或链接：

```
{% post_path slug %}
{% post_link slug [title] %}
```

其中，`slug` 表示 `_post` 目录下的Markdown文件名。

`post_path` 标签将会渲染为文章的地址，即 `permalink`；而 `post_link` 标签将会渲染为链接，可以通过 `title` 指定链接标题。

如以下标签将会生成 `{% post_path hexo-writing-skills %}`

```txt
{% post_path hexo-writing-skills %}
```

而以下标签则会生成 {% post_link hexo-writing-skills 链接标题 %}

```txt
{% post_link hexo-writing-skills 链接标题 %}
```

这种站内引用方式比直接使用url引用的形式更为可靠，因为即使修改了 `permalink` 格式，或者修改了文章的路由地址，只要Markdown文件名没有发生改变，引用链接都不会失效。

### 8.插入Gist

如果需要在页面内插入Gist上的代码片段时，可以使用如下标签:

```
{% gist gist_id [filename] %}
```

其中，各参数意义如下：

- gist_id: Gist仓库页面url中最后一段随机字符串
- filename: Gist中的文件名

如果Gist中只有一个文件，可以不用指定filename，也可以通过JavaScript脚本的形式直接引入，如：

``` html
<script src="https://gist.github.com/Coodool/cb4ff46a3523955dd4b918dd775b6774.js"></script>
```

如果Gist中有多个文件，可以在标签内输入filename来指定只引入某个文件，如果没有指定filename，将会引入Gist中的所有文件。另外，引用JavaScript脚本形式无法精确控制只引入某一个文件，将会同时引入Gist中的所有文件。

如果指定了与Gist无法匹配的filename，页面上将不会显示任何标签内容。所以，一般在Gist只有一个文件的情况下无需指定filename。

{% note warning %}
在页面中引入Gist代码段将会同时从github服务器上下载脚本与CSS样式文件，由于国内访问github服务器延迟较高，往往资源文件连接和下载的速度很慢，会阻塞页面的渲染进程导致短时白屏。
{% endnote %}

## 插入多媒体

### 插入图片

Markdown并不会保存插入的图片资源本身，只是记录了获取资源的链接。因此我们需要选择一款合适的图床来支持博客写作，目前各大云服务商都提供了对象存储服务，如七牛云KODO、又拍云USS、腾讯云COS、阿里云OSS等。

所以在Markdown中插入一张图片要分为以下几步来进行：

1. 将图片资源上传到图床中
2. 获取图片外链
3. 插入到Markdown文档中

对于博客这种低频访问的应用场景，各大服务商的服务其实并没有显著的差异，并且前期的使用都提供了免费的流量，所以我认为图床的选择主要参考以下几个方面：

- 图床是否提供了便捷的图形化管理工具用于图片的上传下载？

  如阿里云有ossbrowser，腾讯云有cosbrowser，七牛云有QsunSync等，但就本人使用体验来说，七牛云QsunSync的UI界面确实很拙劣，功能较为单一，而腾讯云cosbrowser的界面就相对美观优雅的多，并以Windows资源管理器的交互方式为用户提供资源的上传、下载和管理服务。

  ![腾讯云COS客户端界面截图](https://lucidreamiss-blog-1310999690.cos.ap-chengdu.myqcloud.com/hexo-writing-skills/cos_preview.png "腾讯云COS客户端界面截图")

- 是否能够方便的插入到Markdown文档中？

  这就和服务商的关系不大了，主要看主流Markdown文档编辑器对各大图床的支持程度。其实关于在Markdown中更便捷的插入图片这事儿，最上心的还是文档编辑器的开发者，为了给用户提供更方便的文档写作体验，各大文档编辑器以及支持了Markdown语法的笔记软件都花了不少心思，解决方案无外乎两种，第一种是内置图片存储服务，如有道云笔记（需付费）、石墨文档，第二种是集成了云服务商的图床服务，如Hexo Editor、Mweb等，前者插入图片方便，适用于个人笔记，而后者能够提供CDN加速服务，适用于博客等公开文档。

  其中Hexo Editor支持腾讯云和七牛云的一键上传服务，MWeb支持Imgur、七牛云、又拍云，也可自定义图床服务。

{% note info %}
本站使用腾讯云COS提供对象存储服务。
{% endnote %}

### 网易云音乐

在网页版云音乐中找到歌曲，点击生成外链播放器：

![获取歌曲外链](http://yearito-1256884783.image.myqcloud.com/hexo-writing-skills/20181104044431732.png "获取歌曲外链")

根据个人喜好选择播放器尺寸和播放模式：

![获取插件代码](http://yearito-1256884783.image.myqcloud.com/hexo-writing-skills/20181104044659251.png "获取插件代码")

将获取到的 `iframe` 代码添加到页面中，默认样式如下：

<div class="fluid-vids" style="position: relative; margin-bottom: 20px; width: 100%; padding-top: 10.75%;"><iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width="329" height="86" src="//music.163.com/outchain/player?type=2&amp;id=34613621&amp;auto=0&amp;height=66" style="width: 100%; height: 100%; position: absolute; top: 0px; left: 0px;">
</iframe></div>

播放器宽度将会被拉长占满整个页宽，看起来有点别扭。查看控制台之后发现 `iframe` 在渲染的时候被处理过，外层包了一个类名为 `fluid-vids` 的 `div` 元素。顺藤摸瓜，找到了相关代码，原来是为了让嵌入的视频支持自适应布局，恰好也将 `music.163.com` 域名包含在了处理逻辑内，只需要将该行删除即可。

``` js themes\next\source\js\src\utils.js
var SUPPORTED_PLAYERS = [
  'www.youtube.com',
  'player.vimeo.com',
  'player.youku.com',
  //'music.163.com',
  'www.tudou.com'
];
```

这样播放器样式就变成左对齐固定宽度了，如果你还想让播放器居中，可以将 `iframe` 包在 `<center>` 标签内。

```html
<center>
  <iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=329 height=86 src="//music.163.com/outchain/player?type=2&id=34613621&auto=0&height=66"></iframe>
</center>
```

效果如下：

<center>
  <iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=329 height=86 src="//music.163.com/outchain/player?type=2&id=34613621&auto=0&height=66">
  </iframe>
</center>

{% note warning %}
其实这种通过HTML标签实现CSS样式的做法并不合适，写前端代码的时候不推荐这么做，并且HTML5中也已经废除了 `<center>` `<strong>` 等纯粹为了改变样式而存在的HTML标签，HTML标签应该只负责文档结构，所有样式相关的工作应该交给CSS来实现。
{% endnote %}

如果你发现播放器前后都有空行，可以在控制台中查看元素，检查 `iframe` 元素前后是否多了 `<br>` 元素：

![播放器前后空行代码](http://yearito-1256884783.image.myqcloud.com/hexo-writing-skills/20181027111824579.png "播放器前后空行代码")

造成这样的原因是Markdown渲染引擎把 `<center>` 标签和 `<iframe>` 标签之间的回车当作 `<br>` 来处理了。参考链接: [Hexo issues #1388](https://github.com/hexojs/hexo/issues/1388)

简单的解决方案是将HTML标签写到一行内，但这样会降低代码可编辑性。

另外一种方案是在站点配置文件中添加如下代码：

``` yaml _config.yml
marked:
  gfm: true
  breaks: false
```

重启服务器之后更改才会生效。

{% note warning %}
网易云音乐中部分歌曲因版权保护已经无法生成外链了，即使是通过控制台强行拿到外链地址，嵌入网页后也无法播放。
{% endnote%}

### Aplayer音频播放器

[APlayer](https://aplayer.js.org/#/)是由 [DIYgod](https://github.com/DIYgod) 编写的HTML5音频播放器，提供了另一种音频播放方案。

{% note %}
了解诞生背景： [APlayer - 送给小狐狸和小兔子的 HTML5 播放器](https://diygod.me/2167/)
{% endnote%}

借助 [hexo-tag-aplayer](https://github.com/MoePlayer/hexo-tag-aplayer) 插件，可以通过标签的形式方便快捷的插入音频组件。

在站点根目录下执行以下命令：

``` bash
$ npm install hexo-tag-aplayer --save
```

然后在页面中按照以下标签格式插入歌曲链接和相关信息：

```
{% aplayer title author url [picture_url, narrow, autoplay, width:xxx, lrc:xxx] %}
```

其中，各参数意义如下：

- title: 曲目标题
- author: 曲目作者
- url: 音乐文件 URL 地址
- picture_url: (可选) 音乐对应的图片地址
- narrow: （可选）播放器袖珍风格
- autoplay: (可选) 自动播放，移动端浏览器暂时不支持此功能
- width:xxx: (可选) 播放器宽度 (默认: 100%)
- lrc:xxx: （可选）歌词文件 URL 地址

示例效果如下：

{% aplayer "前前世世 -《君の名は。》" "RADWIMPS" "https://moeplayer.b0.upaiyun.com/aplayer/yourname.mp3" "http://pic.5577.com/up/2016-12/201612891712576.png" lrc:"https://moeplayer.b0.upaiyun.com/aplayer/yourname.lrc" %}

当开启 Hexo 的 [文章资源文件夹](https://hexo.io/zh-cn/docs/asset-folders.html#%E6%96%87%E7%AB%A0%E8%B5%84%E6%BA%90%E6%96%87%E4%BB%B6%E5%A4%B9) 功能时，可以将图片、音乐文件、歌词文件放入与文章对应的资源文件夹中，然后直接引用，示例如下：

```
{% aplayer "Caffeine" "Jeff Williams" "caffeine.mp3" "picture.jpg" "lrc:caffeine.txt" %}
```

如果想要统一实现固定宽度和居中样式，可以在自定义样式文件中添加如下样式规则

``` css themes\next\source\css\_custom\custom.styl
//Aplayer 播放器居中
div.aplayer {
  margin: 5px auto;
  max-width: 500px;
}
```

{% note info %}
插入播放列表功能请参考： [hexo-tag-aplayer | With playlist](https://github.com/MoePlayer/hexo-tag-aplayer#with-playlist)
{% endnote %}