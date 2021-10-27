> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [www.kancloud.cn](https://www.kancloud.cn/hanxt/javacrazy/1579397)

> 当你用十几行代码，完成别人两三行就搞定的问题，你不觉得自己有问题么？ 当别人面对一个老问题，用一个新的方法解决了，你不想一探究竟么？ 当别人完成该工作用了一小时，你用了一天，你不想多休息休息么？ 期望能够让读者攫取到一些有价值的，能够提高工作效率的东西。让你的写代码，一看上去就透漏着一种高级的味道；让你的设计，一看上去就经过专业的学习与训练。

**Java8 forEach** 是一个工具方法用于遍历集合，比如: (list, set or map) 和 stream 流（java8 提供的另外一个特性），然后对集合中的每一个元素执行特定的操作。

1. Java 8 forEach 方法
--------------------

#### 1.1. Iterable.forEach() 方法

下面的代码片段显示了 Iterable 接口 forEach 方法的默认实现。我们可以通过这个方法去遍历除了 Map 之外的所有集合类。  
![](https://img.kancloud.cn/cf/ce/cfcebc1645f035a9597d6f0d120f810a_464x181.png)

上面的方法对 Iterable 的每个元素执行操作，直到所有元素都已处理或该操作引发异常。“action”用来表示一个接受单个输入参数且不返回结果的操作。它是 “Consumer” 接口的一个实例。

![](https://img.kancloud.cn/f6/ac/f6acbe2b8fb14f718a7e2f3cf940e980_568x218.png)

我们可以通过实现 Consumer 接口的 accept 方法，实现自己对集合元素需要做的自定义操作。比如：下面的代码是实现集合中字符串转大写并打印出来的操作。

![](https://img.kancloud.cn/c2/41/c2415cdf1368acb1c11a0d9c7888cef8_572x374.png)

#### 1.2. Map.forEach()

Map.forEach() 方法对 map 中的每一个 entry 执行特定的操作，直到所有 map 的 entry 被处理完成或者抛出异常。  
![](https://img.kancloud.cn/e5/fa/e5fa75e1e848d00d76447bcdfba2651e_606x337.png)

使用 Map.forEach() 方法  
![](https://img.kancloud.cn/f6/bf/f6bff1608225b3d5923e8986efb012c0_520x325.png)

与 List 等集合类遍历类似，我们可以自定义一个 biconsumer action 去处理 key-value 键值对.  
![](https://img.kancloud.cn/63/0f/630fee49958b3af7322278dbf8c4b71d_435x307.png)  
Program output.

```
Key is : A
Value is : 1
 
Key is : B
Value is : 2
 
Key is : C
Value is : 3


```

复制

2. 使用 forEach 遍历 List 的例子
-------------------------

下面的代码使用 forEach 遍历 List 中的所有偶数。  
![](https://img.kancloud.cn/71/ef/71ef84531ebb29008d25f61806223db5_476x180.png)

输出：

```
2
4


```

复制

3. 使用 forEach 遍历 Map
--------------------

We already saw above program to iterate over all entries of a[HashMap](https://howtodoinjava.com/java-hashmap/)and perform an action.

We can also iterate over map keys and values and perform any action on all elements.

Java 8 forEach map entries  
![](https://img.kancloud.cn/bd/9a/bd9a0e0508cac02e9b5c0196d193ed9a_590x430.png)

Program output.

```
A=1
B=2
C=3
 
A
B
C
 
1
2
3


```

复制