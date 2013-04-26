---
layout: post
title: "Octopress基本定制"
date: 2013-04-24 22:19
comments: true
categories: Octopress
---

Octopress的配置还是非常简单的，首先是建议简单阅读下官方文档[点我](http://octopress.org/docs/configuring/)。

（这块不得不说gedit这个gnome环境基本的编辑器做的还是相当不错的。竟然支持部分markdown语法高亮。。。）

所有的配置都包含在这4个文件中:
```
_config.yml       # Main config (Jekyll's settings) 主配置文件，网站相关的配置基本都在这个文件
Rakefile          # Configs for deployment 部署配置文件
config.rb         # Compass config 不懂。。。
config.ru         # Rack config 不懂。。。
```


<!-- more -->
总之你基本上只会用的第一个。然后看下里面基本的选项：
```
root:               # Mapping for relative urls (default: /) 相对路径的根路径位置
permalink:          # Permalink structure for blog posts  blog前缀规则
source:             # Directory for site source files blog存放的目录
destination:        # Directory for generated site files 站点文件路径
plugins:            # Directory for Jekyll plugins 插件路径
code_dir:           # Directory for code snippets (for include_code plugin) code路径
category_dir:       # Directory for generated blog category pages 目录页路径

pygments:           # Toggle python pygments syntax highlighting
paginate:           # Posts per page on the blog index
pagination_dir:     # Directory base for pagination URLs eg. /blog/page/2/
recent_posts:       # Number of recent posts to appear in the sidebar

default_asides:     # Configure what shows up in the sidebar and in what order
blog_index_asides:  # Optional sidebar config for blog index page
post_asides:        # Optional sidebar config for post layout
page_asides:        # Optional sidebar config for page layout

```
基本上不用动，了解一下。

然后是网站名等一些必改的配置项。
```
url: http://macrov.github.io
title: 网站title
subtitle: 次标题
author: 你的名字
simple_search: http://google.com/search
description: 简单描述
```

然后就是一些三方服务的嵌入了，如twiiter，G+等。这些也放到单独的文章中。

##增加about页面。

应该是Jekyll的规则，/blog/archives这样的链接就会链接到你网站http://your_username.github.com/blog/archives/index.html。
所以我们要添加一个about的界面就在source下建立一个about目录，然后在下面建立一个/about/index.html或者index.markdown文件。
然后再里面编辑我们需要的内容。

实际上不需要你手动去建立这些目录，直接执行
```
rake new_page["about"]
```
就会执行上面的操作。
然后编辑source/about/index.markdown文件。

最后是在导航栏加上我们的新页面，编辑source/_includes/custom/navigation.html文件
修改成如下样式：
```
<ul class="main-navigation">
  <li><a href="{{ root_url }}/">Blog</a></li>
  <li><a href="{{ root_url }}/blog/archives">Archives</a></li>
  <li><a href="{{ root_url }}/about">About Me</a></li>  //这行就是我们增加的链接
</ul>

```
然后rake generate, rake preview查看效果，okey后deploy就可以了。

##增加tag列表到侧边栏

增加文件plugin/category_list.rb如下：
```
module Jekyll
  class CategoryListTag < Liquid::Tag
    def render(context)
      html = ""
      categories = context.registers[:site].categories.keys
      categories.sort.each do |category|
        posts_in_category = context.registers[:site].categories[category].size
        category_dir = context.registers[:site].config['category_dir']
        category_url = File.join(category_dir, category.gsub(/_|\P{Word}/, '-').gsub(/-{2,}/, '-').downcase)
        html << "<li class='category'><a href='/#{category_url}/'>#{category} (#{posts_in_category})</a></li>\n"
      end
      html
    end
  end
end

Liquid::Template.register_tag('category_list', Jekyll::CategoryListTag)
```
增加文件source/_includes/asides/category_list.html，内容如下：
```
<section>
  <h1>Categories</h1>
  <ul id="categories">
    {% category_list %}
  </ul>
</section>
```
修改_config.yml文件，把我们新建的category_list.html加入到侧边栏:
```
default_asides: [asides/recent_posts.html, asides/category_list.html]
```
付:每篇文章的categories:后边跟tag名,如：
```
# One category
categories: Sass

# Multiple categories example 1
categories: [CSS3, Sass, Media Queries]

# Multiple categories example 2
categories:
- CSS3
- Sass
- Media Queries

```


##增加评论功能
Octporess默认使用的是disqus，一个国外的评论平台。貌似还可以植入广告给你赚点钱。
然后有人说这个国内访问比较问，有移植多问的，可以搜一下。
先去[disqus](http://disqus.com/)注册你的网站，记住shortname。
然后编辑_config.yml文件。
找到disqus配置然后输入你网站的shortname，然后把disqus_show_comment_count: 改成true。这样默认就开启评论了，不想默认开启评论就置成false，然后在文章的header加comment: true.
```
disqus_short_name: shortname
disqus_show_comment_count: true
```

##增加google analytics
Google Analytics可以分析你的网站数据，比如访问量等信息。

Octopress已经做好，支持，去网站上申请到track id后填到_config.yml文件中google_analytics_tracking_id一项后边就可以了.
Google Analytics[地址](www.google.com/analytics)


