# 资料

CS学习  https://github.com/izackwu/TeachYourselfCS-CN/blob/master/TeachYourselfCS-CN.md

SCIP视频  https://www.bilibili.com/video/av8515129/

SCIP书籍  https://mitpress.mit.edu/sites/default/files/sicp/full-text/book/book-Z-H-4.html

SCIP答案 http://community.schemewiki.org/?SICP-Solutions  https://sicp.readthedocs.io/en/latest/

Scheme语法  https://deathking.github.io/yast-cn/



---



# 总结

函数式

核心在于，将过程/函数/方法也视为数据/对象，过程可以作为参数也可以作为返回



抽象分层

Scheme语法非常简单，泛用性也很强，比如可以把函数也视为对象

因此一些基础功能如for-each、map等，都可以自定义实现

之后再基于这些基础功能封装更上一层的功能，甚至可以用scheme作为母语言再开发一套高级语言

有点类似架构里的分层思想，这里是更基础的编程上的、无关业务的语法和逻辑上的分层





如何解决问题？如何将问题抽象到计算机？

* 黑盒抽象
* 约定接口
* 元语言抽象



这里以新语言 LISP 为例进行尝试

LISP 是前缀操作符格式 

+ 基本操作符 +1 5 9 = 15；  + 2 5（* 5 6）=37
+ 定义变量 define a 5； define b（* a a）
+ 定义函数 define (sqr x) (* x x) 使用 sqr 10
+ 函数可以作为参数，传入另一个函数，也可以作为返回内容
+ 函数可以作为对象，被再次用别的名称定义
+ 局部定义 let



递归求解根号的例子，说明程序如何解决问题。

定义一个个小方法，组成大方法，最终封装这个方法，对外提供直接使用。



迭代Interator 和 递归Recursion

迭代是一个直线，递归是一个回环



递归例子

斐契那波数列

fb(n) = fb(n-1) + fb(n-2) ... 将文字拆成2个子问题，递归下去。问题是会有重复计算。



抽象

https://www.bilibili.com/video/av8515129/?p=3&spm_id_from=pageDriver

遇到相同类似的方法时，如累加、不同变种的累加，如何抽取通用的方法？

除了定义数据，还可以定义过程，方法也可以是参数甚至是返回~ 全部都抽象



构造

对于一个分数，如何用数据表达？

使用构造，相当于面向对象编程。分数对象，其中包含分子字段，分母字段。

构造对象也是一种抽象分层的方式，操作xx对象，依赖于xx对象下的xx字段，xx字段又可能是某对象，再依赖于下面某个xx函数...



层数越多时，复杂度越高，修改维护成本越高，如何解决呢？

命名之，才能控制。把可变的一些内容都抽取出来？



数据结构

利用cons（数据对）这种最简单的例子，说明了 cons + cons、链表、二叉树等复杂结构的组合





Lambda

SCIP中过程/方法也是一个参数，所以可以定义一些通用的方法

如 map 参数是 过程p 和 列表l

```scheme
(define (map p l)
    (if(null? l)
        nil
        (cons (p (car l))
            (map p (cdr l)))))
```

Java8中引入了Steam用于lambda操作

RxJava中也有lambda操作



---



# 读书笔记

https://book.douban.com/subject/1148282/



1.1 计算机语言的基础元素

* 基本表达形式  15
* 组合的方法 (+ 15 18)
* 抽象的方法 define  (<name>  <params>)  <body>
  * define 可以定义一个数据，也可以定义一个过程（方法）



1.1.8 过程作为黑箱抽象

当把程序用自然思维一步步拆解为过程时，通常会把更细节的异步先作为黑箱抽象，不关心其实现细节。

之后再一层层向下完善这些黑箱抽象的过程方法。



1.2 过程与它们所产生的计算

递归和迭代



1.3 用高阶函数做抽象

将过程也作为参数，抽象之



1.3.2 lambda和let

匿名函数直接用lambda申明。(lambda  <params>  <body>)

用let创建局部变量，另多个参数代表多个值，然后在后面的body局部内使用

(let ((<var1> <exp1>) (<var2> <exp2>)... ) <body>)



1.3.3 过程作为一般性的方法？



1.3.4 过程作为返回值





2.构造数据对象

将多个数据进一步封装成一个数据整体，对象

针对对象的各种过程操作，应该建立起适当的抽象屏障，不关心底层细节的内容



2.1 数据抽象导引

以有理数对象为例进行封装，主要有构造函数和选择函数

```scheme
; 构造函数，把分子n，分母d两个整数构造成一个有理数对象
(make-rat <n> <d>)
; 选择函数，传入有理数对象，分别返回分子分母
(numer <x>)
(denom <x>)
```

具体实现用scheme自带的cons car cdr



2.2 层次性数据和闭包性质

用cons实现了个链表list结构，然后用它遍历所有数据做变化或打印，

引出了封装遍历过程的概念，即map、foreach方法

这是更高一层的抽象，对「遍历」概念的抽象

```scheme
; 定义map
(define (map p l)
    (if (null? l)
        nil
        (cons (p (car l))
            (map p (cdr l)))))

; 使用map，比如对列表l挨个缩放s倍
(define (scale-list s l)
    (map (lambda(x)(* 10 x)) l))

; 使用map封装后的scale-list方法
(display (scale-list 10 (1, 2, 3, 4)))
```





