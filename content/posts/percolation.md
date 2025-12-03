---
title: "Exact Results in One Dimensional Percolation"
date: 2025-12-01T15:39:50-05:00
draft: false
math: true
---

Although the percolation problem has been studied extensively in higher dimensions, percolation in one dimension seems rather dull and uninteresting, for the simple reason that percolation only happens when the lattice is completely full. In this short post, we compute several properties of the one dimensional model. 

### Bernoulli Bond Percolation
Let $G$ be an infinite chain $\mathbb{Z}$ with bond percolation. Let $p$ satisfy $0 < p < 1$. We declare each edge of $G$ to be open with probability $p$ closed otherwise. We call the resulting process a bond percolation on $G$ [^1]. It is easy to see the critical probability of percolation on $G$ is given by $p_c = 1$. Consider the following argument. For each edge $n$, define the event $A_n$ as the event that the edge is closed. Consider the sequence $(A_n)_{n \geq 0}$, which is the sequence of events for the edges to the right of the origin. We have
{{< katex >}}
$$
    \sum_{n=0}^{\infty} \mathbb{P}_p(A_n) = \sum_{n=0}^{\infty}(1-p) = \infty 
$$
{{< /katex >}}
whenever $p < 1$. By the Second Borel–Cantelli Lemma [^2], then
{{< katex >}}
$$
    \mathbb{P}_p \left(\lim_{n \rightarrow \infty} \sup A_n\right) = 0.
$$
Almost surely, there are infinitely many closed edges to the right of the origin. The same argument can be applied for $(A_{-n})_{n \geq 1}$ and we have infinitely many closed edges to the left of the origin. Thus for $p < 1$, with probability $1$, there are infinitely many closed bonds in each direction of $\mathbb{Z}$. For $p = 1$, almost surely, there is exactly one infinite open cluster, which is the whole lattice $\mathbb{Z}$. We have just proved that $\theta(p) = \mathbb{P}_p(|C| = \infty)$ is given by 
$$
    \theta(p)= \begin{cases}0 & \text { if } p < 1 \\ 1 & \text { if } p = 1 .\end{cases}
$$
{{< /katex >}}

The function $\theta(p)$ is in fact a discontinuous step function. 


### Critical Exponents
We can calculate exactly the critical exponents for the 1D chain.

{{< katex >}}
For percolation on the chain graph, the critical exponent $\gamma = 1$. This is equivalent to calculating the expected value of a geometric series. In the 1D example (approach from below, no infinite cluster), the truncation is automatic, so we need to only concern ourselves with $\chi(p)$ which maybe calculated as
    $$
        \chi(p) = 2\sum_{n\geq 0} n (1-p)p^n + 1 = 2\frac{p}{1-p} + 1 = \frac{1 + p}{1-p} 
    $$
    where the factor of $2$ comes from the fact that the cluster to the left and right are identical. The extra $1$ is the vertex at the origin itself. As $p \rightarrow p_c^- = 1^{-}$, we have $\chi(p) \approx 2/(1-p) = 2/(p_c - p)$. Therefore $\gamma = 1$.
{{< /katex >}}


The critical exponent $\Delta = -1$. To see this, we calculate the conditional moments of $|C|$ on $G$ which is defined as {{< katex >}}
    $$ 
        \frac{\mathbb{E}_p\left(|C|^{k+1} ;|C|<\infty\right)}{\mathbb{E}_p\left(|C|^k ;|C|<\infty\right)} \approx\left|p-p_{\mathrm{c}}\right|^{-\Delta} \qquad p \rightarrow p_c
    $$
    for some $k \geq 1$. For $p < 1$, there is no infinite cluster, so $|C| < \infty$ almost surely, and the truncated expectation is just $\mathbb{E}(|C|^k)$. For ease of derivation, denote $q = (1-p)$. Let $C_{\ell}$ and $C_r$ denote the clusters to the left and right of the origin. It is clear that $|C_{\ell}|, |C_r| \sim \mathrm{Geom}(q)$. The cluster $|C|$ is
    $$
        |C| = |C_{\ell}| + |C_r| + 1
    $$
    and we wish to calculate $\mathbb{E}(|C|^k)$. Observe that
    $$
        \mathbb{E} [|C|^k]q^k = \mathbb{E}[(q|C|)^k] = \mathbb{E}[(q|C_{\ell}| + q|C_{r}| + q)^k] 
    $$
    Thus in the limit $q \rightarrow 0$, we can define a new event $X_{q \rightarrow0} = q|C_{\ell}| + q |C_{r}|$. As $q \rightarrow 0$, we have the fact that $q |C_{\ell}|, q |C_{r}| \sim \mathrm{Exp}(1)$. Therefore, $X \sim \mathrm{Gamma}(2, 1)$. Evaluating the expectation value above in the limit $q \rightarrow 0$, we have
    $$
    \begin{aligned}
        \mathbb{E} [|C|^k]q^k &= \mathbb{E}[X^k] = \int_{0}^{\infty} x^{k+1} e^{-x} dx = \Gamma(k + 2) = (k+1)!,
    \end{aligned}
    $$
    which means that aymptotically
    $$
        \mathbb{E}(|C|^k) \approx (k+1)! q^k = (k+1)! |p-p_c|^{-k} \qquad p \rightarrow p_c
    $$
    Taking the gap ratio gives
    $$
         \frac{\mathbb{E}_p\left(|C|^{k+1} ;|C|<\infty\right)}{\mathbb{E}_p\left(|C|^k ;|C|<\infty\right)} \approx (k+2) |p- p_c|^{-1}\qquad p \rightarrow p_c
    $$
    which reads off the critical exponent $\Delta = 1$. 
{{< /katex >}}


The critical exponent $\nu =1$. The correlation function is the probability that a site two sites at a distance are in the same finite cluster. In the 1D chain graph case, this is equivalent to calculating the probability that all the bonds between two sites are open. Therefore
{{< katex >}} 
    $$
        \mathbb{P}(0 \leftrightarrow n) = p^n = e^{-n/\xi}
    $$
    where 
    $$
        \xi(p) = - \frac{1}{\log p}.
    $$
    For $p = 1- \varepsilon$ with $\varepsilon \downarrow 0$, we can approximate $\log p \sim - \varepsilon$, so $\xi \sim 1/(1-p)$ which means the critical exponent is $\nu = 1$. 
{{< /katex >}}

[^1]: Grimmett, Geoffrey. Percolation. 2nd ed. Berlin ; Springer, 1999.
[^2]: Feller, William. An Introduction to Probability Theory and Its Applications. 2d ed. New York: Wiley, 1957.

