---
title: "Transfer Matrix Method for Hardcore Model"
date: 2025-09-25T22:09:31-04:00
math: true
---


We apply the transfer matrix method[^1]  to evaluate the exact partition function of the hardcore lattice gas on some simple graphs. Recall that the probability distribution of the hardcore model is a probability distribution over independent sets of a graph $G$ defined by
$$
\pi(\sigma)
= \frac{1}{\mathcal{Z}(z)}z^{\sum\_{v\in G}\sigma\_{v}} \mathbf{1}\_{\{\sigma\in I(G)\}}
$$
where $\mathcal{Z}(z) = \sum_{\sigma \in I(G)} z^{|\sigma |}$ is the normalizing constant known as as the partition function, and $z = e^{h}$ is the fugacity. In the special case when $z = 1$, the distribution is a uniform distribution of independent sets of $G$. 

## Chain Graphs
Consider a 1D hardcore model on a chain graph. Let $n_i \in \\{0, 1\\}$ denote the occupation at site $i = 1,\dots,N$, and couple a uniform external field $h$ to the particles, then

{{< katex >}}
$$
\mathcal{Z}_{N}^{(\mathrm{P})} = \sum_{n_{1},\dots,n_{N}} \exp \left( h \sum_{i=1}^{N}n_{i} \right) \prod_{i=1}^N (1-n_{i}n_{i+1})
$$


where the superscript $(\mathrm{P})$ denotes a periodic boundary. The factor $\prod_{i=1}^N (1-n_{i}n_{i+1})$ kills any configurations with adjacent particles. It is useful to introduce a the following function.
$$
T(n_{1}, n_{2}) = \exp \left(\frac{h(n_{1} + n_{2})}{2}\right) \mathbb{1}_{\{(n_{1}, n_{2}) \neq (1, 1)\}}.
$$
Then the original partition function can be written as
$$
\mathcal{Z}_{N}^{(\mathrm{P})} = \sum_{n_{1},\dots,n_{N}} T(n_{1}, n_{2})T(n_{2}, n_{3}),\dots,T(n_{N-1}, n_{N})T(n_{N}, n_{1}).
$$
We note that for any $T(n_i, n_{i+1})$, it takes on four values depending on $n_i$ and $n_{i+1}$, and is thus
$$
T = \begin{pmatrix}
T(0,0) & T(0, 1) \\
T(1, 0) & T(1, 1)
\end{pmatrix}
= \begin{pmatrix}
1 & e^{h/2} \\
e^{h/2} & 0
\end{pmatrix}
= \begin{pmatrix}
1 & \sqrt{ z } \\
\sqrt{ z } & 0
\end{pmatrix}.
$$
The final sum over $n_1$ is equivalent to the trace of the matrix $T^N$, $\mathcal{Z}_N^{(\mathrm{P})} = \mathrm{Tr} T^N$. Consequently we have by writing the two eigenvalues of $\lambda_{\pm}$ for the two eigenvalues of $T$, $\mathcal{Z}_{N}^{(\mathrm{P})} = \lambda_{{+}}^N + \lambda_{{-}}^N$. The two eigenvalues of  $T$ are
$$
\lambda_{\pm} = \frac{1 \pm \sqrt{ 1 + 4z }}{2}.
$$
At $z = 1$, the eigenvalues specialize to
$$
\lambda_{\pm} = \frac{1\pm \sqrt{ 5 }}{2} = \varphi, -\varphi^{-1}.
$$
Adding the eigenvalues gives $\mathcal{Z}_{N}^{(\mathrm{P})} = \varphi^N + (-\varphi ^{-1})^N = L_{N}$, which is then $N$-th Lucas number in the Binet form.  A similar approach can be applied to the open boundary case, with boundary conditions $u = v = (1, \sqrt{z})^{\mathrm{T}}$. Then the open chain partition function for $N$ sites
$$
\mathcal{Z}_{{N}}^{(\mathrm{O})} = v^{\mathrm{T}} T^{N-1} u
$$
Spectral decomposition of $T^{N-1}$ gives
$$
T^{N-1} = S \begin{pmatrix}
\lambda_{+}^{N-1} & 0 \\ 
0 & \lambda_{-}^{N-1}
\end{pmatrix} S^{-1} = \lambda_{+}^{N-1} r_{+}\ell_{+}^{\mathrm{T}} + \lambda_{-}^{N-1} r_{-}\ell_{-}^{\mathrm{T}}
$$
where $S = [r_+, r_{-}]$ and $S^{-1} = [\ell_+, \ell_{-}]$ are the right eigenvectors and left eigenvectors of $T$. Using the decomposition above, the open chain partition function has projection onto the eigenvectors defined as
$$
\mathcal{Z}_{{N}}^{(\mathrm{O})} = (v^{\mathrm{T}}r_{+})(\ell_{+}^{\mathrm{T}}u) \lambda_{+}^{N-1} + (v^{\mathrm{T}}r_{-})(\ell_{-}^{\mathrm{T}}u) \lambda_{-}^{N-1} = c_{+}\lambda_{+}^{N-1} + c_{-} \lambda_{-}^{N-1} 
$$
where we have identified $c_{\pm} = (v^{\mathrm{T}}r_{\pm})(\ell_{\pm}^{\mathrm{T}}u)$. Using initial conditions to solve for $c_{\pm}$ gives $c_{\pm} = \pm\lambda_{\pm}^3/(\lambda_+ - \lambda_-)$. Plugging back into $\mathcal{Z}_{{N}}^{(\mathrm{O})}$ gives
$$
\mathcal{Z}_{{N}}^{(\mathrm{O})} = \frac{\lambda_{+}^{N+2} - \lambda_{-}^{N+2}}{\lambda_{+} - \lambda_{-}} 
$$
At $z = 1$, $\lambda_{\pm} = (1 \pm \sqrt{5})/2$, so the partition function evaluates to
$$
\mathcal{Z}_{{N}}^{(\mathrm{O})}(1) = \frac{\varphi^{N+2} - (-\varphi)^{N+2}}{\sqrt{ 5 }} = F_{N+2}
$$
which is the $N+2$-th Fibonacci number, determined from the eigenvalues of the transfer matrix. 
{{< /katex >}}

## Ladder Graphs
For a ladder graph of $2 \times N$, we may use the transfer matrix

{{< katex >}}
$$
T = \begin{pmatrix}
1 & z & z \\
1 & 0 & z  \\
1 & z & 0
\end{pmatrix}
$$
where $z$ as in the previous case is the fugacity, to model the constraints of the hardcore system. Then for a periodic boundary condition, we have $\mathcal{Z}_{N}^{(\mathrm{P})} = \mathrm{Tr}M^N$.  The characteristic polynomial of $M$ is
$$
\chi_{M}(z) = \lambda^3 - \lambda^2 - (z^2 + 2z) \lambda - z^2
$$
In the special case of $z = 1$, we have $\operatorname{spec}\{1 \pm \sqrt{2}, -1\}$, so 
$$
\mathcal{Z}_{N}^{(\mathrm{P})} = (1 + \sqrt{ 2 })^N + (1-\sqrt{ 2 })^N + (-1)^N.
$$
A similar computation projecting onto the eigenvectors of $T$ resolves the partition function of the open boundary conditions as
$$
\mathcal{Z}_{N}^{(\mathrm{O})} (z = 1) =  \frac{3 + 4\sqrt{ 2 }}{2} (1 + \sqrt{ 2 })^{N-1} + \frac{3 - 4\sqrt{ 2 }}{2} (1-\sqrt{ 2 })^{N-1}.
$$
A nice form would be to use the Pell numbers $P_n$ and Pell-Lucas numbers $Q_n$ so that equivalently
$$
\mathcal{Z}_{N}^{(\mathrm{P})} = Q_{N} + (-1)^N, \quad \mathcal{Z}_{N}^{(\mathrm{O})} = \frac{3}{2} Q_{{N-1}} + 4P_{N-1}
$$
{{< /katex >}}

in closed form.

Another interesting twist of the problem involves giving ladder graph a half twist before glueing the ends together. This graph is known as the [Mobius ladder](https://en.wikipedia.org/wiki/M%C3%B6bius_ladder) graph. Möbius closure flps the column lef-right before glueing. Let $R$ be the permutation matrix that mirros a column state $X$ to $X^r$, the modified partition function is now

{{< katex >}}
$$
\mathcal{Z}_{N}^{(\mathrm{M})} = \sum_{X_{1},\dots,X_{N}} T_{{X_{1}X_{2}}},\dots,T_{{X_{N-1}X_{N}}} \braket{X_{1} | R |X_{N}} = \sum_{X_{1}}  \braket{X_{1} | RM |X_{N}} = \mathrm{Tr}(RM^N).
$$
Going back to the examples of the $2 \times N$ graph, the reflection matrix that flips the valid set of states $X$ is defined as
$$
R = \begin{pmatrix}
1 & 0 & 0 \\
0 & 0 & 1 \\
0 & 1 & 0
\end{pmatrix}.
$$
Again at $z = 1$, the partition function
$$
\mathcal{Z}_{N}^{(\mathrm{M})} = (1 + \sqrt{ 2 })^N + (1 - \sqrt{ 2 })^N - (-1)^N = Q_{N} - (-1)^N
$$
which gives a parity flip compared to $\mathcal{Z}_{N}^{(\mathrm{P})}$. 
{{< /katex >}}


## Dynamic Programming
This transfer matrix method has a curious connection with dynamic programming and Markov chains. Consider the case of the open boundary chain graph at fugacity $z = 1$, the initial states $f_1 = [1, 1]^{\mathrm{T}}$, the transition function $T$, is nothing more but the transition matrix applied as 
$$
f_N = (T^{\mathrm{T}})^{N-1} f_{1}，
$$
so diagonalizing $T$ gives the closed form solution. 

The transfer matrix $T$ is also related to Markov transition matrices. Here for simplicity, we only consider the case as the system goes to the thermodynamic limit $N \rightarrow \infty$. Let $T \geq 0$ be irreducible and aperiodic. Also let $r, \ell$ be the left and right eigenpair, then by a Doob $h$-transform[^2], we may generate a Markov transition kernel
$$
M = \frac{1}{\lambda} D_{r}^{-1} T D_{r}, \quad M_{{ij}} = \frac{T_{ij}r_{j}}{\lambda r_{i}}
$$
with stationary distribution $\pi_i \propto \ell_{i} r_{i}$, and $\pi \propto \ell \odot r$. We pick the $\lambda$ as the largest eigenvalue of $T$. Again going back to the OBC chain at fugacity $z = 1$, we have under the Doob $h$-transform
$$
M(0\rightarrow 0) = \frac{1}{\varphi}, \quad M(0 \rightarrow 1) = \frac{1}{\varphi^2}, \quad M(1\rightarrow 0) = 1, \quad M(0\rightarrow 1) = 1
$$
with stationary distribution
$$
\pi_{0} = \frac{\varphi^2}{1 + \varphi^2}, \quad \pi_{1} = \frac{1}{1+ \varphi^2}.
$$
Let $\mu_N$ be the uniform measure over all valid length $N$ configurations, then the onsite marginals of $\mu_N$ converges to $\pi$ as $N \rightarrow \infty$. 

[^1]: Nishimori, Hidetoshi, and Gerardo Ortiz. Elements of phase transitions and critical phenomena. Oup Oxford, 2010.
[^2]: Levin, David A., and Yuval Peres. Markov chains and mixing times. Vol. 107. American Mathematical Soc., 2017.