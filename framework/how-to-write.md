---
layout: default
title: Docs.how to write?
---

# Framework-HTW

此文档将详细描述如何使用 Beebeeto-framework 框架，让你可以编写出属于你自己的验证代码。

<hr class="content-hr">

# Download

- 框架代码下载地址：[https://github.com/n0tr00t/Beebeeto-framework](https://github.com/n0tr00t/Beebeeto-framework)

结构：

    beebeeto-framework/
     |
     |______init__.py
     |____baseframe.py
     |____batchtest
     |____demo
     |____utils
     |____LICENSE
     |____README.md
     |____SETTINGS.py

- baseframe.py : 框架基础代码，规范了输入输出规则，简单实用。基于此框架，用户可以编写任意类型的 PoC 代码来使用。
- utils: 实用插件，用户可以根据需求来调用相关的函数，例如：HTTP Header生成工具、文件统计工具等，后续我们将不断更新utils的功能。
- demo: 这是一些代码实例，边看代码边阅读此文档，有助于对框架代码的理解。
- batchtest: 介于误报较多的情况，我们编写了 batchtest 批量测试脚本。

<hr class="content-hr">

# Notes

> 注意：编写的代码要尽可能符合PEP8规范，严格规范4个空格为缩进！

在编写属于你的POC之前，你需要将baseframe引入到你的POC中，POC文件应放在与baseframe.py同一目录下。

例如：sqlinjection.py， 编辑sqlinjection.py，在开头，你需要引入baseframe框架，引入的方式如下：

    #!/usr/bin/env python
    # coding=utf-8

    from baseframe import BaseFrame

<hr class="content-hr">

# Start

由于代码是基于baseframe框架的，所以在引入完baseframe之后，你需要建立里一个MyPoc类，这个类是继承BaseFrame的，在继承框架类后，你就可以使用框架规范好的相关输入输出规则。继承的方法如下：

    #!/usr/bin/env python
    # coding=utf-8

    from baseframe import BaseFrame

    class MyPoc(BaseFrame):
        .....

poc_info 是一个字典类型数据，里面存放了这个POC的一些相关信息，方便以后检索，结构如下：

    poc_info = {
      # poc相关信息
      'poc': {
          'id': 'poc-2014-0002',# 由Beebeeto官方编辑
          'name': 'Discuz7.2 /faq.php sql注入漏洞 POC',  # 名称
          'author': 'windows95',  # 作者
          'create_date': '2014-07-28',  # 编写日期
      },
      # 协议相关信息
      'protocol': {
          'name': 'http',  # 该漏洞所涉及的协议名称
          'port': [80],  # 该协议常用的端口号，需为int类型
          'layer4_protocol': ['tcp'],  # 该协议
      },
      # 漏洞相关信息
      'vul': {
          'app_name': 'Discuz',  # 漏洞所涉及的应用名称
          'vul_version': ['7.1', '7.2'],  # 受漏洞影响的应用版本
          'type': 'SQL Injection',  # 漏洞类型
          'tag': ['Discuz!', 'faq.php', 'sql injection'],  # 漏洞相关tag
          'desc': 'Discuz 7.1 or 7.2 has sql injection in faq.php.',  # 漏洞描述
          'references': ['http://www.wooyun.org/bugs/wooyun-2010-066095',  # 参考链接
          ],
      },
    }

各个字段都对应了相关信息（如：漏洞类型参考表）， 用户可以根据自己需求去修改字段里的信息。

> 值得注意的是，layer4_protocol 指5层网络协议中第4层（传输层）协议，它的可选值为tcp/udp/sctp。

#### 命令行传参：

在MyPoc中，你无需考虑传参的问题，框架代码里内置了几个常用的参数提供给用户使用，编写者可以通过以下方式传参：

    -h, --help   打印帮助信息。
    -t, --target 目标地址，POC的目标地址，例如 sqlinjection.py -t www.google.com 即把www.google.com作为目标传入到POC里，在MyPoc中可以使用args['options']['target']来调用传入的参数。
    -v, --verify 检测模式，默认不加此参数就是以verify模式对目标进行检测，POC会调用MyPoc的verify()函数。
    -e, --exploit 攻击模式，选择-e模式，POC将会调用exploit()函数，即以攻击模式打击目标。
    --verbose 详细信息参数，bool类型值，加入此参数，POC运行时会输出过程中的详细信息。不加此参数默认以verbose方式运行。在POC中可以用args['options']['verbose']调用。即加入此参数把args['options']['verbose']设置为True。
    --info 输出poc_info信息。

以上即为框架代码中自带的参数，但如果你需要添加额外参数，例如某些需要传入cookie参数的POC，你可以在MyPoc中定义一个函数来实现，定义方式如下：

    def _init_user_parser(self):  # 定制命令行参数
        self.user_parser.add_option('-c','--cookie',
                                    action='store', dest='cookie', type='string', default=None,
                                    help='this poc need to login, so special cookie '
                                    'for target must be included in http headers.')

其中用户需要修改的地方有-c，--cookie， 这两者是定义传参时的参数名称，表示这两者都能够传入cookie参数。还有需要修改dest='cookie' 即表示传入的参数放入cookie变量中，用户在MyPoc中调用即可以args['options']['cookie']的方式调用传入的参数。type表示存入变量的类型，在这里是string类型。help则是当用户使用--help是输出的帮助信息。

#### 检测模式：

检测模式即为单纯的验证目标网站是否有漏洞，不对目标进行任何修改，上传动作。在MyPoc中用户需要定义verify函数作为检测模式，定义方式如下：

    @classmethod
    def verify(cls, args):
        .....

- @classmethod 是框架中统一使用的一个修饰器，在这编写者保持默认即可。在verify中包含cls和args两个参数。
- cls：表示MyPoc类，类似self，能够使用cls. xxx来调用类的其他用户定义的函数，例如用户定义了一个解密函数需要在POC中用到，在verify函数中可以使用cls.decode() 来调用。
- 如果用户定义的函数不需要cls参数，即函数内部没有需要调用类的其他成员函数，可以使用@staticmethod替换掉@classmeth mod。
- args：即传参变量，上小节说到的传入参数，框架会将参数保存到args中，可以使用args['options']['target']调用- t，-- target传入进来的目标地址。

在 verify 函数中，编写者需要编写 PoC 检测模式相关逻辑，在满足相关条件判定目标存在漏洞时，需要将args['success']设置为True，并把相关结果存入args['poc_ret']中。例如：

    if '14c711768474fac3bf03094625bc1aeaa' in response:
        args['success'] = True
        args['poc_ret']['vul_url'] = args['options']['target']
        return args
    else:
        args['success'] = False
        return args

例子中是一个sql注入漏洞，检测模式采用相关payload让目标站点执行md5函数，并获得请求结果到response变量中，假如response中存在这个特定的md5值，我们判定目标存在sql注入漏洞，并将args['success']设置成True，并把目标站点存入args['poc_ret']['vul_url'] = args['options']['target']中，其中vul_url是用户编写者自己定义的，也可以添加其他信息。
如果不存在，需要将args['success']设置为False。需要注意的是，无论成功与否，最后都需要return args来输出结果。

#### 攻击模式：

攻击模式即对目标采用exploit模式，定义它的方法与检测模式类似，例如：

    @classmethod
    def exploit(cls, args):
        .....

编写者在exploit函数中编写exploit的代码逻辑，返回结果和verify模式一致，具体参考上述文档。

> 注意： 如果你想让verify模式和exploit模式为同一种模式，可以只编写verify函数，然后在下面添加一句： exploit = verify 这样你就无需再写exploit函数了。

#### 调用运行：

在编写完Poc类之后，需要调用运行POC，编写者保持默认代码即可，无需更改，代码如下：

    if __name__ == '__main__':
        from pprint import pprint

        mp = MyPoc()
        pprint(mp.run())

<hr class="content-hr">

# Debug mode

为了能够更加流畅的编写，我们将 try 直接写进了框架层，因此你可以在代码中不加任何 try 捕获异常，出现异常后他都能漂亮的输出，但是他没有具体行号，但这样开启Debug模式来捕获具体详情：

    if __name__ == '__main__':
        from pprint import pprint

        mp = MyPoc()
        pprint(mp.run(debug=True))

<hr class="content-hr">

# Run

<p>
    <video width="550" height="500" controls="controls" style="margin-top:-30px;">
        <source src="/static/video/demo-1.mp4" type="video/mp4" >demo-1.mp4</source>
    您的浏览器不支持video标签
    </video>
<br>
部分浏览器可能不支持video标签，可直接访问：
<a href="/static/video/demo-1.mp4">/static/video/demo-1.mp4</a> 查看
</p>
