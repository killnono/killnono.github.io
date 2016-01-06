title: Android内存泄漏和leakcanary工具
date: 2015-12-01
---

### 内存泄露和内存优化 ###
降低运行时内存，防止oom；
优化不合理的内存使用，避免平凡GC造成的卡顿。

个人感觉内存的优化，更多的是体现在编码过程中的一种策略。  
包括使用轻量级数据结构，合理的缓存机制，重用机制，bitmap胖子的合理缩放和解码等.  
这类型的文章有很多，也介绍的很全面很有用。（占位符，等放链接）

内存泄漏则更偏向于编码时的不严谨和对系统一些机制的疏忽。  
keep context could release , cursor 或 I／O的即时释放,bitmap的释放。

当然两者，优化和避免泄漏都是为了防止oom，提升app的性能和质量。

### 内存泄漏###
提到内存泄漏，是在开发中很容易被忽视的一个问题，谁让我们对java自动回收的固有影响，觉得java开发根本无需太注意内存管理的问题，却忘了GC的机制和原理。  
现在Android开发中最普遍的泄漏，便是Activity的泄漏。  
keep context could release，一般指的是在activity finish时，能保证其的正常回收。

+ 最常见一种情况是匿名内部类，比如handler的问题，其实现在AS的lint插件已经会给用户做Warning提醒，警告用户可能造成内存泄漏，比如一个消息事件生命周期过长，activity执行了finish后，消息持用其引用，造成资源无法释放，网上也基本建议使用静态内部类或是持用acitivty的软引用。
+ 另一个很常见的情况是传递Activity的上下文。很多时候初始化一个全局啊单例时，需要接受一个上下文对象。如果传入的context时activity，那么也很有可能造成该activity无法释放了。因此在出现这类型的初始化传递时，避免直接传递acticity的context，而是application的context。

### 开发中遇到的一个内存泄漏问题###
其实对于现在IDE的越来越智能，代码静态检测工具就可以帮我们检测到许多内存泄漏问题。根据泄漏的严重性和处理的惰性做一个平衡～忽略了一些**伪泄漏**（个人把类似于handler那一类型的延迟释放<当然前提是能释放！>戏称为此）。
还有如后台请求持用上下文，也可能造成的伪泄漏。
伪泄漏在很多时候可能无法暴露问题。但是也是一个需要注意的点。

但是在上期迭代中，引发了一个真泄漏（真的泄漏了。。）的情况。
迭代后期想做性能分析优化时，观察ddms memory,发现内存异常高。一开始以为是页面有大量图片原因，造成加载了这些页面后内存上升。然后先做图片加载优化啊，手动释放啊，手动GC啊啥的,但是后来操作发现，然并卵，finish了这些acitivty后，内存基本没下降释放。以至于最后第三方测试反馈报告显示，最高内存占用快1-200mb了。然后想到了以前简单使用过的内存检测工具**Leakcanary**.一检测发现，一个全局的Taskmanager持用一个类似于列表列表的引用，队列列表持用task引用，task持用activity的context。Ok，当时我的第一反应是我把context改成

```java
context = getApplicationContext();
BaseTask task = new Task1(context,arg1,arg2);
```
然后发现，好像没用啊！！回过来在看leak的log，发现是说task中的一个callback持有了activity，callback是activity中的一个匿名内部类传递到了task中。
于是就好玩了。首先，这个情况变成了匿名内部类泄漏，但是从过程来看，是个伪泄漏啊。不就是假设我的task在队列里，造成activity无法马上释放么？理论上来说，稍等一段时间，不就能释放了（而且同事还做了一个类似于task cancel的接口，在finish的情况下可以取消task），不就完美释放了么？
ok，回到最初的点来：  
Taskmanager持用一个类似于列表列表的引用，cancel一个task会remove掉，但是如果是task done，没有做remove～现在的情况是，假设我的一个拥有很多bitmap的activity，发起了一个task，正常结束，然后这个task的引用还在manger的队列列表，没有被移除～。


### 安利工具LeakCanary###
[LeakCanary github](https://github.com/square/leakcanary)  
基础使用十分简单，个人觉得功能很强大，可以帮你找出很出泄漏风险。


