---
layout: default
title: Docs.faq
---

# 关于误报

开发者编写的 PoC 通常存在误报情况，这种情况多数是由判断漏洞是否存在条件不严谨所导致的。 所以应当在编写时，注意判断语句的严谨性，比如：

    if 'b2ef2cd728d8ec'[::-1] in content:

就比下面这条判断好的多：

    if 'ok' in content:

介于误报较多的情况，我们编写了 batchtest 批量测试脚本。可根据不同需求修改 batchtest.py ，例如进行 poc_2014_0007 的批量扫描，则修改93行 import ：

    if __name__ == '__main__':
      import time
      # run poc_id
      from poc_20140007 import MyPoc

      start_time = time.time()
      bt = BatchTest(seed_file='website.txt',
                    func2run=MyPoc.verify,
                    options=None,
                    result_file='result.txt',
                    thread_num=100,
                    vervose=True)
