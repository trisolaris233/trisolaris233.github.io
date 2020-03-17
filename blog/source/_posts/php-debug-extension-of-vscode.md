---
title: linux下vscode中的php debug插件
tags:
  - vscode
  - php
  - linux
abbrlink: 19517
date: 2019-02-10 22:20:32
---

`vscode`的`php debug`插件简直是巨坑！

从昨天晚上开始一直整这个插件，中途几经放弃， 现在终于可以使用了(~~太感动了~~).. 主要原因可以归结为: ~~对`linux`不熟悉~~**太菜**。

而且关于这个插件百度上的资料真的很少，大多都是蜻蜓点水，让人看了一头雾水不知所云。 我这次整好后更坚定了一个信念： **具体问题具体分析**。有时候网上找的内容不一定符合你的实际情况， 如果生搬硬套结果肯定是自找苦吃， 所以我希望遇到问题先问`why?`，为什么会出现这个问题？ 仔细的考虑过这个问题后， 对问题的解决是非常有帮助的。

## Php Debug介绍
说穿了就是可以调试php代码的插件， 最好的介绍和安装方法就是官方说明：
{% asset_img php-debug-info.png %}

## Php Debug的原理
来一波倒叙，首先先介绍原理，这样可能会更容易理解一点，尤其是读完文章后再回头来看原理，其实还蛮好理解的！(就是支流问题特别多，linux上整需要一定的姿势水平，还是要学习一个)

首先， 你应该会在vscode里面打了几个断点然后启动debug吧， 这时候其实启动了`xdebug`的程序，这个程序按照默认配置会监听`127.0.0.1:9000`，他等待的是**php的连接**(我一开始就在浏览器中访问`localhost:9000`，结果啥也没发生，请求多了还会导致连接关闭)。
假设你的服务器的`DocumentRoot`设在`~/www`下。你正在vscode中调试的文件是`~/www/test/index.php`，这时候你在浏览器进入`localhost/test/index.php`，如果正确配置应该会使浏览器唤起`fastcgi`,`php-fpm`等进程工作并且最后执行php源代码的解析。解析时php会发现自己设置了`debug`选项(因为我们已经在`php.ini`文件中设置好了)，按照默认，就回去连接正在监听中的`xdebug`进程。 这样就完成了对接，于是`debug`就开始了。

### debug过程图解
平时都不画UML图的说... 这回来了一张《xdebug原理图解》
{%  asset_img  xdebug-process.png %}
(好久没用[processOn](https://www.processon.com)，我发现这玩意儿真是好用！！~~(之前尝试用gimp画图的 - -)~~


### 好了，我要怎么安装呢？
事实上， 对于上面的介绍有一点非常让我疑惑不解，就是这句话：`XDebug is a PHP extension (a .so file on Linux and a .dll on Windows) that needs to be installed on your server.`，这句话意思说`xdebug`不仅要为php安装还要为服务器安装，然而我自己编译的`apache2.4`编译时并未选择什么`xdebug`，之后也从未折腾(配置好php解析后)， 所以一直很疑惑。

后来， 我look up了`xdebug`的[官方文档](https://xdebug.org/docs/all)，官网在谈“installation”时候并未提到什么服务器，这里就更让人疑惑了， 为啥一边说需要装在服务器上，一边却只字不提？ 这时候我选择相信官方， 所以就走下面的步骤。

{% blockquote %}
根据我的经历来判断成功安装并且使用`php debug`的前提是先要有一个服务器， 我用的是`apache2.4`，想来`nginx`也应该是可以的。 还有就是一个正确安装的php， 版本在`7.0`-`7.4`之间为上。
{% endblockquote %}

插件官方说明云，我们应该先安装`XDebug`这个东西， 并进入后面提示的这个[XDebug installation wizard](https://xdebug.org/wizard.php)， 按照他的提示，我们把`phpinfo()`(等同于`php -i`的输出)复制到该网页上去， 网页会检测我们是否安装了`XDebug`并提供了一个友好的安装向导:
{% asset_img install-xdebug.png %}
按照上面的指示， 我们可以轻松的为php安装上`xdebug`。

{% blockquote %}
中间可能会出现的问题：执行`./configure`提示需要`php-config`的路径， 这个问题我也遇到了， 如果你是用包管理器安装的php则不会有这个问题。`php-config`的路径和`php`的可执行文件位于同一目录中， 具体取决于你编译时的安装位置。
{% endblockquote %}

装上`xdebug`后， 又回到了插件官方文档， 我们需要在`php.ini`文件中写入以下内容
```
[XDebug]
xdebug.remote_enable = 1
xdebug.remote_autostart = 1
```
很容易理解， 分别是打开远程debug和自动启动远程debug。

接下来的挺简单， 打几个断点，然后点击debug， 没有配置就先`添加`一个！ 然后会生出一个默认的， 按默认使用就可以！ 如果你硬要改`php.ini`文件中也得相应更改。

然后是进入浏览器， 进入你正在debug的文件的目录，就可以打中断点！ 当然，这一切都要求服务器设置得当（设置`DocumentRoot`）。

{% blockquote %}
中间遇到的权限不足稳定可以用`chmod`命令解决，一个http目录路径应该至少具备`711`的权限。
{% endblockquote %}

## debug界面和文件
网上博客都不约而同的旁边开着debug界面... 这让我很气愤， 因为只要给我看看他们的文件目录说不定就可以给我解决问题的方法！ debug界面只要有打中的断点就足以说明问题了(第四行),这里贴一张效果图。
{% asset_img  debug-ui-with-file-manager.png %}











