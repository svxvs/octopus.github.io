---
layout: post
title:  "在配置jekyll过程中遇到的问题!"
date:   2016-10-27 8:34:03 +0800
categories: jekyll update
---

&#160; &#160; &#160; &#160;在昨天在Windows中配置`jekyll`的过程中出现了一些问题，在此写篇博客以作总结 。

&#160; &#160; &#160; &#160;通常我们可以用 `jekyll serve` 命令来启动一个基于jekyll的 web服务以便于本地预览代码效果以及代码的调试。

&#160; &#160; &#160; &#160;但是在昨天，我身为一个新手按照在 [Setting up your GitHub Pages site locally with Jekyll][Setting up your GitHub Pages site locally with Jekyll] 页面的教程进行了配置，并启动了[step4][step4]中的捆扎器系列命令 `bundle exec jekyll serve` 。

&#160; &#160; &#160; &#160;然后，我开始面临一系列的问题：

>&#160; &#160; &#160; &#160;首先，我的这个文件夹放在了桌面，因为我只是想测试一下[GitHub Pages][GitHub Pages]的一系列流程，现在 GitHub Pages 的流程测试完了，我就想把这个文件夹删除，然后正式的在一个我希望的位置来重新开始一个稳定的流程，这时问题出现了，这个桌面上的文件夹无法删除，因为好像什么进程占用了它，而我也不知如何关闭它。

>&#160; &#160; &#160; &#160;那么好吧，我暂时抛却了这个问题不管，去我希望的位置重新开始了一个 `jekyll` ，一切良好，GitHub 线上的内容可以良好的运行。

>&#160; &#160; &#160; &#160;但是，等等。`jekyll` 干什么用来着？我细想了一下，好像 `jekyll` 的主要功能之一就是可以在本地浏览器预览效果吧，于是我在本地直接访问`localhost:4000`，结果，不对呀，怎么不是我当前编辑内容所在的博客？

>&#160; &#160; &#160; &#160;经过一番查找，发现其中一个问题在于， `jekyll serve` 并无法进行服务覆盖，及在第一个服务启动的情况下，第二个 `jekyll serve` 无法覆盖第一个 `jekyll serve` 生成的进程，由此导致的情况就是 `localhost:4000`  默认访问第一个 `jekyll serve` 生成的进程，于是我在第二个位生成和启动的服务就不知如何访问。

>&#160; &#160; &#160; &#160;另一个问题则是教程 [step3][step3] (请前往链接内容查看详情)会修改一些配置，而这些也会导致一些我不知道的配置变化。由此会导致 `jekyll serve` 命令无法识别；而之前我进行第一次尝试操作时，用的并不是 `jekyll serve` 命令，而是[step4][step4]中的捆扎器系列命令 `bundle exec jekyll serve` 

>&#160; &#160; &#160; &#160;好吧，我开始着手解决这些问题，我先在任务管理器关闭了所有能看见的有关于`jekyll`的进程，然后直接删除第一个文件夹，去我的第二个文件夹（我希望的位置）去打算使用 `jekyll serve` 来启动本地服务，结果启动失败了，期间提示给我一个 bug 说是版本问题，然后我复制该提示去 Google 寻找了很多解决答案，去逐一试验。最终终于使本地实现了预览功能，其试验的过程如下：


>使用 <br/>
$`gem cleanup lumberjack`或 <br/>
$`gem list lumberjack`或 <br/>
$`gem uninstall`或 <br/>
$`bundle exec guard`或 <br/>
$`bundle exec rspec` <br/>
这几个命令用来清除因 jekyll 多版本而产生的问题； <br/>
这几个命令来自于 stackoverflow ，链接是[Unresolved specs during Gem::Specification.reset:][Unresolved specs during Gem::Specification.reset:]



>&#160; &#160; &#160; &#160;真正解决我的问题是下一个命令（[next answer][next answer]和上一部分来自同一页面），这个方法虽然很笨，但的确解决了我的问题：

>&#160; &#160; &#160; &#160;`bundle clean --force` 这个命令帮我清除了一系列的相关关系，然后我用`bundle exec jekyll new . --force`重新安装了 jekyll，这使得`bundle exec jekyll serve`得以成功启动本地的预览服务，现在我终于实现了在本地用 `localhost:4000` 地址来访问我的博客框架



#### **前往 [Jekyll docs][jekyll-docs] 可以得到更多帮助。更多bug和特性需求，可以前往这个链接 [Jekyll’s GitHub repo][jekyll-gh]。 如果你有疑问可以在这里和大家一起讨论： [Jekyll Talk][jekyll-talk]。** 



[next answer]: http://stackoverflow.com/a/27813313
[Unresolved specs during Gem::Specification.reset:]:  http://stackoverflow.com/a/18127613
[GitHub Pages]: https://pages.github.com/
[Setting up your GitHub Pages site locally with Jekyll]: https://help.github.com/articles/setting-up-your-github-pages-site-locally-with-jekyll/
[step3]: https://help.github.com/articles/setting-up-your-github-pages-site-locally-with-jekyll/#step-3-optional-generate-jekyll-site-files
[step4]: https://help.github.com/articles/setting-up-your-github-pages-site-locally-with-jekyll/#step-4-build-your-local-jekyll-site
[jekyll-docs]: http://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/
