---
title: Information Theory
---

entropy
$$
\mathrm {H} (X)=\operatorname {E} [\operatorname {I} (X)]=\operatorname {E} [-\log(\mathrm {P} (X))] \\
\mathrm {H} (X)=-\sum _{i=1}^{n}{\mathrm {P} (x_{i})\log _{b}\mathrm {P} (x_{i})} \\
$$
conditional entropy
$$
\mathrm {H} (X|Y)=-\sum _{i,j}p(x_{i},y_{j})\log {\frac {p(x_{i},y_{j})}{p(y_{j})}}
$$


cross-entropy
$$
H(p,q)=-\operatorname {E} _{p}[\log q] \\
H(p,q)=-\sum _{x\in {\mathcal {X}}}p(x)\,\log q(x)
$$


Kullback-Leibler Divergence
$$
D_{\text{KL}}(P\parallel Q)=\sum _{x\in {\mathcal {X}}}P(x)\log \left({\frac {P(x)}{Q(x)}}\right).
$$

$$
H(p,q)=H(p)+D_{\mathrm {KL} }(p\|q) \\
$$

