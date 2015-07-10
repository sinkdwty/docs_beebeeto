---
layout: default
title: Docs.Beehive.Usage
---

# Video

<embed src="http://player.youku.com/player.php/sid/XMTI3OTIyMzA3Mg==/v.swf" allowFullScreen="true" quality="high" width="480" height="400" align="middle" allowScriptAccess="always" type="application/x-shockwave-flash">
<hr class="content-hr">

# Installation

Download Zip: [https://github.com/n0tr00t/Beehive/archive/master.zip](https://github.com/n0tr00t/Beehive/archive/master.zip)

Git:

    git clone https://github.com/n0tr00t/Beehive

Unix:

    cd beehive
    sudo python setup.py

Windows:

    dir beehive
    python setup.py

如果你的 pip 被墙，可以选择 sudo python setup.py proxy 代理模式运行。哦对，我们为了兼容 Windows 真是废了很大的力气 :-)

<b>注意：</b> beehive 需要在 Python 2.x 平台上运行

<hr class="content-hr">

# Usage

#### 初始化

运行程序并导入初始数据库文件：

    python beehive.py

    beehive > updatedb -h
    Usage: updatedb [options] arg

    Options:
      -h, --help            show this help message and exit
      -m MODE, --mode=MODE  Update database. (json / pocs)

    beehive > updatedb -m json

更新数据库功能会寻找 beehive 目录下的 pocdb.json ，通常在导入成功数据库后使用者可以看到导入多少 PoC & Exploit ，更新数量，失败数量。

#### 导入独有的数据文件

如果你拥有主站帐号，那么可在个人主页（www.beebeeto.com）中，右上方导出数据功能导出属于自己的 pocdb.json ，放置到根目录更新那些只对 Beeman 公开和购买过的脚本。

内置交互式命令（其中通常会用到的为 help 以及 q （退出当前模式或退出程序））：

- q / quit
- history
- help
- list
- ...

#### 扫描模式

* shooter : 1 -> 1
* hunter  : 1 -> *
* storm   : * -> 1

<br>
shooter / 单对单模式，也就是传统使用单个脚本进行测试时的方法，默认情况下它需要加载一个攻击载荷然后运行时加上目标：

![/static/img/Screenshots_beehive_2.png](/static/img/Screenshots_beehive_2.png)

hunter / 单对多模式，升级版本的 [beebeeto-framework batchtest](http://www.beebeeto.com/article/5/)：

![/static/img/Screenshots_beehive_3.png](/static/img/Screenshots_beehive_3.png)

storm / 多对单模式，用户选择配置适合目标的 PoC 列表进行扫描测试，可以加载全部攻击载荷，也可加载搜索结果：

![/static/img/Screenshots_beehive_4.png](/static/img/Screenshots_beehive_4.png)


#### 搜索功能

进入 Beehive 主程序的交互式模式后，可以使用 search 命令对目前已有的数据库进行搜索，结果中包含：

- 漏洞名称
- 漏洞时间
- 漏洞等级
- 漏洞作者
- 能否批量扫描
- Poc id

在 storm 模式中可使用 loadsearched 命令将刚才搜索的结果全部加载，自动去除掉不可批量扫描模式的脚本。

#### lastret & export

在一次扫描任务结束后，使用者可以使用 lastret 来查看刚才的扫描结果，也可以使用 export results.json 导出文件。

#### info

使用 info pid 可以快速查看这个脚本的信息（名称，简介，来源，作者，攻击类型等）

#### help

常用 help 可以轻松的记住这些小命令：）

<hr class="content-hr">

# Update Beehive & Pocdb

Beehive 目录下运行：

    git pull origin master

如果 Pocdb.json 更新了的话，可以进入交互式模式运行，以添加新增的攻击载荷：

    updatedb -m json

另外别忘记偶尔切换到 beebeeto-framework 更新一下，同样也是 git pull :-)
