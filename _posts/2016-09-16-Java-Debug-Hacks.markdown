---
layout:     post
title:      "Java Debug Hacks"
date:       2016-09-16 20:00:00
author:     "Java Course Staff"
header-img: "img/post1.jpg"
header-mask: 0.3
catalog:    true
---

日志和调试，是开发过程中必不可少的纠错手段。日志擅长于检查业务逻辑的错误，而调试擅长发现计算机资源的不合理使用。本文以Intellij IDEA为例讲解调试Java程序的诸多有用的技巧。eclipse虽然和idea长得不一样，但是有着相同的调试技巧，就不再写eclipse的调试操作。调试是编程的软实力，不怕不知道具体怎么做，而怕没有这么做的意识。<br>

-------

## 流程控制
“流程控制”指的是调试过程中最常用的三种调试步长：进入、跳出和跳过。

**进入（Step Into）** ![Step Into](/img/java_debug_hacks/Step_into.png)
> Step to the next line executed.

字面意思是执行到下一条代码。常用情况是进入函数内部，和Visual Studio的“逐语句调试（F11）”是一样的。

**跳出（Step Out）** ![Step Out](/img/java_debug_hacks/Step_out.png)
> Step to the first line executed after running from this method.

从当前函数跳出，执行到当前函数之后的下一条语句。

**跳过（Step Over）** ![Step Over](/img/java_debug_hacks/Step_over.png)
> Step to the next line in this file. 
执行到当前文件的下一行，和Visual Studio的“逐过程调试（F10）”是一样的。

另外，如果想直接跳到下一个断点（如果没有断点就等待程序结束），可以使用**恢复执行（Resume Execution)** ![Resume Execution](/img/java_debug_hacks/Resume_execution.png)


## 调用堆栈
查看调用堆栈也是调试过程中的重要工作，它帮助理清函数之间的调用关系。一个典型的调用堆栈如下图: 
![Call Stack](/img/java_debug_hacks/Call_stack.png "A typical call stack")

## 监视
监视有助于实时地展示变量的值。Intellij以两种形式进行监视，一个是监视窗口，
![Watcher](/img/java_debug_hacks/Watcher.png "Watcher(Variable) screen")
另一个就是代码字里行间的变量值。
![In-text Watcher](/img/java_debug_hacks/In-text_watcher.png "In-text watcher")
然而这二者默认只能监视变量，而不能监视表达式。比如说你想监视一个二维数组的某个cell的值，你需要额外**添加监视**。添加监视的方法也很简单，在监视窗口左侧的一竖排图标中有一个加号，点击那个加号就可以输入你要监视的表达式了；也可以在代码中框选要监视的表达式，右键添加监视。<br>

说到监视，还要注意的问题就是变量的作用域和声明顺序。一定要记住，**当前行是正在执行的行，不是已经执行的行**。假如当前行有变量声明，那么这个变量是监视不到的，下一行才能监视到。

#### 计算表达式
计算表达式相当于临时监视。它的图标是![Evaluate](/img/java_debug_hacks/Evaluate.png)。你也可以在顶部的*Run*菜单中找到它

#### 临时修改变量
在监视菜单里有一些“要么不戴眼镜（基本类型）、要么带小三角（类对象、数组）”的变量，这些变量是可以**在运行的时候临时修改**的。右键点击该变量，其中一项就是“Set Value”。
![Modify On The Fly](/img/java_debug_hacks/Modify_on_the_fly.png "Modify a variable on the fly")
而对于数组或类对象，你可以逐级点开它的小三角，直到找到底层的基本类型。<br>
![Modify An Array Cell](/img/java_debug_hacks/Modify_array.png "Modify an array cell")

## 设置断点属性
我们都知道左键点击代码行号，就能在该行设置断点，再左键一次就能删除断点。然而你有没有尝试过右键点击断点？<br>
在idea中，右键点击断点会弹出一个气泡对话框，
![Breakpoint Attribute](/img/java_debug_hacks/Breakpoint_attribute_dialog.png "Breakpoint Attribute (Partial)")
点击more之后是这样的。
![Breakpoint Attribute](/img/java_debug_hacks/Breakpoint_attribute_dialog_full.png "Breakpoint Attribute (Full)")
通过这个界面，我们可以给断点设置线程挂起（Suspend XXX）、条件断点（Condition）、日志（Evaluate and log)、触发后移除（Remove once hit）等高级属性。

#### 条件断点
这里重点提一下**条件断点**：条件断点就是给断点附加一个布尔表达式，当布尔表达式为真时断点生效，否则是无效的。条件断点在循环语句中尤其有用，因为你只关注循环中容易发生（或者已经发生过）bug的位置，又不想一直盯着循环变量，这时候就可以在循环内部设置一个条件断点，只有当循环变量满足某条件时断点才生效。

#### 挂起单个线程 vs. 挂起所有线程
多线程调试**最忌讳的就是一路Step Over下去**，这样你只能调试一条执行路径，调试不到其他的执行路径。如果你的程序开了多线程，你就能在调用堆栈的下拉菜单里切换你要看的线程，同时自动切换堆栈、监视窗口等。如图。
![Switching Context](/img/java_debug_hacks/Threads.png "Switching Context")
由于系统对线程的调度是随机的（虽然调度算法本身没有随机性，但是要考虑到系统中其他程序的线程的影响），如果采用“挂起所有线程”（Suspend All）的方式，一个线程触发断点，就会导致线程组中所有的线程被迫挂起。这其实会带来混乱，因为你不知道、也无法理解其他线程到底停在哪了。<br>
如果采用“挂起单个线程”（Suspend Thread）的方式，一个线程触发断点时，其他的线程照常执行，除非它们遇到断点，或者进入等待。一些优秀的IDE，如Intellij、Eclipse等，在其他线程触发断点、进入等待或完成执行时，会提示你线程状态的改变。使用这种挂起方式，好处就是能确切地知道其他线程的状态。