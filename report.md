# 1 论文题目

标题: Adaptive Shivers Sort: An Alternative Sorting Algorithm

作者: Vincent Jugé

来源: 2020 ACM-SIAM Symposium on Discrete Algorithms

链接: https://epubs.siam.org/doi/10.1137/1.9781611975994.101

# 2 论文阅读报告

## 2.1 摘要

作者提出了一种新的排序算法, 叫做**自适应 ShiversSort (adaptive ShiversSort)**, 它利用存在的单调 run (片段) 来高效地排序部分有序的数据. 这个算法是有名的 **TimSort** 排序算法的变种, **TimSort** 是 Python 和 Java 等语言对于非基本数据类型的标准库排序算法. 更精确地讲, **adaptive ShiversSort** 是一种 $k$-aware 归并算法, 这类算法由 Buss 和 Knop 提出, 包含那些与 **TimSort** 类似的算法.

在文章中, 作者证明了尽管 **adaptive ShiversSort** 容易实现且与 **TimSort** 仅有细微差别, 以比较次数来衡量的计算开销是所有自然归并排序中最优的. 这使得 **adaptive ShiversSort** 成为第一个从这个特性中获益的 $k$-aware 算法, 比 TimSort 的最差情况有了 33% 的提升, 这也表明 **adaptive ShiversSort** 能够成为代替 **TimSort** 的有力竞争者.

然后, 作者研究了 $k$-aware 算法的最优性: 与最优的稳定自然归并排序算法相比, 作者给出了此类算法的最佳逼近因子的上下限. 特别地, 我们设计了 **adaptive ShiversSort** 的归纳, 其计算成本在任意小的乘法因子下都是最优的.

## 2.2 问题定义

数据排序问题一直是计算机科学中最早且研究最广泛的问题之一, 由于它在许多算法中作为子例程使用, 因此排序无处不在. 早在上世纪 40 年代, 就发明了排序算法, 该算法在时间 (比较次数或元素移动次数) 和空间复杂度上有最优性. 每十年左右, 就有一种新的主要排序算法被发明, 这些算法可以使用不同的方法进行排序, 也可以采用经过特殊调整的数据结构来改进以前的算法, 如: **MergeSort**, **QuickSort**, **SmoothSort**, **SplaySort** 等等

在 2002 年, 软件工程师 Tim Peter 发明了一种称为 **TimSort** 的新的排序算法. 该算法立即证明了其对实际数据进行排序的效率, 并在诸如 Python 和 Java 之类的广泛使用的编程语言的核心库中被用作标准排序算法. 这种定制算法相对于之前被使用的最优算法的突出之处, 重新引发了人们对于排序算法研究的兴趣.

理解 **TimSort** 成功的原因仍然在进行中. 这些原因包括 **TimSort** 与数据的实际分布和计算机体系结构配合的很好 (例如处理缓存问题). 特别的, 一个成功解释了 **TimSort** 为什么适合现实数据排序的模型包含了 run 的分解. 早于 **TimSort** 的 **NaturalMergeSort** 已经使用了这种分解方法, 其对传统的 **MergeSort** 进行了如下改进: **NaturalMergeSort** 将数组拆分为单调的子序列, 也称为 run, 并将这些 run 合并到一起. 因此, 所有使用这种特性的排序算法都被称为自然归并排序算法.

**TimSort** 不仅是一种自然归并排序, 还包括许多优化. 这些优化被精心设计, 经过了广泛的测试, 最终提供了最佳的复杂度性能. **TimSort** 的总体结构可以被分为三个主要部分:

1. 一个插入排序的复杂变体, 用于处理小的 run;
2. 一个用于选择要合并的大 run 的简单策略
3. 一个用于合并这些 run 的复杂的子过程

第一和第三个部分是经过最精细调整的部分, 因此很难理解为什么他们如此高效, 以及如何去改进. 第二部分则非常简单, 所以在这部分最有可能进行更改并改进 **TimSort** 的效率.

**TimSort** 的成功激发了人们对于寻找一种排序算法的兴趣, 它能够适用于 run 数量更少的数组. 然而, **TimSort** 的简单概念让它的复杂度分析变得困难. 直到 2015 年, 也就是 **TimSort** 被大规模应用的十年之后, 才有人证明了排序大小为 $n$ 的数组, **TimSort** 需要 $\mathcal O(n \log n)$ 次比较.

更糟糕的是, 由于缺乏系统的和理论的分析, 最近人们从 Python 和 Java 实现的 **TimSort** 中发现了一些 bug.

自从 **TimSort** 被发明之后, 已经提出了几种自然归并排序算法, 所有的这些算法都旨在提供易于证明的复杂性. 这些算法包括 **ShiversSort**, **MinimalSort**, $\alpha$-**MergeSort**, 以及最近被提出的 **PeekSort** 和 **PowerSort**.

如下表所示, 这些算法具有 TimSort 的大多数优良特性. 比如, 除了 **MinimalSort**, 这些算法都是稳定排序. 这一点对于归并排序尤为重要, 因为只有相邻的 run 会被合并, 这让归并过程可以直接合并数组而不需使用链表. 这个特性对于合并复合类型也十分重要, 因为可以按照不同的关键字分别执行两次排序. 而且, 这些排序都能在 $\mathcal O(n\log n)$ 时间内排序长度为 $n$ 的数组. 而且除 **ShiversSort** 外, 所有的排序都能在 $\mathcal O(n\log\rho)$ 时间内完成, 其中 $\rho$ 是数组中 run 的数量. 在基于比较的排序中, 这是复杂度最优的算法.

| Algorighm            | Time complexity             | Stable       | k-aware  | Worst-case merge cost                    |
| -------------------- | --------------------------- | ------------ | -------- | ---------------------------------------- |
| NatrualMergeSort     | $\mathcal O(n\log\rho)$     | $\checkmark$ | $\times$ | $n\log_2\rho + \mathcal{O}(n)$           |
| TimSort              | $\mathcal O(n+n\mathcal H)$ | $\checkmark$ | $k=4$    | $3/2\,n\mathcal{H} + \mathcal{O}(n)$     |
| ShiversSort          | $\mathcal O(n\log n)$       | $\checkmark$ | $k=2$    | $n\log_2n + \mathcal{O}(n)$              |
| MinimalSort          | $\mathcal O(n+n\mathcal H)$ | $\times$     | $\times$ | $n\mathcal{H} + \mathcal{O}(n)$          |
| $\alpha$-MergeSort   | $\mathcal O(n+n\mathcal H)$ | $\checkmark$ | $k=3$    | $c_\alpha n\mathcal{H} + \mathcal{O}(n)$ |
| PowerSort            | $\mathcal O(n+n\mathcal H)$ | $\checkmark$ | $\times$ | $n\mathcal{H} + \mathcal{O}(n)$          |
| PeekSort             | $\mathcal O(n+n\mathcal H)$ | $\checkmark$ | $\times$ | $n\mathcal{H} + \mathcal{O}(n)$          |
| adaptive ShiversSort | $\mathcal O(n+n\mathcal H)$ | $\checkmark$ | $k=3$    | $n\mathcal{H} + \mathcal{O}(n)$          |

一些算法的复杂度甚至与 run 的长度有关, 而不仅仅是 run 的数量: 如果一个数组包含 $\rho$ 个 run, 每个 run 的长度是 $r_1,\dots,r_\rho$, 这些算法能在 $\mathcal O(n+n\mathcal H)$ 内执行完毕, 其中 $\mathcal H = H(r_1/n,\dots,r_\rho/n)$ 且 $H(x_1,\dots,x_\rho)=-\sum_{i=1}^\rho x_i\log_2x_i$ 是广义熵函数. 将 run 的数量和长度作为参数, 这个更精细的上界仍是基于比较的排序模型中最佳的.

只关注时间复杂度, 似乎有五种算法可以相互媲美, 并且需要更精细的复杂度评估才能将他们分开. 除了 **TimSort**, 这些算法实际上只描述了合并 run 的策略, 用于合并的子过程是隐含的. 因此, 我们确定如下的开销模型.

由于朴素的归并算法大约需要 $m + n$ 次元素比较和元素移动来合并长度为 $m$ 和 $n$ 的两个数组, 并且在最坏的请卡 u 嗯下可能需要 $m+n$ 个元素移动, 因此我们测量如下的归并开销复杂度: 将长度为 $m$ 和 $n$ 的两个 run 合并的成本定义为 $m+n$, 并且定义算法的复杂度为在算法规定的归并策略下, 所有归并过程的开销总和.

在这种新模型中, 可以证明任何自然归并排序的合并成本至少是 $n\mathcal{H}+\mathcal{O}(n)$. 这使得 **MinimalSort**, **PeekSort** 和 **PowerSort** 是表格中仅的三个归并开销最优的算法.
因此, 人们还没有找到一种自然的合并类别, 其结构非常接近于 TimSort 且其合并成本在 $\mathcal{O}(n)$ 前的加法项也是最佳的.

找到这一算法的第一步是引入恰当的 "类 **Timesort**" 算法的概念, 因此我们来看一看 $k$-aware 类算法. 这一类算法被发明的目的是明确哪些算法具有与 **TimSort** 相似的合并策略. 更精确地说, **TimSort** 是基于发现

## 2.3 算法或证明过程

## 2.4 实验结论

# 3 领域综述

# 4 方法不足与改进

## 4.1 方法不足

## 4.2 方法改进

## 4.3 改进结果理论分析

## 4.4 改进结果实验验证

### 4.4.1 实验设计

### 4.4.2 实验结果

### 4.4.3 实验结果分析

# 5 附录

# 参考文献
