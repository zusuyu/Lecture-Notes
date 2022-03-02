# PRIMES is in P

$\textsf{PRIMES} = \{w | w \text{ is binary representation of a prime number}\}$

$\text{binary}$ is not essential, but $\text{unary}$ is not allowed.

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

$\tilde{O}(f(n)) = O(f(n) \text{poly} \log f(n))$

$\mathbb F = \mathbb Z_p[x] / h(x)$

“模 $r$ 的阶”



## AKS correctness



根据有限域上分圆多项式那套理论，$x^r - 1$ 在 $\mathbb Z_p$ 下有一个 $o_r(p)$ 次不可约多项式因子 $h(x)$

$\mathbb F = \mathbb Z_p[x] / h(x)$ 是域，但我们不方便(或者说不能)显式求出 $p$ 和 $h(x)$ ，所以我们只在 $\bmod (x^r-1, n)$ 下做运算，并指出 $f \equiv g \bmod (x^r-1, n) \Rightarrow f \equiv g \mod (h(x), p)$

