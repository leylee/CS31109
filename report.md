$\newcommand{o}{\overline} \newcommand{S}{\mathcal{S}} \newcommand{l}{\mathcal{l}} \newcommand{H}{\mathcal{H}} \newcommand{lognc}{\log_2(n/c)}$

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

## 2.2 引言与定义

数据排序问题一直是计算机科学中最早且研究最广泛的问题之一, 由于它在许多算法中作为子例程使用, 因此排序无处不在. 早在上世纪 40 年代, 就发明了排序算法, 该算法在时间 (比较次数或元素移动次数) 和空间复杂度上有最优性. 每十年左右, 就有一种新的主要排序算法被发明, 这些算法可以使用不同的方法进行排序, 也可以采用经过特殊调整的数据结构来改进以前的算法, 如: **MergeSort**, **QuickSort**, **SmoothSort**, **SplaySort** 等等

在 2002 年, 软件工程师 Tim Peter 发明了一种称为 **TimSort** 的新的排序算法. 该算法立即证明了其对实际数据进行排序的效率, 并在诸如 Python 和 Java 之类的广泛使用的编程语言的核心库中被用作标准排序算法. 这种定制算法相对于之前被使用的最优算法的突出之处, 重新引发了人们对于排序算法研究的兴趣.

理解 **TimSort** 成功的原因仍然在进行中. 这些原因包括 **TimSort** 与数据的实际分布和计算机体系结构配合的很好 (例如处理缓存问题). 特别的, 一个成功解释了 **TimSort** 为什么适合现实数据排序的模型包含了 run 的分解. 早于 **TimSort** 的 **NaturalMergeSort** 已经使用了这种分解方法, 其对传统的 **MergeSort** 进行了如下改进: **NaturalMergeSort** 将数组拆分为单调的子序列, 也称为 run, 并将这些 run 合并到一起. 因此, 所有使用这种特性的排序算法都被称为自然归并排序算法.

**TimSort** 不仅是一种自然归并排序, 还包括许多优化. 这些优化被精心设计, 经过了广泛的测试, 最终提供了最佳的复杂度性能. **TimSort** 的总体结构可以被分为三个主要部分: (i) 一个插入排序的复杂变体, 用于处理小的 run; (ii) 一个用于选择要合并的大 run 的简单策略; (iii) 一个用于合并这些 run 的复杂的子过程. 第一和第三个部分是经过最精细调整的部分, 因此很难理解为什么他们如此高效, 以及如何去改进. 第二部分则非常简单, 所以在这部分最有可能进行更改并改进 **TimSort** 的效率.

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

找到这一算法的第一步是引入恰当的 "类 **Timesort**" 算法的概念, 因此我们来看一看 $k$-aware 类算法. 这一类算法被发明的目的是明确哪些算法具有与 **TimSort** 相似的合并策略. 更精确地说, **TimSort** 发现 fly 中的 run, 并将这些 run 保存在栈中: 如果一个 run 从数组中第 $i$ 个元素到第 $j$ 个元素, 那栈将会包含数对 $(i, j)$. 然后, **TimSort** 只合并在栈顶的 run, 而这些合并的决策只基于栈顶这些 run 的长度.

这一过程的基本原理是, 这样处理 run 能够适应计算机体系结构, 比如可以避免高速缓存未命中. 另外, 当一种自然归并排序算法只根据栈顶的 $k$ 个 run 决定应该合并哪些 run, 并且只合并栈顶 $k$ 个 run 中的某几个, 就称这种算法是 $k$-aware 的. 上表中的第四列表明了哪些算法是 $k$-aware 的且 $k < +\infty$.

聚焦于 $k$-aware 算法看起来与目标更加相关, 因为 TimSort 的一些很好的特性也是由于其第 (ii) 和第 (iii) 部分的高度调整. 因此, 如果不改变这些部分, 而且遵循与 TimSort 相似的归并逻辑, 那就有理由希望这些 TimSort 的优点被完整地保留, 尽管人们不知道这些优点为何存在. 这告诉我们, 区分自然归并排序的最好特征是他们的归并策略, 并在其后集成部分 (ii) 和 (iii).

我们提出了一种新的自然归并排序, 称之为 adaptive ShiversSort. 如上所述, 我们使用特定的 run 合并策略与其他算法区别开来. Adaptive ShiversSort 是 TimSort 和 ShiversSort 的混合, 借鉴了这两种算法好的特性. 结果是, adaptive ShiversSort 的归并策略与 TimSort 极为相似, 这意味着从一种算法迁移到另一种算法几乎是没有成本的, 因为它只需要更改 Java 代码中的几十行即可.

Adaptive ShiversSort 是一个 $3$-aware 算法, 该算法是稳定排序且拥有 $n\mathcal{H} + \mathcal{O}(n)$ 的归并上界, 这个上界是最优的. 因此, 相对于表中的所有标准, adaptive ShiversSort 似乎是最优的. 它是第一个已知的 $k$-aware 算法, 合并成本为 $n\mathcal{H} + \mathcal{O}(n)$. 而且, 因为其简单的策略, adaptive ShiversSort 的运行时间复杂度证明也很简单. 下面, 我们将给出该证明的简短独立版本.

为了与 PeekSort, PowerSort 和 adaptive ShiversSort 比较, 我们研究了它们最佳情况下的合并成本. 事实证明, 在每种情况下, PowerSort 的合并成本都介于 $n\mathcal{H}$ 和 $n(\mathcal{H} + 2)$ 之间, 这些边界都与任何稳定的归并排序都是最优的. PeekSort 的合并成本仅有 $n(\mathcal{H} + 3)$ 的上界. 这比 PowerSort 稍差一点, 因此下面我们将不讨论该算法. 同样, adaptive ShiversSort 的合并成本仅由 $n(\mathcal{H} + \Delta)$ 限制上界, 其中 $\Delta = 24/5 - \log_2 5 \approx 2.478$, 这也比 PowerSort 稍差一点. 因此, 我们设计了一种 adaptive ShiversSort 的变体, 称为 length-adaptive ShiversSort, 该变体失去了在线的性质, 但其合并成本的上限为 $n(\mathcal{H} + 2)$.

最终, 我们要探讨的问题如下: 对于给定的整数 $k$ 和一个实数 $\varepsilon \gt 0$, 是否存在一种 $k$-aware 算法, 对于任意的数组, 其合并成本最多为任意稳定归并排序合并成本的 $1 + \varepsilon$ 倍? 我们证明了当 $k = 2$ 时, 不存在这样的算法; 对于任意 $k \ge 3$, 使得前提成立的一组 $\varepsilon$ 没有上界, 并且我们证明了下界是一个正实数, 且当 $k$ 增长时, 下界趋近于 $0$.

## 2.3 Adaptive ShiversSort

在这一节, 我们描述了 adaptive ShiversSort 算法和相关算法的 run 合并策略. Adaptive ShiversSort 的合并策略在算法 1 中进行了描述. 为了便于后续章节的叙述, 我们将 adaptive ShiversSort 视为参数化算法: 除了要排序的数组之外, 该算法还需要一个正整数 $c$ 作为参数.

在随后的部分中, 我们大多数考虑 $c$ 的两种取值: $c = 1$ 或 $c = n + 1$, 其中 $n$ 是待排序数组的长度. 我们将选择这两个参数获得的算法分别称为 1-adaptive ShiversSort 和 length-adaptive ShiversSort. 除了在第 2.4 节专门提及 length-adaptive ShiversSort 的时候, 读者都可以假定 $c=1$.

该算法基于发现单调的 run 并维护存储这些 run 的堆栈, 这些堆栈可以根据是否情况 #1 至情况 #4 来进行合并或压入新的 run. 特别地, 因为这些条件只涉及到 $\mathcal l_1$, $\mathcal l_2$ 和 $\mathcal l_3$, 且只有 $R_1$, $R_2$, 和 $R_3$ 可能被合并, 所以如果 $c$ 的取值与输入无关, 这个算法就属于 $3$-aware 稳定排序算法.

```text
Algorighm 1: Adaptive ShiversSort
Input: 待排序数组 A, 整数参数 c$
Result: 数组 A 被排序成一个 run, 这个 run 留在栈里.
Note: 我们用 h 表示栈 S 的高度, 用 R_i 表示栈中第 i 高的 run. r_i 表示 R_i 的长度, 令 l_i = floor(log_2(r_i/c))

runs = A 的分解
S = 空栈
while true:                   # (主循环)
    if h >= 3 and l_1 >= l_3:       # case #1
        合并 R_2 和 R_3
    else if h >= 3 and l_2 >= l_3:  # case #2
        合并 R_2 和 R_3
    else if h >= 2 and l_1 >= l_2:  # case #3
        合并 R_1 和 R_2
    else if size(runs) > 0:         # case #4
        从 runs 中取出一个 R 并压入 S 中
    else break
while h >= 2: 合并 R_1 和 R_2
```

## 2.4 Adaptive ShiversSort 的最坏情况分析

我们在引言中提到过, adaptive ShiversSort 拥有极佳的最坏情况下的合并成本上界. 下面的结果对此进行了概述.

> _定理 3.1._ Adaptive ShiversSort 的合并成本上界是 $n(\mathcal{H} + \Delta)$, 其中 $\Delta = 24/5 - \log_2 5 \approx 2.478$.

本章的其余部分用来证明定理 3.1 的较弱的变体.

> _定理 3.2._ 对于任意的参数 $c$, adaptive ShiversSort 的合并成本上界是 $n(\mathcal H + 3 - \{\log_2(n/c)\}) - \rho - 1$, 其中 $\{x\} = x - \lfloor x\rfloor$ 表示实数 $x$ 的小数部分.

接下来, 参数 $c$ 的值会被固定下来. 然后, 我们用 $r$ 表示 run $R$ 的长度, 用整数 $\mathcal l$ 表示 $\lfloor\log_2(r/c)\rfloor$, 用 $\lambda$ 表示 $\{\log_2(r/c)\}$. 整数 $\mathcal l$ 称作 $R$ level. 特别地, 我们用 $(R_1, \dots, R_h)$ 表示栈, 其中 $R_k$ 是栈顶端第 $k$ 个 run, $r_k$ 表示 $R_k$ 的长度, $\mathcal l_k = \lfloor\log_2(r_k/c)\rfloor$.

现在我们证明两个关于 run 的 level 的辅助引理, 他们会贯穿整个算法.

> _引理 3.3._ 当两个 run $R$ 和 $R'$ 合并成一个 run $R''$后, 有 $\mathcal l'' \le \max\{\mathcal l, \mathcal l'\} + 1$ .

_证明:_ 不失一般性地, 假设 $r \le r'$. 则有

$$2^{\mathcal l''}c \le r'' = r + r' \le 2r' \lt 2\times 2^{\mathcal l' + 1}c = 2^{\mathcal l' + 2}c$$

因此 $\mathcal l''\le \mathcal l' + 1$.

> _引理 3.4._ 在算法执行过程中的任何时候, 如果栈为 $\mathcal S = (R_1, \dots, R_h)$, 则有
> $$\mathcal l_2 \le \mathcal l_3 \lt \mathcal l_4 \lt \dots \lt \mathcal l_h \tag 1$$

_证明:_ 利用数学归纳法. 首先, 当 $h \le 2$ 时, 无需证明: 这仅在算法开始时出现.
接下来我们证明如果 $\mathcal S = (R_1, \dots, R_h)$ 满足 $(1)$, 且合并或压栈操作之后新的栈为 $\overline {\mathcal S} = (\overline R_1, \dots, \overline R_{\overline h})$, 则 $\overline {\mathcal S}$ 仍满足 $(1)$. 这可以通过分情况讨论证明:

- 如果两个 run 被刚刚合并, 则当 $i \ge 3$ 时, 有 $\overline h = h - 1$, $\overline R_i = R_{i+1}$, 且 $\overline R_2 = R_3$ (如果 $R_1$ 和 $R_2$ 合并) 或 $\overline R_2$ 是 $R_2$ 和 $R_3$ 合并的结果. 由于引理 3.3, 可得 $\overline {\mathcal l}_2 = \mathcal l_3$ 或 $\overline{\mathcal l}_2 \le \max\{\mathcal l_2, \mathcal l_3\} + 1 = \mathcal l_3 + 1$. 又因为在 $\mathcal S$ 中 $(1)$ 成立, 所以有 $\mathcal l_3 \le \overline{\mathcal l}_3 \le \overline{\mathcal l}_4 \le \dots \le \overline{\mathcal l}_{\overline h}$, 所以 $\overline{\mathcal l}_2 \le \mathcal l_3 + 1 \le \overline{\mathcal l}_3$, 得出 $(1)$ 在 $\overline{\mathcal S}$ 中也成立.
- 如果 $\overline R_1$ 是刚刚压入的 run, 则对于 $i \ge 2$, 有 $\overline h = h + 1$, 且 $\overline R_i = R_{i-1}$. 因为 $\S$ 满足 $(1)$, 所以有 $\o\l_4 \lt \o\l_5 \lt \dots \lt \o\l_{\o h}$. 而且因为满足了情况 #4, 所以情况 #1 和 #3 都不满足, 所以 $\l_1 \lt \l_2 \lt \l_3$ 也就是 $\o\l_2 \lt \o\l_3\lt\o\l_4$, 故 $\o\S$ 也满足 $(1)$.

粗略的讲, 引理 3.4 说明了存储在栈中的 run 的长度以指数增长, 除了刚刚压入栈顶的元素长度是无法控制的.

接下来, 定理 3.2 的证明要点在于对算法执行的合并的总成本进行仔细的估计. 直观地讲, 该证明可以看作是对成本的分配, 其中两个 run $R$ 和 $R'$ 的合并成本需要由一些 run 承担 (包括 $R$, $R'$ 或其他的 run). 对于我们的证明来说, 如果把一次合并的所有成本都分给一个 run, 未免有点太粗糙. 因此, 我们把每一次合并产生的成本分为两半, 分别由不同的 run 来承担, 这样会更方便一些.

因此, 接下来我们将 $R$ 和 $R'$ 之间的合并人为地分为两个单独的合并操作: 一部分成本 $r$ 叫做 $R$ 对 $R'$ 合并的成本, 另一部分成本 $r'$ 叫做 $R'$ 对 $R$ 合并的成本.加起来, 合并 $R$ 和 $R'$ 的成本是 $r + r'$.

然后, 如果 R 和 R' 合并成更大的 R'' 时, $\l'' \ge \l + 1$, 那没就称 R 的合并是膨胀合并, 反之称作非膨胀合并. 注意, 如果 $\l \le \l'$, 则 $R$ 对 $R'$ 的合并一定是膨胀合并; 因此, 如果 $\l = \l'$, 则 R 和 R' 的合并都是膨胀合并, 在这种情况下称 R 和 R' 的合并是完全膨胀合并.

> _引理 3.5._ 膨胀合并所需的开销最多是 $n(\H - \{\log_2(n/c)\}) + \Lambda$, 其中 $\Lambda = \sum_{i=1}^\rho r_i\lambda_i$.

_证明:_ 算法运行时, 一个原始长度为 $r$ 的 run $R$ 中的每一个元素最多参与的膨胀合并次数等于

$$
\begin{aligned}
\lfloor\lognc\rfloor - \l &= (\lognc - \{\lognc\}) - (\log_2(r/c) - \lambda) \\
&= \log_2(n/r) + \lambda - \{\lognc\}
\end{aligned}
$$

所以, 如果数组最初被分成 $r_1,\dots,r_\rho$, 总的膨胀合并开销最大等于

$$
\sum_{i=1}^\rho r_i(\log_w(n/r_i) + \lambda_i - \{\lognc\}) = n(\H-\{\lognc\}) + \Lambda
$$

我们还需要证明非膨胀合并的总开销不多于 $3n - \Lambda - \rho$. 这需要根据触发这些合并的情况进一步拆分合并序列. 为了方便讨论, 我们将 #k 导致的更新称为 #k 更新, #k 导致的合并称为 #k 合并, #4 导致的压栈称为 #4 压栈. 下面证明了这些更新并不是随机排列的.

> _引理 3.6._ #2 合并后不会紧跟着 #1 合并, #3 合并后不会紧跟 #1 或 #2 合并.

*证明:* 设 $m$ 为一次合并. 合并前的栈是 $\mathcal S = (R_1, \dots, R_h)$, 合并后的栈是 $\overline {\mathcal S} = (\overline R_1, \dots, \overline R_{\overline h})$, 则 $\o h = h - 1$. 如果 $m$ 是 #2 合并, 则 $H \ge 3$, $\o R_1 = R_1$ 且 $\o R_3 = R_4$. 因为 #1 没有满足, 所以 $\l_3 > \l_1$. 由 $(1)$, $\o\l_3 = \l4\gt\l_3\gt\l_1=\o\l_1$, 因此不可能紧跟一个 #1 合并.

类似地, 如果 $m$ 是一个 #3 合并, 若要使后面紧跟一个 #1 合并或 #2 合并, 则必有 $\o h \ge 3$. 在这种情况下, $h \ge 4$, $\o R_2 - R_3$, $\o R_3 = R_4$, 且 $\o R_1$ 是 $R_1$ 和 $R_2$ 合并的结果. 另外, 因为没有执行 #1 合并或 #2 合并, 所以 $\l_3 \gt \max\{\l_1, \l_2\}$. 根据引理 3.3, $\o\l_1 \le \max\{\l_1, \l_2\} + 1 \le \l_3$. 因此, 由 $(1)$ 得
$$
\o\l_3 = \l_4 \gt \l_3 = \o\l_2 \gt \o\l_1, \tag 2
$$
这说明 $m$ 后不可能紧跟 #1 合并或 #2 合并. 

引理 3.6 产生的结果是,  



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
