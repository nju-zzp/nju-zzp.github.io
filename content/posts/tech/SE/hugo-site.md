---
title: "Hugo博客搭建与部署" #标题
date: 2023-03-10T02:16:24+08:00 #创建时间
lastmod: 2023-03-10T02:16:24+08:00 #更新时间
author: ["River"] #作者
keywords: #关键词
    - Hugo
    - PaperMod
    - 博客
    - vercel
categories: #类别
    - 
tags: #标签
    - 博客
    - Hugo
    - Vercel
    - 
description: "" #描述
weight: # 输入1可以顶置文章，用来给文章展示排序，不填就默认按时间排序
slug: ""
draft: false # 是否为草稿
comments: true #是否展示评论
showToc: true # 显示目录
TocOpen: true # 自动展开目录
hidemeta: false # 是否隐藏文章的元信息，如发布日期、作者等
disableShare: true # 底部不显示分享栏
showbreadcrumbs: true #顶部显示当前路径
cover:
    image: "/images/hugo-start.png" #图片路径：posts/tech/文章1/picture.png
    zoom: # 图片大小，例如填写 50% 表示原图像的一半大小
    caption: "" #图片底部描述
    alt: ""
    relative: false
    
reward: true # 打赏
---

# Hugo博客搭建与部署｜PaperMod主题

## 1 安装Hugo与主题

1. 安装Hugo，详见官方文档 [Hugo Installation Overview](https://gohugo.io/installation/)

2. 安装成功 `hugo version`检验

3. 新建站点 `hugo new site xxx_site_name -f yml`

   **关键：使用`yml`格式文件，方便主题使用和可读性**

4. 下载主题 以PaperMod为例 [PaperMod Wiki Doc](https://github.com/adityatelange/hugo-PaperMod/wiki)

   安装到themes目录下面

5. 设置配置文件为该主题

   ```yml
   theme: PaperMod # 主题名字，和themes文件夹下的一致
   ```

6. **关键：复制主题下的一些静态文件和配置文件到站点目录下，方便自定义样式优先（覆盖主题样式）**

   - `assets`
   - `i18n`
   - `layouts`
   - `resources`
   - `static`
   
7. 在终端直接输入`hugo server -D`就可以在本地预览了

8. 输入`hugo`就可以生成public文件夹，这个文件夹可以部署到云服务器或者托管到github上

## 2 配置文件设置

站点配置文件推荐改成`.yaml` / `.yml`后缀的写法，因为更适合阅读，也是主题默认的配置文件格式

站点配置文件详细可以检索Hugo的官方文档：[Configure Hugo](https://gohugo.io/getting-started/configuration/)

```yml
baseURL: https://www.riverblog.cn/
languageCode: zh-cn
title: River's BLOG
theme: PaperMod

enableInlineShortcodes: true #允许内联短码
enableEmoji: true # 允许使用 Emoji 表情，建议 true
enableRobotsTXT: true # 允许爬虫抓取到搜索引擎，建议 true

hasCJKLanguage: true # 自动检测是否包含 中文日文韩文 如果文章中使用了很多中文引号的话可以开启

paginate: 10    # 每页显示的文章数

minify:    # 最小化站点的体积
  disableXML: true  # 不允许XML

permalinks: #浏览器链接显示方式
  post: "/:title/"
  # post: "/:year/:month/:day/:title/"

menu: # 顶栏菜单
  main:
    - identifier: search
      name: Search
      url: /search/
      weight: 50
    - identifier: posts
      name: Essays
      url: /posts/
      weight: 3
    - identifier: archives
      name: Time
      url: /archives/
      weight: 20
    - identifier: tags
      name: Tags
      url: /tags/
      weight: 40
    - identifier: about
      name: About
      url: /about/
      weight: 60
    - identifier: friend
      name: Friends
      url: /friends
      weight: 70



# content的分类方式, 每个post的front matter里设置响应的项就好，比如加tags
taxonomies:
  category: categories
  series: series
  tag: tags

markup:
  goldmark:
    renderer:
      unsafe: true # HUGO 默认转义 Markdown 文件中的 HTML 代码，如需开启的话
  highlight:
    # anchorLineNos: true
    codeFences: true  
    guessSyntax: true
    lineNos: false
    # noClasses: false
    # style: monokai
    style: darcula

    # codeFences：代码围栏功能，这个功能一般都要设为 true 的，不然很难看，就是干巴巴的-代码文字，没有颜色。
    # guessSyntax：猜测语法，这个功能建议设置为 true, 如果你没有设置要显示的语言则会自动匹配。
    # hl_Lines：高亮的行号，一般这个不设置，因为每个代码块我们可能希望让高亮的地方不一样。
    # lineNoStart：行号从编号几开始，一般从 1 开始。
    # lineNos：是否显示行号，我比较喜欢显示，所以我设置的为 true.
    # lineNumbersInTable：使用表来格式化行号和代码,而不是 标签。这个属性一般设置为 true.
    # noClasses：使用 class 标签，而不是内嵌的内联样式 

  
# 这里的参数会被代码以 .Site.Params 的形式读取
params:
  env: production # to enable google analytics, opengraph, twitter-cards and schema.
  description: "于广阔无垠的宇宙里，留下有限的痕迹"
  defaultTheme: light
  #defaultTheme: dark
  #defaultTheme: auto
  #disableThemeToggle: true  #defalt false
  
  # use Home-Info Mode
  # homeInfoParams:
  #   Title: Home Info Title
  #   Content: home info content, about me river

  profileMode: #Profile Mode Config
    enabled: true
    title: "Hi 👋 Welcome to River's BLOG"
    subtitle: ""
    imageUrl: "icons/pixel-blue.JPG"
    imageTitle: "Cat"  #title of image alt
    imageWidth: 150
    imageHeight: 150
    buttons:
     - name: "Tech"
       url: "posts/tech"
     - name: "Read"
       url: "posts/read"
     - name: "Life"
       url: "posts/life"

  socialIcons:
    - name: github
      url: "https://github.com/nju-zzp"
    - name: bilibili
      url: "https://space.bilibili.com/21401435"
    - name: qq
      url: "/images/my-qq.JPG"
    - name: wechat
      url: "/images/my-wechat.JPG"
    - name: email
      url: "mailto:1311237665@qq.com"

  ShowBreadCrumbs: true
  ShowShareButtons: true
  ShowPostNavLinks: true
  ShowCodeCopyButtons: true
  hideFooter: false # 隐藏页脚
  showtoc: true
  tocopen: false
  VisitCount: true
  ShowWordCounts: true
  ShowLastMod: true #显示文章更新时间

  comments: true

  label:
    text: "River's BLOG"
    icon: "icons/pixel-blue.JPG"
    iconheight: 35

  assets:
    favicon: "icons/pixel-blue.JPG"

  #Customizing Fusejs Options for search functionality
  fuseOpts:
    isCaseSensitive: false
    shouldSort: true
    location: 0
    distance: 1000
    threshold: 0.4
    minMatchCharLength: 0
    keys: ["title", "permalink", "summary", "content"]

outputs:
  home:
    - HTML
    - RSS
    - JSON
```


## 3 目录与文件设置

### 3.1 hugo目录解释

官方文档：

```
example/
├── archetypes/          # `hugo new 文件` 时文件的预定义的front matter
│   └── default.md
├── assets/              # Stores all the files which need be processed by Hugo Pipes（CSS、JS）
├── content/             # All content for your website will live inside this directory
├── data/
├── layouts/             # how views of your content will be rendered into a static website.
├── public/
├── static/              # Stores all the static content: images, CSS, JavaScript, etc. 
├── themes/
└── config.toml
```

### 3.2 content下的目录与文件

**关于content要点：**

- content下每个目录/文件都是一个bundle，branch bundle下有`_index.md`，leaf bundle是单独的md文件或含有`index.md`的目录
- branch bundle以list结构呈现，leaf bundle表示一个具体内容页面
- content里若目录作为一个文件夹而非页面文件，则添加一个 _index.md文件，该文件里面可以加 Front Matter用来控制标题或其它的展示
- url遵循以content为root的path，生成的页面对应的url为配置文件里的`baseURL` + `文件url`(相对`content`)

**我的content目录**：

```
|--content
   |--about.md
   |--search.md
   |--archives.md
   |--links.md
   |--posts
      |--_index.md
      |--tech
         |--ML
         |--DB
         |--AL
         |--SE
      |--read
      |--life
```

**branch bundle的`_index.md`的 front matter 示例：**

```yml
---
title: "Essays"
description: "记录下技术、阅读、生活的每个脚步"
hidemeta: true # 是否隐藏文章的元信息，如发布日期、作者等                                                       
---
```

输入`hugo new 文章名称.md`就会在`content`目录下生成 “文章名称.md” 名字的文件，所有文章都是放在`content`这个文件夹里

如果自己还定义了分类目录，如在content目录的posts目录下有blog、read、tech、life等文章分类，那么在用命令生成文章的时候，如果要把文章生成到指定目录，可以用命令：`hugo new posts/tech/文章名称.md`，这样就会把文章生成到tech目录下

### 3.3 archetypes设置默认front matter

生成的文章内部头部配置信息包括一些文章名称，时间之类的信息，可以事先在目录`archetypes/default.md`下使用模板，这样在用命令`hugo new`生成文章后会自动加上模板里的配置

**我的`archetypes/posts.md`模板如下**:

```yml
---
title: "{{ replace .Name "-" " " | title }}" #标题
date: {{ .Date }} #创建时间
lastmod: {{ .Date }} #更新时间
author: ["River"] #作者
keywords: #关键词
-
categories: #类别
- 
tags: #标签
-
description: "" #描述
weight: # 输入1可以顶置文章，用来给文章展示排序，不填就默认按时间排序
slug: ""
draft: true # 是否为草稿
comments: true #是否展示评论
showToc: true # 显示目录
TocOpen: true # 自动展开目录
hidemeta: false # 是否隐藏文章的元信息，如发布日期、作者等
disableShare: true # 底部不显示分享栏
showbreadcrumbs: true #顶部显示当前路径
cover:
    image: "" #图片路径：posts/tech/文章1/picture.png
    zoom: # 图片大小，例如填写 50% 表示原图像的一半大小
    caption: "" #图片底部描述
    alt: ""
    relative: false
    
reward: true # 打赏
---
```

### 3.4 使用本地图片

**在文章里面使用本地图片的方式**： 

- 放在页面md文档同名目录下：

  - 比如我在`content/posts/tech/`下有一篇文章叫做 `golang_base.md`，那么在和该文章同一目录下新建一个文件夹叫做 `golang_base`，里面放图片如叫做 `picture.png` 的图片，那么在`golang_base.md`里面调用的时候可以这样写:` ![](picture.png) `

- 放在`static/`下，作为全局global的静态资源，可以直接访问：

  - 比如`static/image/item0.png`，可以直接引用`url: /image/item0.png`
  
  

## 4 优化：侧边目录

### 4.1 修改TOC代码

首先找到目录 `layouts/partials/toc.html` ，把之前的代码换成如下代码

```html
{{- $headers := findRE "<h[1-6].*?>(.|\n])+?</h[1-6]>" .Content -}}
{{- $has_headers := ge (len $headers) 1 -}}
{{- if $has_headers -}}
<aside id="toc-container" class="toc-container wide">
    <div class="toc">
        <details {{if (.Param "TocOpen") }} open{{ end }}>
            <summary accesskey="c" title="(Alt + C)">
                <span class="details">{{- i18n "toc" | default "Table of Contents" }}</span>
            </summary>

            <div class="inner">
                {{- $largest := 6 -}}
                {{- range $headers -}}
                {{- $headerLevel := index (findRE "[1-6]" . 1) 0 -}}
                {{- $headerLevel := len (seq $headerLevel) -}}
                {{- if lt $headerLevel $largest -}}
                {{- $largest = $headerLevel -}}
                {{- end -}}
                {{- end -}}

                {{- $firstHeaderLevel := len (seq (index (findRE "[1-6]" (index $headers 0) 1) 0)) -}}

                {{- $.Scratch.Set "bareul" slice -}}
                <ul>
                    {{- range seq (sub $firstHeaderLevel $largest) -}}
                    <ul>
                        {{- $.Scratch.Add "bareul" (sub (add $largest .) 1) -}}
                        {{- end -}}
                        {{- range $i, $header := $headers -}}
                        {{- $headerLevel := index (findRE "[1-6]" . 1) 0 -}}
                        {{- $headerLevel := len (seq $headerLevel) -}}

                        {{/* get id="xyz" */}}
                        {{- $id := index (findRE "(id=\"(.*?)\")" $header 9) 0 }}

                        {{- /* strip id="" to leave xyz, no way to get regex capturing groups in hugo */ -}}
                        {{- $cleanedID := replace (replace $id "id=\"" "") "\"" "" }}
                        {{- $header := replaceRE "<h[1-6].*?>((.|\n])+?)</h[1-6]>" "$1" $header -}}

                        {{- if ne $i 0 -}}
                        {{- $prevHeaderLevel := index (findRE "[1-6]" (index $headers (sub $i 1)) 1) 0 -}}
                        {{- $prevHeaderLevel := len (seq $prevHeaderLevel) -}}
                        {{- if gt $headerLevel $prevHeaderLevel -}}
                        {{- range seq $prevHeaderLevel (sub $headerLevel 1) -}}
                        <ul>
                            {{/* the first should not be recorded */}}
                            {{- if ne $prevHeaderLevel . -}}
                            {{- $.Scratch.Add "bareul" . -}}
                            {{- end -}}
                            {{- end -}}
                            {{- else -}}
                            </li>
                            {{- if lt $headerLevel $prevHeaderLevel -}}
                            {{- range seq (sub $prevHeaderLevel 1) -1 $headerLevel -}}
                            {{- if in ($.Scratch.Get "bareul") . -}}
                        </ul>
                        {{/* manually do pop item */}}
                        {{- $tmp := $.Scratch.Get "bareul" -}}
                        {{- $.Scratch.Delete "bareul" -}}
                        {{- $.Scratch.Set "bareul" slice}}
                        {{- range seq (sub (len $tmp) 1) -}}
                        {{- $.Scratch.Add "bareul" (index $tmp (sub . 1)) -}}
                        {{- end -}}
                        {{- else -}}
                    </ul>
                    </li>
                    {{- end -}}
                    {{- end -}}
                    {{- end -}}
                    {{- end }}
                    <li>
                        <a href="#{{- $cleanedID -}}" aria-label="{{- $header | plainify -}}">{{- $header | safeHTML -}}</a>
                        {{- else }}
                    <li>
                        <a href="#{{- $cleanedID -}}" aria-label="{{- $header | plainify -}}">{{- $header | safeHTML -}}</a>
                        {{- end -}}
                        {{- end -}}
                        <!-- {{- $firstHeaderLevel := len (seq (index (findRE "[1-6]" (index $headers 0) 1) 0)) -}} -->
                        {{- $firstHeaderLevel := $largest }}
                        {{- $lastHeaderLevel := len (seq (index (findRE "[1-6]" (index $headers (sub (len $headers) 1)) 1) 0)) }}
                    </li>
                    {{- range seq (sub $lastHeaderLevel $firstHeaderLevel) -}}
                    {{- if in ($.Scratch.Get "bareul") (add . $firstHeaderLevel) }}
                </ul>
                {{- else }}
                </ul>
                </li>
                {{- end -}}
                {{- end }}
                </ul>
            </div>
        </details>
    </div>
</aside>
<script>
    let activeElement;
    let elements;
    window.addEventListener('DOMContentLoaded', function (event) {
        checkTocPosition();

        elements = document.querySelectorAll('h1[id],h2[id],h3[id],h4[id],h5[id],h6[id]');
         // Make the first header active
         activeElement = elements[0];
         const id = encodeURI(activeElement.getAttribute('id')).toLowerCase();
         document.querySelector(`.inner ul li a[href="#${id}"]`).classList.add('active');
     }, false);

    window.addEventListener('resize', function(event) {
        checkTocPosition();
    }, false);

    window.addEventListener('scroll', () => {
        // Check if there is an object in the top half of the screen or keep the last item active
        activeElement = Array.from(elements).find((element) => {
            if ((getOffsetTop(element) - window.pageYOffset) > 0 && 
                (getOffsetTop(element) - window.pageYOffset) < window.innerHeight/2) {
                return element;
            }
        }) || activeElement

        elements.forEach(element => {
             const id = encodeURI(element.getAttribute('id')).toLowerCase();
             if (element === activeElement){
                 document.querySelector(`.inner ul li a[href="#${id}"]`).classList.add('active');
             } else {
                 document.querySelector(`.inner ul li a[href="#${id}"]`).classList.remove('active');
             }
         })
     }, false);

    const main = parseInt(getComputedStyle(document.body).getPropertyValue('--article-width'), 10);
    const toc = parseInt(getComputedStyle(document.body).getPropertyValue('--toc-width'), 10);
    const gap = parseInt(getComputedStyle(document.body).getPropertyValue('--gap'), 10);

    function checkTocPosition() {
        const width = document.body.scrollWidth;

        if (width - main - (toc * 2) - (gap * 4) > 0) {
            document.getElementById("toc-container").classList.add("wide");
        } else {
            document.getElementById("toc-container").classList.remove("wide");
        }
    }

    function getOffsetTop(element) {
        if (!element.getClientRects().length) {
            return 0;
        }
        let rect = element.getBoundingClientRect();
        let win = element.ownerDocument.defaultView;
        return rect.top + win.pageYOffset;   
    }
</script>
{{- end }}

```

### 4.2 修改CSS

`assets\css\extended`底下新建一個`.css`文件，命名為`toc-left.css`

```css
:root {
    --nav-width: 1380px;
    --article-width: 650px;
    --toc-width: 300px;
}

.toc {
    margin: 0 2px 40px 2px;
    border: 1px solid var(--border);
    background: var(--entry);
    border-radius: var(--radius);
    padding: 0.4em;
}

.toc-container.wide {
    position: absolute;
    height: 100%;
    border-right: 1px solid var(--border);
    left: calc((var(--toc-width) + var(--gap)) * -1);
    top: calc(var(--gap) * 2);
    width: var(--toc-width);
}

.wide .toc {
    position: sticky;
    top: var(--gap);
    border: unset;
    background: unset;
    border-radius: unset;
    width: 100%;
    margin: 0 2px 40px 2px;
}

.toc details summary {
    cursor: zoom-in;
    margin-inline-start: 20px;
    padding: 12px 0;
}

.toc details[open] summary {
    font-weight: 500;
}

.toc-container.wide .toc .inner {
    margin: 0;
}

.active {
    font-size: 110%;
    font-weight: 600;
}

.toc ul {
    list-style-type: circle;
}

.toc .inner {
    margin: 0 0 0 20px;
    padding: 0px 15px 15px 20px;
    font-size: 16px;

    /*目录显示高度*/
    max-height: 83vh;
    overflow-y: auto;
}

.toc .inner::-webkit-scrollbar-thumb {  /*滚动条*/
    background: var(--border);
    border: 7px solid var(--theme);
    border-radius: var(--radius);
}

.toc li ul {
    margin-inline-start: calc(var(--gap) * 0.5);
    list-style-type: none;
}

.toc li {
    list-style: none;
    font-size: 0.95rem;
    padding-bottom: 5px;
}

.toc li a:hover {
    color: var(--secondary);
}

```

## 5 优化：文章封面缩小移到侧边

### 5.1 添加class标识

定位到目录 `layouts/_default/list.html`，添加一个class标识，命名为post-info，把entry-header和entry-content和entry-footer都包裹进去，如下代码

```html
<div class="post-info">  <!--新添加的行-->
    <header class="entry-header">
        <h2>
            {{- .Title }}
            {{- if .Draft }}<sup><span class="entry-isdraft">&nbsp;&nbsp;[draft]</span></sup>{{- end }}
        </h2>
    </header>
    {{- if (ne (.Param "hideSummary") true) }}
    <section class="entry-content">
        <p>{{ .Summary | plainify | htmlUnescape }}{{ if .Truncated }}...{{ end }}</p>
    </section>
    {{- end }}
    {{- if not (.Param "hideMeta") }}
    <footer class="entry-footer">
        {{- partial "post_meta.html" . -}}
    </footer>
    {{- end }}
</div>  <!--新添加的行-->

<!--
下面这两行本来是在<article class="{{ $class }}">和<header class="entry-header">之间的，
但是我喜欢把图片展示在右边，所以把这两行代码移了下来
-->
{{- $isHidden := (.Site.Params.cover.hidden | default .Site.Params.cover.hiddenInList) }}
{{- partial "cover.html" (dict "cxt" . "IsHome" true "isHidden" $isHidden) }}
```

### 5.2 修改CSS

在`\assets\css\extended`底下新建一個.css文件，命名為`image-right.css`

```css
.post-entry {
    display: flex;
    flex-direction: row;
    align-items: center;
}

.entry-cover {
    overflow: hidden;
    padding-left: 18px;
    height: 100%;
    width: 50%;
    margin-bottom: unset;
}

.post-info {
    display: inline-block;
    overflow: hidden;
    width: 90%;
}
```

### 5.3 解决冲突

为什么会说有冲突呢，因为上面对文章封面图片进行了调整，会影响到文章里面顶部的图片展示，所以我从其他人博客找到解决方案：

1. 定位到目录 `layouts/partials/cover.html`，在相同目录下创建一个名字为`cover1.html`的文件，并把`cover.html`里的文件复制一份一样的到`cover1.html`下
2. 把`cover1.html`文件里的`<figure class="entry-cover">`修改为`<figure class="entry-cover1">`
3. 定位到 `layouts/_default/single.html`，把`{{- partial "cover.html" (dict "cxt" . "IsHome" false "isHidden" $isHidden) }}`改为`{{- partial "cover1.html" (dict "cxt" . "IsHome" false "isHidden" $isHidden) }}`

这样文章封面调用的是经过修改的图片，而文章里面顶部的图片调用的是未经修改的图片

## 6 优化：添加友链

### 6.1 友链 HTML 代码

首先在`layouts/shortcodes`目录下添加一个html文件，命名为`friend.html`，在里面添加如下代码

```html
{{- if .IsNamedParams -}}
<a target="_blank" href={{ .Get "url" }} title={{ .Get "name" }} class="friendurl">
  <div class="frienddiv">
    <div class="frienddivleft">
      <img class="myfriend" src={{ .Get "logo" }} />
    </div>
    <div class="frienddivright">
      <div class="friendname">{{- .Get "name" -}}</div>
      <div class="friendinfo">{{- .Get "word" -}}</div>
    </div>
  </div>
</a>
{{- end }}
```

### 6.2 友链 CSS 代码

然后在`assets/extended/friend.css`文件里添加如下css代码

```css
.friendurl {
    text-decoration: none !important;
    color: black;
    box-shadow: none !important;
}

.myfriend {
    width: 56px !important;
    height: 56px !important;
    border-radius: 50%!important;
    padding: 2px;
    margin-top: 20px !important;
    margin-left: 14px !important;
    background-color: #fff;
}

.frienddiv {
    overflow: auto;
    height: 100px;
    width: 49%;
    display: inline-block !important;
    border-radius: 5px;
    background: none;
    
    -webkit-transition: all ease-out 0.3s;
    -moz-transition: all ease-out 0.3s;
    -o-transition: all ease-out 0.3s;
    transition: all ease-out 0.3s;
}

.dark .frienddiv:hover {
    background: var(--code-bg);
}

.frienddiv:hover {
    background: var(--theme);
    transition: transform 1s;
    webkit-transform: scale(1.1);
    -moz-transform: scale(1.2);
    -ms-transform: scale(1.2);
    -o-transform: scale(1.2);
    transform: scale(1.1);
}

.frienddiv:hover .frienddivleft img { 
    transition: 0.9s !important;
    -webkit-transition: 0.9s !important;
    -moz-transition: 0.9s !important;
    -o-transition: 0.9s !important;
    -ms-transition: 0.9s !important;
    transform: rotate(360deg) !important;
    -webkit-transform: rotate(360deg) !important;
    -moz-transform: rotate(360deg) !important;
    -o-transform: rotate(360deg) !important;
    -ms-transform: rotate(360deg) !important;
}

.frienddivleft {
    width: 92px;
    float: left;
    margin-right: -5px;
}

.frienddivright {
    margin-top: 18px;
    margin-right: 18px;
}

.friendname {
    text-overflow: ellipsis;
    font-size: 100%;
    margin-bottom: 5px;
    color: var(--primary);
}

.friendinfo {
    text-overflow: ellipsis;
    font-size: 70%;
    color: var(--primary);
}

@media screen and (max-width: 600px) {
    .friendinfo {
        display: none;
    }
    .frienddivleft {
        width: 84px;
        margin: auto;
    }
    .frienddivright {
        height: 100%;
        margin: auto;
        display: flex;
        align-items: center;
        justify-content: center;
    }
    .friendname {
        font-size: 18px;
    }
}
```

### 6.3 填写友链

在要放友链的文件里输入下例的使用方法，比如我放在`content/friends.md`里（因为会自动感应效果，无法用代码贴出来，所以用图片代替）

```html
---
title: "🥰 Friends Links"
layout: links
date: 2023-03-08T13:41:48+08:00
draft: false
hidemeta: true
showtoc: false
showbreadcrumbs: false
disableShare: true
---

<div class="friend">

{{< friend name="FYL's BLOG" url="https://www.baidu.com" logo="/icons/pixel-fox.JPG" word="My roommate's home page~" >}}

</div>
```

## 7 优化：添加基于Twikoo评论功能

- Hugo博客搭建twikoo评论，并配置邮件提醒功能，基于PaperMod主题

- [Twikoo官方文档](https://twikoo.js.org/)

### 7.1 采用Vercel云函数部署Twikoo

我的步骤：

1. 申请 MongoDB 账号

2. 创建免费 MongoDB 数据库，区域推荐选择 `AWS / N. Virginia (us-east-1)`或HongKong

3. 创建user，包括用户名和密码

4. 设置网络允许所有 IP 地址的连接 `0.0.0.0`

5. 在 Clusters 页面点击 CONNECT，记录数据库连接字符串，请将连接字符串中的 `<password>` 修改为数据库密码

   ```
   mongodb+srv://river:<password>@cluster0.qggbywf.mongodb.net/?retryWrites=true&w=majority
   ```

6. 创建并登录 Vercel 帐号

7. 回到[Twikoo文档](https://twikoo.js.org/quick-start.html#vercel-%E9%83%A8%E7%BD%B2)，点击蓝色按钮一键部署

8. 连接github，自动创建project并deploy

9. 进入dashboard中的 Settings - Environment Variables，添加环境变量 `MONGODB_URI`，值为第 5 步的数据库连接字符串

10. 进入Settings - Function，在region设置中与第 2 步中的同步

11. 进入 Deployments , 然后在任意一项后面点击更多（三个点） , 然后点击Redeploy , 最后点击下面的Redeploy

12. 进入 Overview，点击 Domains 下方的链接，如果环境配置正确，可以看到 “Twikoo 云函数运行正常” 的提示

13. Vercel Domains（包含 `https://` 前缀，例如 `https://xxx.vercel.app`）即为您的环境 id

### 7.2 Hugo 配置

Hugo的 PaperMod 主题添加 twikoo 代码的位置：`layouts/partials/comments.html`

```html
<!-- Twikoo -->
<div>
    <div class="pagination__title">
        <span class="pagination__title-h" style="font-size: 20px;">💬评论</span>
        <hr />
    </div>
    <div id="tcomment"></div>
    <script src="https://cdn.staticfile.org/twikoo/{{ .Site.Params.twikoo.version }}/twikoo.all.min.js"></script>
    <script>
        twikoo.init({
            envId: "https://twikoo-for-river-blog.vercel.app",  //腾讯云环境填 envId；Vercel 环境填地址（https://xxx.vercel.app）
            el: "#tcomment",
            lang: 'zh-CN',
            region: '',  //环境地域，默认为 ap-shanghai，腾讯云环境填 ap-shanghai 或 ap-guangzhou；Vercel 环境不填
            path: window.TWIKOO_MAGIC_PATH||window.location.pathname,
        });
    </script>
</div>
```

调用上述twikoo代码的位置：`layouts/_default/single.html`

```html
<article class="post-single">

  // 这里是默认的其他代码
  
  // twikoo，一般只需要复制以下3行代码，加上其他代码是为了帮助读者确认代码添加的位置
  {{- if (.Param "comments") }}
    {{- partial "comments.html" . }}
  {{- end }}
</article>
```

在站点配置文件`config.yml`的`params`中加上如下代码，版本号自己去twikoo的github看最新的版本

```yml
params:
	twikoo:
      version: 1.6.10 # 这个版本号要自己手动修改，和twikoo的版本号要对得上
```



## 8 优化：Mac风格的code block

在`assets\css\extended\`底下新建一個.css文件，命名為`mac-code-blocks.css`

```css
pre {
    position: relative;
    padding: 30px 10px 10px 10px;
}

.post-content pre code {
	font-family: Consolas;
}

pre::after {
    display: block;
    content: " ";
    position: absolute;
    border-radius: 50%;
    background: #ff5f56;
    width: 12px;
    height: 12px;
    top: 0;
    left: 12px;
    margin-top: 12px;
    -webkit-box-shadow: 20px 0 #ffbd2e, 40px 0 #27c93f;
    box-shadow: 20px 0 #ffbd2e, 40px 0 #27c93f;
}

```

## 9 优化：自定义字体与emoji

### 13.1 下载字体资源

去网上找到字体资源，即`ttf`结尾的文件，放到`static/fonts/`文件夹下

### 13.2 引用字体

在`assets/css/extended/fonts.css`文件中引用字体如下图

```css
@font-face {
    font-family: "font_name";
    src: url("/fonts/font_name.ttf") format("truetype");
}
```

![img](https://zzp-note.oss-cn-hangzhou.aliyuncs.com/image/2.png)

### 13.3 使用字体样式

比如我想要修改文章内的文字样式，找到对应的的class名称或id名称，然后用font-family这个属性指定使用什么字体，下图使用的是我们刚才指定的字体，第一个是中文字体，只对中文起效果，第二个是英文字体，只对英文起效果，第三个是默认字体，读取顺序优先从左到右读

![img](https://zzp-note.oss-cn-hangzhou.aliyuncs.com/image/3.png)

## 10 部署：部署至云服务器

`hugo`指令生成`public`文件夹

打包发送到云服务器

`nginx`部署该静态文件夹

## 11 部署：部署至Github Page

参考 [部署hugo静态文件到 github page 官方文档](https://gohugo.io/hosting-and-deployment/hosting-on-github/)

1. 创建github仓库

2. [本地仓库上传到github](https://docs.github.com/zh/get-started/importing-your-projects-to-github/importing-source-code-to-github/adding-locally-hosted-code-to-github)

3. From the main menu choose **`Settings > Pages`**. In then center of your screen you will see `Build and deployment`

4. Change the **`Source`** to `GitHub Actions`. The change is immediate; you do not have to press a Save button

5. Create an empty file in your local repository: `.github/workflows/hugo.yaml`

6. Copy and paste the YAML below into the file you created. Change the branch name and Hugo version as needed.

   ```yml
   # Sample workflow for building and deploying a Hugo site to GitHub Pages
   name: Deploy Hugo site to Pages
   
   on:
     # Runs on pushes targeting the default branch
     push:
       branches:
         - main
   
     # Allows you to run this workflow manually from the Actions tab
     workflow_dispatch:
   
   # Sets permissions of the GITHUB_TOKEN to allow deployment to GitHub Pages
   permissions:
     contents: read
     pages: write
     id-token: write
   
   # Allow one concurrent deployment
   concurrency:
     group: "pages"
     cancel-in-progress: true
   
   # Default to bash
   defaults:
     run:
       shell: bash
   
   jobs:
     # Build job
     build:
       runs-on: ubuntu-latest
       env:
         HUGO_VERSION: 0.111.2
       steps:
         - name: Install Hugo CLI
           run: |
             wget -O ${{ runner.temp }}/hugo.deb https://github.com/gohugoio/hugo/releases/download/v${HUGO_VERSION}/hugo_extended_${HUGO_VERSION}_linux-amd64.deb \
             && sudo dpkg -i ${{ runner.temp }}/hugo.deb          
         - name: Install Dart Sass Embedded
           run: sudo snap install dart-sass-embedded
         - name: Checkout
           uses: actions/checkout@v3
           with:
             submodules: recursive
             fetch-depth: 0
         - name: Setup Pages
           id: pages
           uses: actions/configure-pages@v3
         - name: Install Node.js dependencies
           run: "[[ -f package-lock.json || -f npm-shrinkwrap.json ]] && npm ci || true"
         - name: Build with Hugo
           env:
             # For maximum backward compatibility with Hugo modules
             HUGO_ENVIRONMENT: production
             HUGO_ENV: production
           run: |
             hugo \
               --gc \
               --minify \
               --baseURL "${{ steps.pages.outputs.base_url }}/"          
         - name: Upload artifact
           uses: actions/upload-pages-artifact@v1
           with:
             path: ./public
   
     # Deployment job
     deploy:
       environment:
         name: github-pages
         url: ${{ steps.deployment.outputs.page_url }}
       runs-on: ubuntu-latest
       needs: build
       steps:
         - name: Deploy to GitHub Pages
           id: deployment
           uses: actions/deploy-pages@v1
   ```

7. Commit the change to your local repository with a commit message of something like “Add workflow”, and push to GitHub.

8. From GitHub’s main menu, choose **Actions**. Under the deploy step, you will see a link to your live site.

9. 修改为自己的域名：DNS server 添加负载均衡的记录，CNAME为github.io

10. 在github的仓库里，`settings - pages`里添加`custom domain`为自己的域名

## 12 部署：部署至Vercel（推荐）

[vercel 官方网站](https://vercel.com/)

### 12.1 前置注意要点

1. 源代码需要发布到Github上。
2. public目录不能发布。（添加到 .gitignore上）
3. 主题如果是从github下载下来的，子目录里面会有 `.git` 文件，发布代码的话，内层目录是不能有`.git`的，它会识别成另一个git仓库并忽略上传，只上传一个软链接文件。所以需要删除themes主题下面的`.git`文件，否则 Vercel 无法从 github上拉取到主题，编译会失败。

### 12.2 部署步骤

1. 创建github仓库

2. [本地仓库上传到github](https://docs.github.com/zh/get-started/importing-your-projects-to-github/importing-source-code-to-github/adding-locally-hosted-code-to-github)（不能含有public目录，子目录比如`theme`下删除`.git`文件

3. 登录[vercel](https://vercel.com/)，导入github对应仓仓库作为project

4. FRAMEWORK PRESET 自动会识别为Hugo（或者手动设置）

5. Build Command 是hugo

6. Output directory是public

7. **（一定要填）Environment Variables 填写hugo版本。不然会默认使用很旧的版本。**

   <img src="https://zzp-note.oss-cn-hangzhou.aliyuncs.com/image/image-20230310013307737.png" alt="image-20230310013307737" style="zoom:50%;" />

8. 添加自己的自定义domain至vercel项目

9. 根据第8步，vercel会给出提示，在DNS server处添加解析规则CNAME到vercel的解析器
