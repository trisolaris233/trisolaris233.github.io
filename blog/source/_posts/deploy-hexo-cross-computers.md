---
title: hexo的备份和多台电脑操作
date: 2019-01-27 01:48:41
tags: 
    - hexo
    - git
---

最近换了系统，想在新的系统上继续编辑我的博客， 于是就出现了问题： 我发现我用hexo的deployer-git备份的文件都是渲染后的html文件， 而并没有我的原稿(.md)。这给我带来不少麻烦， 于是乎， 上网查询了一些资料， 找到了一些可以在多处编辑hexo博客的办法。

当用新电脑的时候除了要安装`node.js`和`npm`以外， 如果要使用hexo还要安装支持hexo运行的一大堆文件，这些文件我们把他们备份在github上， 如果你使用Github Pages部署你的网页那么可以放在你的Github Pages的repo的一个分支上， 其中保留`master`分支不受影响（放渲染后的文件供浏览）。


## 备份hexo
之后， 其实都是前人总结的经验了， 我在这里用自己的方法赘述一遍，操作之前，要确保你本地有一个**已经安装好的hexo**：
1. 在博客的repo上创建分支hexo， 这个分支用来备份hexo的运行环境文件， 并在repo的setting中将hexo分支设为默认分支。
2. 将博客克隆到本地: `git clone https://github.com/trisolaris233/trisolaris233.github.io`
3. 此时克隆下来了一个trisolaris233.github.io的文件夹， cd进去， 此时这个目录应该全是渲染后的文件， 这时候你把hexo文件夹下的东西都拖进去（没事， 都拖进去罢）。
4. 创建一个.gitignore文件（如果已经存在则对比文件内容）， 将于环境无关的文件/目录排除在外：
写入内容:
```
.DS_Store
Thumbs.db
db.json
*.log
node_modules/
public/
.deploy*/
```
5. 几乎全部都搞定了， 接下来只要在终端中执行上传的代码即可：
```git
git add .
git commit -m "hexo environment files."
git push origin hexo
```

以上我们就基本搞定了hexo文件的备份，这样hexo每次deploy的时候会把hexo渲染后的文件推送到repo的`master`分支， 这是hexo会自动完成的， 因此我们要对付的只有环境文件， 在你创建了新的post的时候或者添加了一些插件、主题的时候你都应该commit至hexo分支。

## 换电脑后
终于你换了一台电脑，你现在要做的就是在新电脑上安装`node.js`, `npm`, `git`。 安装完成之后，clone你的远程repo（因为默认已经设置成了hexo分支因此我们clone下来的自然也是hexo分支）。 cd到clone下的目录中去， 执行`npm install`即可。（注意： 这里需要遵守的一个规则就是用npm安装与hexo相关内容是要添加--save选项）

{% blockquote %}
Linux系统下如果出现安装有ERR的情况， 可以检查是否有用`sudo npm install`， 安装软件包时， sudo是必要的。

而且需要注意的是：`npm`的源的设定是对于当前用户的， 也就是说如果执行`sudo npm install ...`可能会非常慢！ 会出现看似卡着不动的现象。 对于这种可以建议开启`--verbose`选项，该命令可以查看具体进度，排坑必备。 不过我的建议是对`root`用户也执行一次换源: `sudo npm config set registry https://registry.npm.taobao.org `

{% endblockquote %}

`npm install -g`顺利执行后， 就可以使用hexo了（因为hexo也一并安装了）。

{% blockquote %}
#####2020/2/13按:
这样做的原理和前提是npm的执行目录下有packge.json文件。
由于之前已经安装过hexo，npm会把安装过的包(也就是hexo)记录到packge.json文件中。
而在某个目录下执行npm install就是安装当前目录中packge.json文件中记录的包，详情可以参考npm官方文档。

还有-g，这个g实际上是globally的缩写，如果不加g那么包就只会被装到当前目录下，加上-g就是全局安装，如果不加-g的话会无法使用的。就算在当前目录都不行
{% endblockquote %}




## 多台电脑同时编辑
有了以上操作之后，多边编辑同一个hexo的博客就不难实现。 每台参与编辑的电脑只要在编辑之前拉取最新的repo即可： `git pull`， 操作完后， 执行commit。 然后在另一台电脑上也可以执行同样的操作并随时看到更新。

## Reference
本文参考了一些网络文章才得以写成， 而且说不定比我写的更加到位, 我很愿意把它们贴出来， 希望能够对你有帮助：
1. [Hexo博客从一台电脑迁移到其他电脑](https://www.jianshu.com/p/beb8d611340a)
2. [使用hexo，如果换了电脑怎么更新博客？ - CrazyMilk的回答 - 知乎](https://www.zhihu.com/question/21193762/answer/79109280)
3. [利用Hexo在多台电脑上提交和更新github pages博客](https://www.jianshu.com/p/0b1fccce74e0)
