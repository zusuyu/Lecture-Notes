# BOOSTING THE CERTIFIED ROBUSTNESS OF L-INFINITY DISTANCE NETS

这篇文章主要介绍了在基于 L-inf 距离的网络下如何提升 certified robustness.

robustness 是机器学习中一件需要着重考虑的事情, 感性地说就是对于一个小的增量 $\delta$, 神经网络对于输入 $x$ 和 $x + \delta$ 的输出 $f(x)$ 和 $f(x + \delta)$ 应该差不太多.

单从这一篇文章看 L-inf 网络的引入有些突兀, 大概是在此之前就有一些良好的基础. 粗略地说其就是一个运算基于无穷范数距离的神经网络.

命题 2.2 指出 L-inf 网络在无穷范数下是满足 1-Lipschitz 连续的. 这使得其天生地拥有了良好的 certified robustness 属性. 这是因为 1-Lipschitz 连续保证了 $\|f(x + \delta) - f(x)\| \leq \|\delta\|$, 这个差被 $\delta$ 的范数 bound 住了.

论文中定义了 margin. 考虑做分类的网络 $g$, 它对于输入 $x$, 输出的分类是 $\argmax_{\hat y \in [K]} [g(x)]_{\hat y}$, 定义数据 $(x, y)$ 在 $g$ 下的 margin 为 $\text{margin}(x, y; g) = [g(x)]_y - \max_{j \neq y}[g(x)]_j$ 即正确分类时输出最大值与次大值的差.

定义了 margin 后我们可以发现 large margin + 1-Lipschitz 连续 可以说明 certified robustness.

## L-inf 网络在 robust classification 下的表达能力

首先定义了 r-seperation 的概念, 然后指出只要一个大小为 n 的数据集 D 满足 r-seperation, 就存在一个大小为 O(n) 的 L-inf 网络可以 100% 把这个数据集分对, 说明表达能力是没有问题的(...?) (证明方式是构造性证明)

## L-inf 网络的训练

L-inf 网络的训练听上去很抽象, 因为可以想象得到做 gradient descent 的时候, 梯度是非常稀疏的.

提了一个用 hinge loss 训练的办法, 但是寄了.

发现问题在于用 L-p relaxation (为啥叫这名儿) 的时候, 虽然解决了稀疏梯度的问题, 但是破坏了 Lipschitz 连续的条件.

具体来说, 选用 loss function 为 $\mathbb E_{(x, y) \in D} [\max\{\theta - \text{margin}(x, y; g), 0\}]$. 其中 $\theta$ 取一个比 $2\varepsilon$ 要大的数. 

这里采用的方式是在训练开始时取一个很小的 $p (=8)$ 然后在 L-p 下算梯度, 但是命题 4.1 指出这时候 Lipschitz 常数是 $d^{1/p}$ 级别的, 也就是说小的 $p$ 会导致大的 Lipschitz 常数, 从而虽然可以有 large margin, 但 certified robustness 并不能保证.

然后想到了用另外一种 loss function: cross entropy. 但是 cross entropy 只能粗略地减小 margin, 并不能精确地优化 $\mathbb P_{(x, y) \in D}[\text{margin}(x, y; g) / 2 \le \varepsilon]$. 所以最终做法是把两者结合起来.

$\mathcal L(g, D; \theta) = \mathbb E_{(x, y) \in D}[\lambda\ell_{CE}(s \cdot g(x), y) + \min\{\ell_{hinge}(g(x) / \theta, y), 1\}]$

+ (scaled) cross entropy 中额外乘了一个 scaling factor $s$, 这是因为 L-inf 网络是 1-Lipschitz 连续的, 因此无法在训练中学到这样的缩放系数
+ (clipped) hinge loss 额外搞了个和 $1$ 取 $\min$ 的操作, 等价于是让那些分错的 sample (margin 为负) 的 hinge loss 固定为 $1$ 而不是继续增大. 这么做的原因有三:
    - cross entropy 已经能干“正确分类”的事情了, 不需要 hinge loss 再去插一脚
    - 在训练的后期有些分错的 sample 已经没救了, clipped hinge loss 可以不去管它们而专注于提升那些分对了的 sample 的 margin
    - clipped hinge loss 是 $\mathbb P_{(x, y) \in D}[\text{margin}(x, y; g) / 2 \le \varepsilon]$ 的一个更好的表示
+ 结合系数 $\lambda$, 随着 $p$ 的增大而减少.