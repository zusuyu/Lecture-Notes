# PRIMES is in P

$\textsf{PRIMES} = \{w | w \text{ is binary representation of a prime number}\}$

具体几进制无所谓，只要不是 $\text{unary}$ 就行了。

$\textsf{PRIMES}$ is in 

+ $\textbf{EXP}$, which is known to all
+ $\textbf{coNP}$, since $ \overline{\textsf{PRIMES}} = \textsf{COMPOSITES}$ is in $\textbf{NP}$
+ $\textbf{NP}$, Lucas Primality Test [Pratt75]
+ $\textbf{BPP}$, [AB99]
+ $\textbf{P}$, [AKS02]

### Partt75

**Lucas Primality Test**: a positive integer $n \ge 3$ is a prime number iff there exists an integer $1 < a < n$ such that $a^{n-1} \equiv 1 \bmod n$ and for every prime factor $q$ of $n-1$, $a^{(n-1)/q} \not\equiv 1 \bmod n$.

**Proof: ** $\Leftarrow: $ consider $o_n(a)$, $\Rightarrow:$ consider the primitive root of $n$

通过递归，可以在 NDTM 上把 $n-1$ 的所有质因子全都搞出来

具体细节就不讲了，只需要指出得到的算法复杂度是 $O(\text{poly} \log n)$

### Generalization of Fermat Little Thm.

a positive integer $n \ge 2$ is prime iff there exists $a \in \mathbb Z$ coprime to $n$ such that $(x + a)^n = x^n + a \bmod n$

(freshman's dream)

**Proof: ** $n$ is prime $\Rightarrow$ all coefficients $\equiv 0 \bmod n$; $n$ is composite $\Rightarrow$ $p^k \| n$, then $p^k \nmid \binom np$ since there're only $k-1$ $p$-factors in $\binom np$.

**AB99:** 随机取 $a$ 和 $r(x)$ 作为模数多项式



## Notations & Preliminaries

$\tilde{O}(f(n)) = O(f(n) \text{poly} \log f(n)) = o(f^{1 + \varepsilon}(n))\ (\forall \varepsilon > 0)$ 

$\mathbb F = \mathbb Z_p[x] / h(x)$

“模 $r$ 的阶”



## AKS correctness

+ 先把 $n = a^b$ 这种情况干掉
+ 找最小的 $r$ 满足 $(r, n) = 1$ 且 $o_r(n) > \log^2n$
+ 判断 $n$ 有没有不超过 $r$ 的非平凡因子
+ 如果 $n \le r$ 通过了第三步测试，则 $n$ 是质数
+ 对每个 $1 \le a \le l = \lfloor \sqrt{\varphi(r)}\log n \rfloor$，检查是否 $(x + a)^n \equiv x^n + a \bmod (x^r-1, n)$
+ 第五步寄了说明 $n$ 是合数，否则 $n$ 是质数

【这是算法伪代码】



*关于 $r$ 的存在性以及上界限制将被安排在下一节中讨论*

对于到达了第五步的 $n$ ，它一定有一个大于 $r$ 的质因子 $p$

根据有限域上分圆多项式那套理论，$x^r - 1$ 在 $\mathbb Z_p$ 下有一个 $o_r(p)$ 次不可约多项式因子 $h(x)$

$\mathbb F = \mathbb Z_p[x] / h(x)$ 是域，但我们不方便(或者说不能)显式求出 $p$ 和 $h(x)$ ，所以我们只在 $\bmod (x^r-1, n)$ 下做运算，并指出 $f \equiv g \bmod (x^r-1, n) \Rightarrow f \equiv g \mod (h(x), p)$

其实 $\deg h(x) = o_r(p)$ 的具体数值无关紧要，我们只需要知道 $o_r(p) > 1$ 就可以了。 我们采用的技术只保证了 $o_r(n)$ 足够大，而 Hendrik Lenstra 的工作简化了证明，告诉我们保证 $o_r(n)$ 足够大其实就够了。

### Introspectivity: Generalization of Freshman's Dream

我们直接在 $\mathbb F = \mathbb Z_p[x] / h(x)$ 下考虑问题。

称 $m \in \mathbb Z$ 对 $f \in \mathbb F$ 内省，如果 $f^m(x) = f(x^m)$。

惊喜地发现“内省”对数乘和多项式乘都是封闭的，所以只要我们知道了两个小集合 $S_1 \subseteq \mathbb Z, S_2 \subseteq \mathbb F$ 之间存在两两内省关系，那么由 $S_1, S_2$ 分别作为生成元得到的大集合之间也就存在两两的内省关系。

我们知道 $p, \frac np$ 对 $x, x + 1, \cdots, x + l$ 内省，从而 $I = \langle p, \frac np \rangle$ 对 $P = \langle x, x + 1, \cdots, x + l \rangle$ 内省。因为 $x^r \equiv 1 \bmod x^r-1$，所以可以对 $I$ 中元素模 $r$ 得到有限群 $G \leqslant \mathbb Z_p^*$；同理 $P$ 中元素模 $h(x)$ 得到有限群 $\mathcal G \leqslant \mathbb F$。



接下来做的事情是对 $|\mathcal G|$ 进行上下界估计产生矛盾，从而说明通过第五步的 $n$ 一定是质数。



### upperbound & lowerbound of $|\mathcal G|$

**Lem. ** $|\mathcal G| \ge \binom{|G| + l}{|G| - 1}$

**Proof. ** 我们证明 $P$ 中两个不同的小于 $|G|$ 次多项式，在对 $(h(x), p)$ 取模后仍不同。反证，假设 $f, g$ 相同，那么根据 introspectivity ，$f - g$ 代入所有 $x^m$ 后模 $(h(x), p)$ 的结果都是 $0$ ，其中 $m \in I$。这说明如果我们把 $f -g$ 看成 $\mathbb F[y]$ 上的多项式的话，$f - g$ 在 $\mathbb F$ 上就有至少 $|G|$ 个根，而 $\deg (f - g) < |G|$ ，产生了矛盾，故反证假设不成立。

*虽然 $\deg (f - g) < |G|$ 的结论是在把 $f - g$ 看成 $\mathbb Z[x]$ 上多项式得到的，但这是没关系的*

因此 $|\mathcal G| \ge $ $P$ 中次数小于 $|G|$ 的多项式个数。因为 $P$ 的生成元有 $l + 1$ 个，小于 $|G|$ 的次数可以任意分配，因此个数等于不定方程 $\sum_{n=0}^{l}x_n < |G|$ 的解数，也就是把 $|G| - 1$ 个相同的球放入 $l + 2$ 个不同的盒子(多一个表示要扔掉的)的方案数，隔板法可知就是 $\binom{|G| + l}{|G| - 1}$

**Remark. ** 一个直观的结论是 $|\mathcal G| \ge \binom{o_r(p) + l}{o_r(p) - 1}$，因为 $o_r(p) = \deg h(x)$。Hendrik Lenstra 提出了这个引理，把对 $o_r(p)$ 的限制改成了对 $o_r(n)$ 的限制。



**Lem. ** 任意域的有限乘法子群都是循环群。

**Proof. ** 分析元素的阶数即可。

**Lem. ** 取 $a, b \in I$，若 $a \equiv b \bmod r$，那么 $|\mathcal G| \mid |a - b|$。

**Proof. ** $a \equiv b \bmod r \Rightarrow x^a \equiv x^b \bmod h(x) \Rightarrow g(x^a) = g(x^b) \Rightarrow g^a(x) = g^b(x) \Rightarrow g^{|a-b|}(x) = \mathbf 1(x) \Rightarrow o(g) \mid |a-b|$

$g$ 是可任取的，那么取 $g$ 为 $\mathcal G$ 的生成元，就有 $|\mathcal G| \mid |a - b|$。

**Lem. ** 如果 $n$ 不是 $p$ 的整数次幂，那么 $|\mathcal G| \le n^{\lfloor \sqrt{|G|} \rfloor}$

**Proof. ** 考虑集合 $\{(\frac np)^ip^j | 0 \le i, j \le \lfloor \sqrt {|G|}\rfloor \}$ ，如果 $n$ 不是 $p$ 的整数次幂，那么这个集合内的数是两两不同的，同时一定存在两个数之差是 $r$ 的倍数(因为集合大小超过了 $|G|$，一定有两个数被映到 $G$ 中的相同元素)，根据前面的引理，可知 $|\mathcal G| \le $ 两数之差 $\le n^{\lfloor \sqrt{|G|} \rfloor}$



之后只需要说明这个上下界冲突了，从而导致 $n$ 一定是 $p$ 的整数次幂。这种情况下 $n$ 不是质数的已经被第一步判掉了，所以 $n$ 就是质数。



## AKS Complexity

咕咕咕，明天写