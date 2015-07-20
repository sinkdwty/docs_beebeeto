---
layout: default
title: Docs.Beehive.Faq
---

### No module named ***

Beehive 中有一部分攻击载荷依赖于第三方 Python 库，所以使用 pip 或者 easy_install 安装即可。

例如：

- sudo pip install name

<hr class="content-hr">

### No module named baseframe

Beehive 依赖于 Beebeeto-framework 所以在 setup.py 中我们会进行检测与 Beehive 同级目录有没有 bframework 框架，如果没有则会进行 clone。

解决方案：git clone https://github.com/n0tr00t/Beebeeto-framework

使得最后的目录结构如下：

    Test
    |
    |- Beebeeto-framework
    |- Beehive

