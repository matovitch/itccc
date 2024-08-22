# Information Theory: A Constructive Crash Course

This text aims at intuitively explaining and proving the main definitions and theorems of Shannon's information theory with limited prerequisites; this includes: cross/conditional entropy, KL-divergence, mutual information, asymptotic codelength property (variant of [AEP](https://en.wikipedia.org/wiki/Asymptotic_equipartition_property)), source and channel coding theorems. It contains a new *constructive* proof of the coding theorem for discrete memoryless channel and an other non constructive proof of achievability of Shannon's capacity for the bit-flip channel.

The target audience is "undergraduates/good high school students" but anyone is welcome here.

## Find the coin

As a playful [formalist](https://en.wikipedia.org/wiki/Formalism_(philosophy_of_mathematics)), I would like to introduce the subject with a small game. ;)

A coin is hidden among four opaque cups. At each round, we ask yes/no questions to uncover the cup concealing the coin. Multiple rounds are played and we aim to minimize the number of questions asked.

We first assume the coin has $1$ in $4$ chances to be under each cup such that the best strategy is a [binary search](https://en.wikipedia.org/wiki/Binary_search_algorithm) asking $2$ questions per round.

But after playing a few rounds, we notice the coin has in fact $1$ in $2$ chance to be in the first cup, $1$ in $4$ to be in the last one and $1$ in $8$ to be in the others. If we update our questionnaire asking: "Is the coin in the first cup? Is the coin in the last cup? Is the coin in this remaining cup?” we will ask on average only $\frac{1}{2}+\frac{1}{4}\times2+\left(\frac{1}{8}+\frac{1}{8}\right)\times3=1.75$ questions per round.

Now imagine playing the same game using only two cups with a $70\%$ chance for the coin to be in the first cup and $30\%$ chance to be in the other. Here, we must ask one question per round.

But let's change the rules of the game allowing questions relative to two simultaneous rounds. There is a $49\%$ chance for the two coins to be in their first cups, $9\%$ chance for them to be in their last cups and $21\%$ for the two other cases. The best strategy will ask $\frac{1}{2}(0.49+0.21\times2+(0.21+0.09)\times3)=0.905$ questions per coin.

## Entropies and KL divergence

Given $n$ cups and their probabilities to contain the coin $p_{1},\dots,p_{n}$, as we increase the number of simultaneous rounds, the number of questions per coin of the best strategy will converge to,
$$-\sum_{i=1}^{n}p_{i}\log_{2}\left(p_{i}\right)$$

where $\log_{2}$ is the [binary logarithm](https://en.wikipedia.org/wiki/Binary_logarithm).

This is the *entropy* of the [distribution](https://en.wikipedia.org/wiki/Probability_distribution) $p$ and is denoted $H(p)$. If a single-round optimal strategy exists, we can view it as a decision tree: $-\log_{2}\left(p_{i}\right)$ is the length of the branch we will follow with probability $p_{i}$ when the coin is found in the $i_{^{th}}$ cup.

Now if we assume the wrong distribution $q$, the number of questions we ask on average is:
$$-\sum_{i=1}^{n}p_{i}\log_{2}\left(q_{i}\right)$$

This is the *cross-entropy* $H(p,q)$ and it is not symmetric. As expected:
$$H(p,q) \geq H(p)$$

The difference is called *Kullback-Leibler divergence*, we note:
$$D_{KL}(p\Vert q)=H(p,q)-H(p)$$

In the rest of the text we will drop the $2$ from $\log_{2}$ for simplicity.

## Conditional entropies

Let $X$ and $Y$ be two discrete [random variables](https://en.wikipedia.org/wiki/Random_variable), we will write the [conditional probabilities](https://en.wikipedia.org/wiki/Conditional_probability) as,
$$P(X,Y = x,y) = P(X = x)P(Y = y|X = x)$$

hence,
$$\log P(X,Y = x,y) = \log P(X = x)+\log P(Y = y|X = x)$$

and the [expected value](https://en.wikipedia.org/wiki/Expected_value) operator being linear,
$$\mathbb{E}_{x,y}\left[\log P(X,Y = x,y)\right]=\mathbb{E}_{x,y}\left[\log P(X = x)\right]+\mathbb{E}_{x,y}\left[\log P(Y = y|X = x)\right]$$

we will note,
$$H(XY)=H(X)+H(Y|X)$$

and:
$$\mathbb{E}_{y}\left[\log P(Y = y|X = x)\right] = H(Y|X = x)$$

With a similar reasonning, given $X_{1},\dots,X_{n}$ discrete random variables:
$$H(X_{1}\dots X_{n})=\sum_{i=1}^{n}H\left(X_{i}|X_{j\lt i})\right)$$

# Mutual information

Given two discrete random variables $X$ and $Y$ we have, $H(X)+H(Y)-H(XY)=D_{KL}(P(X,Y)\Vert P(X)\otimes P(Y))\geq 0$ where $\otimes$ denotes the [outer product](https://en.wikipedia.org/wiki/Outer_product).

This quantity is called the *mutual information* $I(X, Y)$.

It can be equivalently expressed as:
$$
\begin{align*}
I(X, Y) & =H(XY) - H(X|Y) - H(Y|X)\\
 & = H(X) - H(X|Y)\\
 & = H(Y) - H(Y|X)
\end{align*}
$$

This is left as an exercise but rest assured from that point on we will prove everything.

## Source coding theorem (lower bound)

For a discrete random variable $X$, a binary encoding $b$ of $X$ is an injection from the domain of $X$ to the set of binary strings. Noting $B_X=b(X)$ and $B_{X}^{i}$ the binary random variable given by the $i_{^{th}}$ bit when the length $\overline{B_X} \geqslant i$. For a such binary encoding we have:
$$\mathbb{E}[\overline{B_X}]\geqslant H(X)$$

Indeed, since $b$ is injective we get:
$$
\begin{align*}
H(X) & =H(B_X)\\
 & =\sum_{i=1}^{\max\overline{b}}P(\overline{B_X}=i)H(B_X|\overline{B_X}=i)\\
 & =\sum_{i=1}^{\max\overline{b}}P(\overline{B_X}=i)\sum_{j=1}^{i}H\left(B_X^{j}|\overline{B_X}=i \land B_{X}^{k\lt j}\right)\\
 & \leq\sum_{i=1}^{\max\overline{b}}P(\overline{B_X}=i)\times i\\
 & \leq\mathbb{E}[\overline{B_X}]
\end{align*}
$$

Using the fact that for any $B$ binary random variable $H(B)\leq 1$.

## Source coding theorem (upper bound)

Given $\epsilon>0$ and $n>1$, noting $X^n$ a sequence of $n$ random variables from a same distribution, there exists a binary encoding $B_{X^n}$ such that:
$$nH(X)\leq\mathbb{E}\left[\overline{B_{X^n}}\right]\leq n\left(H(X)+\epsilon\right)$$

From the conditional entropy formula we have $H(X^{n})=nH(X)$, so the left inequality is given by our previous result.

For the right hand side, we first notice that given some naturals $l_{1}\leq\dots\leq l_{n}$ where $\sum2^{-l_{i}}\leq1$ we can build binary strings of lengths $l_{1},\dots,l_{n}$ such that no string is the prefix of another.

Indeed, $1-\sum2^{-l_i}$ is a multiple of $2^{-l_n}$, such that adding $2^{l_{n}}-\sum2^{l_{n}-l_{i}}$ times $l_{n}$ to our set of lengths we get $\sum 2^{-l_i} = 1$. Then, if we prune an infinite binary tree at these lengths, the set of paths from the root to the leafs described using left/right instructions will form a valid *prefix-distinct* set of binary strings.

Now if we pick the lengths as $\left\lceil -\log_{2}P(X^{n}=(x_{1},\dots,x_{n}))\right\rceil$ then:
$$
\begin{align*}
\mathbb{E}\left[\overline{B_{X^n}}\right] & =\sum P(X^{n}=(x_{1},\dots,x_{n}))\left\lceil -\log_{2}P(X^{n}=(x_{1},\dots,x_{n}))\right\rceil \\
 & \leq\sum P(X^{n}=(x_{1},\dots,x_{n}))(1-\log_{2}P(X^{n}=(x_{1},\dots,x_{n})))\\
 & \leq n\left(H(X)+\frac{1}{n}\right)
\end{align*}
$$

And we obtain the right inequality for $n>1/\epsilon$.

## Asymptotic Codelength Property (ACP)

Given $n > 1$, some sequence of $n$ discrete random variables $X^n$ and some logical statement $S({X^n})$, we will say that $S(X^n)$ holds almost surely asymptotically (a.s.a.) if we have:
$$\lim_{n\to\infty}P(S(X^n))=1$$

Let $m > 1$, using previous notation, we can encode $X^{nm} =(X^n_{1},\dots,X^n_{m})$ as $B_{X^{nm}}=B_{X^n_1}\dots B_{X^n_m}$ since the encoding $B_{X^n}$ is *prefix-distinct*.

With the [weak law of large numbers](https://en.wikipedia.org/wiki/Law_of_large_numbers#Weak_law) we get a.s.a.,
$$\left|\frac{1}{m}\overline{B_{X^{nm}}} - \mathbb{E}\left[\overline{B_{X^n}}\right]\right|\leq 1$$
using the last inequality of the previous section and $\mathbb{E}[\overline{B_{X^{n}}}]\geqslant nH(X)$ a.s.a.,
$$n\left(H(X)-\frac{1}{n}\right) \leq\frac{1}{m}\overline{B_{X^{nm}}} \leq n\left(H(X)+\frac{2}{n}\right)$$
and with $n=m$ and $k=n^2$, using [Landau's notation](https://en.wikipedia.org/wiki/Big_O_notation#Family_of_Bachmann%E2%80%93Landau_notations) we get a.s.a.:
$$\overline{B_{X^{k}}} \in \left[kH(X)\pm o(k)\right]$$

## ACP for conditional encodings

Given a discrete random variable $Y$ and using the distribution $P(Y=y|X=x)$ in place of $P(X=x)$ we can speak of the conditional encoding $B_{Y|X=x}$.

If we similarly derive the ACP for conditional encodings, we get the existence of an encoding $B_{Y^n|X^n=x^n}$ such that a.s.a. $\overline{B_{Y^n|X^n=x^n}} \in \left[H(Y^n|X^n=x^n)\pm o(n)\right]$.


Given $m > 1$, we can encode a sequence $(x^n_1,y^n_1,\dots,x^n_m,y^n_m)$ as $B_{X^{nm}}B_{Y^n|X^n=x^n_1}(y^n_1)\dots B_{Y^n|X^n=x^n_m}(y^n_m)$ triming the prefix $B_{X^{nm}}$ we have,

$$\overline{B_{Y^n|X^n=x^n_1}(y^n_1)\dots B_{Y^n|X^n=x^n_m}(y^n_m)}=\sum_{i=1}^{m}\overline{B_{Y^n|X^n=x^n_i}(y^n_i)}$$

using the weak law of large numbers as before we get a.s.a.
$$\frac{1}{m}\sum_{i=1}^{m}\overline{B_{Y^n|X^n=x^n_i}(y^n_i)}\in\left[ nH(Y|X)\pm o(n) \right]$$


and with $n = m$ and $k = n^2$ it means we can encode $(x^k,y^k)$ as $B_{X^k}(x^k)B_{Y^k|X^k=x^k}(y^k)$ such that a.s.a $\overline{B_{X^{k}}(x^k)} \in \left[kH(X)\pm o(k)\right]$ and 
$\overline{B_{Y^{k}|X^{k}=x^k}(y^k)} \in \left[kH(Y|X)\pm o(k)\right]$.

## Prefix tree rebalancing

$B_{Y^{k}|X^{k}=x^k}$ is a *prefix distinct* encoding as concatenation of *prefix distinct* encodings.

Given $\epsilon > 0$, we can then perform a rebalacing of the *prefix distinct* tree defining $B_{Y^{k}|X^{k}=x^k}$ as follow:

1. if $\overline{B_{Y^{k}|X^{k}=x^k}(y^n)}<k(H(Y|X) - \epsilon)$ pad it with ones to length $k(H(Y|X) - \epsilon)$
1. use the new internal nodes with a single child to graft branches pruned from length $k(H(Y|X) - \epsilon)$

We repeat these two operations till the level $k(H(Y|X) - \epsilon)$ of the binary tree is "full".

We can bound the increase in expected length from the padding step by $P(\overline{B_{Y^{k}|X^{k}=x^k}(y^n)}<k(H(Y|X) - \epsilon))H(X|Y)k=|o(k)|$, and the grafting step followed by the second padding step only decrease the expected length (inductively for following iterations).

This rebalancing procedure terminates since $H(B_{Y^{k}|X^{k}=x^k}) = H(Y^k|X^k=x^k) = kH(Y|X) + o(k)$.

Since this is valid for any $\epsilon > 0$ we can pick a suitable $o(k)$ such that $\epsilon = |o(k)|/k$.

<!--the original encoding we have:
$$H(B_{X^k}B_{Y^k|X^k=x^k})=H(X^kY^k)$$

using conditional probabilities and the positivity of mutual information we get,
$$H(B_{X^k})+H(B_{Y^k|X^k=x^k})\geq H(X^k)+H(Y^k|X^k)$$

with $H(B_{X^k})=H(X^k)$ we have,
$$H(B_{Y^k|X^k=x^k})\geq kH(Y|X)$$

Contradiction.-->

## Unification of prefix encodings using branch permutations

After the rebalancing we can view the prefix from $B_{Y^{k}|X^{k}=x^k}$ of length $kH(Y|X) - |o(k)|$ as defining a family of surjections indexed by $x^k$ from the domain of $Y^k$ to binary strings of said length.

We can then consider the right inverses $g_{x^k}$ (note we don't need the axiom of choice since all sets are finite) such that $\text{prefix}_{kH(Y|X) - o(k)}(B_{Y^{k}|X^{k}=x^k})\circ g_{x^k}=B_{Y^k|X^k}$.

Note that $B_{Y^k|X^k}$ is a function of $y^k$ that does not depend upon the realisation of $X^k$. Using $H(B_{Y^k|X^k=x^k})=kH(Y|X)+o(k)$ we have $H(B_{Y^k|X^k}) = kH(Y|X)-|o(k)|$.

Similarly, the suffix $B'_{Y^k|X^k=x^k}$ has an entropy of $|o(k)|$. We are now ready to dive into the channel coding theorem.

## Channel capacity

A communication channel is modeled by a source of input messages $X$ transmitted and received as output messages $Y$ on the other end of the channel. The conditional distribution $P(Y|X)$ defines the reliability of the channel.

Given a fixed conditional distribution $P(Y|X)$, we can try to modify the distribution of the input messages $P(X)$ to maximize the mutual information $I(X,Y)$. This maximal mutual information is called the *capacity* of the channel.

## Channel coding theorem

Given $n$ pairs of discrete random variables sampled from a joint probability distribution, we note $X^n$ and $Y^n$ the deinterlaced sequences.

Let $(e_n)$ and $(d_n)$ be sequences of functions such that a.s.a. $e_n(X^n)=d_n(Y^n)$. If we note,
$$\begin{align*}
h_{e,d} & =\sup_{n}\frac{1}{n}H(e_{n}(X^{n}))\\
& =\sup_{n}\frac{1}{n}H(d_{n}(Y^{n}))
\end{align*}$$

then:
$$\sup_{e,d}h_{e,d}=I(X,Y)$$

## Channel coding proof (achievability)

For the rest of the proof we will note $C=I(X, Y)$. Let $\epsilon > 0$ and $n > 1$. We start by building the "(en/de)coders" $e_n$ and $d_n$.

For each $x^n$, we first go throught the channel sampling $\widetilde{y^n}$ and define $e_n(x^n) = \text{prefix}_{n(C-\epsilon)}(B_{Y^n|B_{Y^n|X^n}}(\widetilde{y^n}))$. For $d_n$, we will simply use $\text{prefix}_{n(C-\epsilon)} \circ B_{Y^n|B_{Y^n|X^n}}$.

Note from previous results we have $\overline{B_{Y^n|B_{Y^n|X^n}}} = nC - |o(n)|$, such that a.s.a. the $n(C-\epsilon)$ bits prefixes are defined.

We then remark we can encode $(X^n,Y^n)$ as $B_{Y^n|X^n}B_{Y^n|B_{Y^n|X^n}}B_{X^n|Y^n}B'$ with $H(B')=|o(n)|$ as concatenation of the various suffixes.

And we can recover $X^n$ from the suffix $B_{Y^n|B_{Y^n|X^n}}B_{X^n|Y^n}B'$ since prepending it with $B_{Y^n|X^n}$ recovers $Y^n$, so we have,
$$
\begin{align*}
H(B_{Y^n|B_{Y^n|X^n}}B_{X^n|Y^n}B'|X^n)&=H(B_{Y^n|B_{Y^n|X^n}}B_{X^n|Y^n}B') - H(X^n) \\
&\leq H(B_{Y^n|B_{Y^n|X^n}}) + H(B_{X^n|Y^n}) +H(B') - H(X^n)\\
&\leq nC - |o(n)| + nH(X|Y)-|o(n)| + |o(n)| - nH(X)\\
\mathbb{E_{x^n}}[H(B_{Y^n|B_{Y^n|X^n}}B_{X^n|Y^n}B'|X^n=x^n)]&\leq|o(n)|
\end{align*}$$

discarding the suffix $B_{X^n|Y^n}B'$ only decreases the entropy hence,
$$\mathbb{E_{x^n}}[H(B_{Y^n|B_{Y^n|X^n}}|X^n=x^n)]\leq|o(n)|$$

that is a.s.a. we have,
$$H(B_{Y^n|B_{Y^n|X^n}}|X^n=x^n) \leq n\epsilon/2$$

and since $\overline{B_{Y^n|B_{Y^n|X^n}}} = nC - |o(n)|$ then  $B_{Y^n|B_{Y^n|X^n}}(\widetilde{y^n}$) and $B_{Y^n|B_{Y^n|X^n}}(y^n)$ a.s.a. share a prefix of $n(C-\epsilon)$ bits.

<!--
We remark that we can encode $(X^n,Y^n)$ as $B_{Y^n|X^n}B_{Y^n|B_{Y^n|X^n}}B_{X^n|Y^n}B'$ with $H(B')=|o(n)|$ as concatenation of the various suffixes.

We can recover $X^n$ from $B_{Y^n|B_{Y^n|X^n}}B_{X^n|Y^n}B'$ since prepending $B_{Y^n|X^n}$ recovers $(X^n,Y^n)$ and $H(B_{X^n|Y^n}B')=nH(X|Y)+|o(n)|$ so $H(B_{Y^n|B_{Y^n|X^n}})\geq nC-|o(n)|$.

This result together with the ACP ensures we can a.s.a. get a prefix of $n(C-\epsilon)$ bits and that $e_n$ and $d_n$ are properly defined.

And the images of $\widetilde{y^n}$ and $y^n$ by the prefix $B_{Y^n|B_{Y^n|X^n}}$ of this encoding of $X^n$ must a.s.a. match on a prefix of size $n(C-\epsilon)$ since they are both sampled from $P(Y^n|X^n=x^n)$.
-->
## Channel coding proof (optimality)

By contradiction, using [Landau's notation](https://en.wikipedia.org/wiki/Big_O_notation#Family_of_Bachmann%E2%80%93Landau_notations), let's assume there is a $d_n$ such that,
$$I(d_n(Y^n), X^n)-nC=|\Omega(n)|$$

Since $H(X^nY^n)=H(X^n)+H(B_{Y^n|X^n})+o(n)$, we would have,
$$I(B_{d_n(Y^n)|B_{Y^n|X^n}}, X^n)-nC=|\Omega(n)|$$

but since:
$$
\begin{align*}H(Y^{n}) & \geq H(B_{d_n(Y^n)|B_{Y^n|X^n}}B_{Y^n|X^n})\\H(Y^{n}) & \geq H(B_{d_n(Y^n)|B_{Y^n|X^n}})+H(B_{Y^n|X^n})\\nC+o(n)& \geq H(B_{d_n(Y^n)|B_{Y^n|X^n}})\end{align*}
$$

Contradiction.

## Bonus 1: capacity of the bit flip channel

Inputs and outputs of this channel take binary values and the conditional probabilities are,
- $P(Y=0|X=1)=p$
- $P(Y=1|X=0)=p$

where $0\leq p\lt \frac{1}{2}$ and the two missing conditional probabilities can be deduced.

We can bound the mutual information:
$$
\begin{align*}
I(X,Y) & =-H(Y|X)+H(Y)\\
 & =p\log_{2}p+(1-p)\log_{2}(1-p)+H(Y)\\
 & \leq p\log_{2}p+(1-p)\log_{2}(1-p)+1
\end{align*}
$$

And the bound is reached when $P(Y=0)=1/2$ that is when $P(X=0)=1/2$.

From now on, we will note $h(p)=-p\log_{2}p-(1-p)\log_{2}(1-p)$.

## Bonus 2: proof of achivability for the bit flip channel

The idea of the proof consists in taking a random set of binary strings of given length $n$ and to show that if this set contains less than $2^{n(C-\epsilon)}$ strings, for any $\epsilon>0$, we will be almost certain to recognize the strings after a transmission as $n$ goes to infinity.

Let's assume $\eta>0$ is our targeted probability of error. If we transmit a random string of length $n$ chosen among a set of size $2^{nR}$, the number of bit flips $F$ will follow a binomial distribution with parameters $n$ and $p$.

With [Chebyshev's inequality](https://en.wikipedia.org/wiki/Chebyshev%27s_inequality#Probabilistic_statement) we get:
$$
P\left(|F-np|\geq k\sqrt{p(1-p)n})\right)\leq\frac{1}{k^{2}}
$$

Let's pick $k$ such that $1/k^{2}<\eta/2$.

Now assuming $|F-np|\lt k\sqrt{p(1-p)n}$, we must find a condition on $R$ such that the probability of having an other transmitted string in a radius of $np+k\sqrt{p(1-p)n}$ flips is lower than $\eta/2$.

Since the set of strings is chosen uniformly at random, the number of flips $F'$ from the original transmitted string to any other transmitted string will follow a binomial distribution of parameters $n$ and $1/2$. So we need:
$$
p_{error}=1-\left(1-P\left(F'\lt np+k\sqrt{p(1-p)n}\right)\right)^{2^{nR}-1}\leq\frac{\eta}{2}
$$

Using the fact that for $n>1$, $1-(1-x)^{n}$ is below its tangent $nx$ at $0$:
$$
\begin{align*}
p_{error} & \leq(2^{nR}-1)\times P\left(F'\lt np+k\sqrt{p(1-p)n}\right)\\
 & \leq(2^{nR}-1)\times\sum_{i=0}^{np+o(n)}\binom{n}{i}2^{-n}\\
 & \leq2^{n(R-1)}\sum_{i=0}^{np+o(n)}\binom{n}{i}\\
 & \leq2^{n(R-1)}(np+o(n))\binom{n}{np+o(n)}
\end{align*}
$$

Indeed, since $p<1/2$ for $n$ large enough, we have for all $i\lt np+o(n)$:
$$
\binom{n}{i}\lt\binom{n}{np+o(n)}
$$

Now, as a small interlude, let's prove for $k\leq n$, $\binom{n}{k}\leq2^{nh\left(\frac{k}{n}\right)}$:
$$
\begin{align*}
\left(\frac{k}{n}+1-\frac{k}{n}\right)^{n} & =1\\
\sum_{i=0}^{n}\binom{n}{i}\left(\frac{k}{n}\right)^{i}\left(1-\frac{k}{n}\right)^{n-i} & =1\\
\binom{n}{k}\left(\frac{k}{n}\right)^{k}\left(1-\frac{k}{n}\right)^{n-k} & \leq1\\
\binom{n}{k} & \leq2^{nh\left(\frac{k}{n}\right)}
\end{align*}
$$

For our main inequality, we get,
$$
\begin{align*}
p_{error} & \leq2^{n(R-1)}(np+o(n))2^{nh(p+o(1))}\\
 & \leq(np+o(n))2^{n(R-(1-h(p+o(1)))}
\end{align*}
$$

and if $R<1-h(p)=C$, the probability $p_{error}$ goes to $0$ as desired.