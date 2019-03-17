---
layout: post
title:  "Transaction management：两阶段锁（two-phase locking）"
date:   2019-03-17 05:25:06
author: Zixu Wang
categories: Study-notes Database
tags: 学习笔记 数据库 database 事务 transaction consistency 两阶段锁 two-phase locking 2PL
lang: zh
ref: transaction-consistency-2PL
---

刚刚勉强活过了这个quarter的最后一个星期，消耗了半打红牛，不眠不休地赶完了三个homework、两个大project、两篇五页的report、和一个final，这个周末终于闲下来一点，只剩一个final和三篇seminar report了，嗯 :smiley:。抽空继续写一写关于transaction management的话题吧。上一篇文章获得的阅读和关注都少得可怜。一方面可能是现在数据库原理这个方向的热度陷入了低谷；另一方面是我篇幅没有控制好，一下包含了过多的信息。虽然说这个博客的本意是让我自己巩固和记录知识，但我以后还是尽量把篇幅控制的短一点吧，我写起来也不累，文章粒度细化日后查阅起来也方便。

在[上一篇文章](/zh/study-notes/database/2019/02/24/transaction-consistency-serializability/){:target='_blank'}里，我们简单建立了database transaction的模型，并了解了serializability的概念。我们知道了为了确保数据一致性（consistency）并满足ACID中的isolation，实际的database management system需要保证其事务调度（transaction schedule）具有conflict serializability。但是在上一篇文章里我们仅仅进行了理论上的分析，不少读者可能觉得比较抽象、云里雾里。那么这一篇文章我们就来了解一个最简单的实现conflict serializability的算法：[两阶段锁（2PL: two-phase locking）](https://en.wikipedia.org/wiki/Two-phase_locking){:target='_blank'}[^1]。

<div class="toc" id="toc">
  <div class="toctitle">
    <h4>目录</h4>
  </div>
  <ul>
    <li class="toclevel-1 tocsection-1">
      <a href="#回顾">
        <span class="tocnumber">1</span>
        <span class="toctext">回顾</span>
      </a>
    </li>
    <li class="toclevel-1 tocsection-2">
      <a href="#解决思路">
        <span class="tocnumber">2</span>
        <span class="toctext">解决思路</span>
      </a>
    </li>
    <li class="toclevel-1 tocsection-3">
      <a href="#两阶段锁two-phase-locking">
        <span class="tocnumber">3</span>
        <span class="toctext">两阶段锁（two-phase locking）</span>
      </a>
    </li>
    <li class="toclevel-1 tocsection-4">
      <a href="#后记">
        <span class="tocnumber">4</span>
        <span class="toctext">后记</span>
      </a>
    </li>
    <li class="toclevel-1 tocsection-5">
      <a href="#参考资料">
        <span class="tocnumber">5</span>
        <span class="toctext">参考资料</span>
      </a>
    </li>
  </ul>
</div>
<hr>

## 回顾
开始之前我们先简单复习一下上一篇文章中相关的概念，如果读者有需要也可以返回阅读[上一篇文章](/zh/study-notes/database/2019/02/24/transaction-consistency-serializability/){:target='_blank'}。
- **数据库模型**: 我们的讨论继续使用上一篇文章中的模型：数据库由不可再分（indivisible）的、互不重叠（non-overlapping）的数据对象（data objects）的集合构成：$\\{o_1, o_2,\dots,o_n\\}$，每一个object都有一个取值范围（domain of values）。这个系统的一个*状态（state）* 就是一个从object到value的映射。数据库的操作有$read$（$r(o_i)$）和$write$（$w(o_i)$）。
- **事务（transaction）**: Transaction是对数据库系统中读写操作的更高一层抽象，代表了“一个单位”的数据库操作。一个transaction可能包含对多个数据对象的多个读写操作，但是这些操作被视为一个整体。
- **调度（schedule）**: Schedule是多个transactions的交错，表示了多个transaction中的多个数据操作的执行顺序。
- **可串行性（serializability）**: 一个可串行的（serializable）schedule “等价于”某个serial schedule，就是其中涉及的所有transaction的某种序列化执行。更具体地说，根据对“等价”的不同定义，我们得出了三种不同的serializability定义：**final state serializability (FSR)**，**view serializability (VSR)**，和**conflict serializability (CSR)**。其中，因为CSR确保了data consistency和transaction isolation并且实现复杂度最低，在实际数据库系统中我们一般选择实现CSR。

<hr>
## 解决思路
在上一篇文章中我们分析conflict serializability的时候发现，造成inconsistent schedule的原因是多个transaction之间冲突的读写操作的执行顺序。通常来说在并发情景中，这种执行顺序差异造成的错误被称为race condition。比如在 $S: w_1(x)~w_2(x)~w_2(y)~w_1(y)$ 这个inconsistent schedule的例子中，$T_1$ 抢先写入了 $x$，但是 $T_2$ 抢先写入了 $y$，这样的race condition导致了 $S$ 中出现了conflict环，没法等价于任何一种串行的调度执行。

那么我们在CS其他领域，比如多线程控制中，是怎么处理race condition的呢？没错，就是通过加锁来控制仅有一个线程能进入执行critical section。所以，一个很自然的想法就是也通过加锁的方法来控制schedule中的冲突读写操作。但是在CSR这个问题中，用锁控制的“critical section”并不是直观的一段代码，而是冲突操作的顺序。怎么通过加锁来保证所有冲突操作的顺序一致呢？这就有了最简单的两阶段锁的概念。

<hr>
## 两阶段锁（two-phase locking）[^1][^2]
Two-phase locking的算法非常简单。数据库中的每一个数据对象都有两种锁：***(S)hared lock*** 和 ***e(X)clusive lock***。正如字面意思，shared lock允许多个锁并存；exclusive lock具有排它性。两种锁之间的compatibility参考下表：

| Lock type |     Shared     |    Exclusive   |
|:---------:|:--------------:|:--------------:|
|    Shared |   Compatible   | Not compatible |
| Exclusive | Not compatible | Not compatible |

操作和锁的对应关系很简单：如果一个transaction想要读取 $x$，那它必须获取 $x$ 上的shared lock；如果一个transaction想要写入 $x$，那它必须获取 $x$ 上的exclusive lock。读到这里，读者可能会发现，这个算法就是常规的数据锁，并不能限制多个冲突操作的执行顺序啊？接下来的一条规则就是2PL的精髓所在了：

{:style="color:#333"}
>如果一个transaction释放了它所持有的**任意一个锁**，那它就**再也不能获取任何锁**。

明白了这一条规则我们也就明白two-phase locking名字的由来了：在2PL协议下，每个transaction都会经过两个阶段：在第一个阶段里，transaction根据需要不断地获取锁，叫做 ***growing phase (expanding phase)***；在第二个阶段里，transaction开始释放其持有的锁，根据2PL的规则，这个transaction不能再获得新的锁，所以它所持有的锁逐渐减少，叫做 ***shrinking phase (contracting phase)***。

2PL的算法非常简单，但是它为什么能够确保transaction的执行满足CSR呢？它的正确性证明也非常简单精妙[^2]：

{:style="color:#333"}
>我们用**反证法**证明：假设 $S$ 是一个使用2PL得出的schedule，并假设 $S$ 并不满足CSR。因为 $S$ 并不满足CSR，所以 $S$ 的serialization graph $SG(S)$ 中**必然存在一个环**（不明白的读者请参考[上一篇文章](/zh/study-notes/database/2019/02/24/transaction-consistency-serializability/){:target='_blank'}），不失一般性地，我们把这个环记做 $T_i\to T_j\to\cdots\to T_i$。考虑环中的一条边 $T_i\to T_j$，这条边的存在说明 $T_i$ 中存在某个操作 $o_i$，它和 $T_j$ 中的某个操作 $o_j$冲突，并且 $o_i\prec_S o_j$。因为 $o_i, o_j$ 冲突，我们根据conflicting operations的定义可知，$o_i$ 和 $o_j$ 涉及同一个数据对象，并且其中至少有一个写操作。那么根据2PL的lock compatibility，这两个操作所需要的锁一定冲突。又因为 $o_i\prec_S o_j$，所以 $T_i$ 先取得了锁 $l$；并且在此之后 $T_j$ 取得了和 $l$ 冲突的锁（因为 $T_j$ 执行了 $o_j$），所以这时 $T_i$ 一定已经释放了 $l$。所以，我们可以总结得出：**$T_i$ 一定在 $T_j$ 获得 $T_j$ 需要的所有锁之前释放了某个锁**，换句话说，**$(T_i\to T_j)\in SG(S)\Rightarrow$ $T_i$ 一定在 $T_j$ 完成expanding phase之前进入了shrinking phase**。依此类推，$(T_j\to T_k)\in SG(S)\Rightarrow$ $T_j$ 一定在 $T_k$ 完成expanding phase之前进入了shrinking phase...... 所以，环 $(T_i\to T_j\to\cdots\to T_i)\in SG(S)\Rightarrow$ $T_i$ 在它自己完成expanding phase之前就进入了shrinking phase，得出矛盾，假设不成立，所以使用2PL得出的schedule必然满足CSR。证毕。

<hr>
## 后记
2PL通过引入expanding phase和shrinking phase的顺序，非常精妙简单地在有冲突操作的transaction之间产生了一个偏序，保证了serialization graph中不存在环。本质上来说，就是通过锁的设置保证了在每一组冲突操作中，第一个获得锁的transaction一定会排在后来的transaction之前执行，从而实现了等价于串行执行。

2PL还有许多变种，比如保守两阶段锁（conservative 2PL）：假设每个transaction都提前知道自己会进行哪些操作，在开始执行之前先获取所有需要的锁，这种保守算法可以保证transaction不会被abort。还比如严格两阶段锁（strict 2PL）：transaction直到执行结束（commit/abort）后才统一释放所有的锁。这种算法保证了strictness，避免了一个transaction abort就导致其他transaction产生cascading abort。这些有趣的衍生2PL就留给有兴趣的读者自己研究了。

最后给读者留几道有趣的思考题：

1. 判断下面的两个schedule能否由一个使用2PL的scheduler产生。如果可以，给出一种可能的加锁解锁的顺序；如果不行，指出导致2PL拒绝这一schedule的第一个操作。
    1. $w_1(x)\;w_1(x)\;w_2(y)\;w_3(z)\;w_1(a)\;w_2(a)$
    2. $w_3(x)\;w_4(y)\;w_1(z)\;w_3(a)\;w_3(y)\;w_2(a)\;w_3(x)\;w_1(a)\;w_3(y)\;w_2(z)\;w_1(x)$

2. 我们介绍的最简单的2PL算法可能会出现死锁（deadlocks）和活锁（livelocks）的问题，你能给出一个可以处理这两种情况并保证系统进展（progress）的2PL算法吗？
3. 你在 (2) 中提出的算法可以在分布式的2PL系统上使用吗？（不同的数据对象可能分布在不同的site，它们的锁也都由各自的site管理）如果不行，在这样一个分布式的系统里该怎么检测或处理死锁呢？

<hr>
## 参考资料
[^1]: [Two-phase locking - Wikipedia](https://en.wikipedia.org/wiki/Two-phase_locking){:target='_blank'}
[^2]: UCI CS223: Transaction Processing and Distributed Data Management
