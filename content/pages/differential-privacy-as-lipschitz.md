+++
author = "Kevin H. Wilson"
title = "Metric Formulations of Differential Privacy"
date = "2021-07-01"
description = "Constructing differential privacy as a topology on the space of probability measures"
tags = [
    "differential privacy",
]
+++


## Differential Privacy as a Lipschitz Condition

Another way to look at differnetial privacy is as a Lipschitz-like condition between two metric spaces. Specifically, take $X$ and $Y$ to be complete, second countable metric spaces we can think of the randomized function $f: X \to Y$ as defining a family of probability measures $\mu_x$ that satisfy the condition that $\mu_x(A) \leq \exp(\varepsilon d(x, x')) \mu_{x'}(A)$ for all $A \subseteq Y$ and $x, x' \in X$.[^randomized_function] This condition obviously forces $\mu_x$ to be [absolutely continuous](https://en.wikipedia.org/wiki/Absolute_continuity#Absolute_continuity_of_measures) with respect to $\mu_{x'}$ (and vice-versa). Then the [Radon-Nikodym Theorem](https://en.wikipedia.org/wiki/Radon%E2%80%93Nikodym_theorem) tells us that there is a measureable function $\frac{d\mu_x}{d\mu_{x'}} : Y \to \mathbb{R}$ (unique upto a $\mu_{x'}$ null-set) such that $\int_A \frac{d\mu_x}{d\mu_{x'}}(x) d\mu_{x'}(x) = \mu_x(A)$.

[^randomized_function]:
    There are a few possible definitions of _randomized function_. For instance, you could say that a randomized function $f : X \to Y$ is actually a function $f : X \to \mathcal{P}(Y)$ the space of probability measures on $Y$ (the definition we're using here). You could also think of a randomized function $f : X \to Y$ as a $\operatorname{Hom}(X, Y)$-valued random varaiable, i.e., a map $f : \Omega \to \operatorname{Hom}(X, Y)$. Then composing with the restriction map $\operatorname{Hom}(X, Y) \to \operatorname{Hom}(\{x\}, Y)$ and the canonical isomorphism $\operatorname{Hom}(\{x\}, Y) \cong Y$, you recover a map $X \to \mathcal{P}(Y)$ by pushing forward the measure on $\Omega$.

    Of course, the space $\operatorname{Hom}(X, Y)$ can be quite badly behaved in general, and it is not clear that we want to consider only continuous maps $X \to Y$. (Consider, for instance, the paucity of continuous maps `$g: \mathbb{R} \to \{0, 1\}$`.) Resolving these technical issues is difficult, and so we instead prefer for this post to consider a _randomized function_ definitionally as a map $f : X \to \mathcal{P}(Y)$.

For any function $h : Y \to \mathbb{R}$ and measure $\mu$ on $Y$, let `$\| h \|_{\infty, \mu}$` be the sup-norm on $h$, i.e., the infimum over all $K \geq 0$ such that $h(y) \leq K$ except potentially on a set of $\mu$-measure $0$. Note that this norm depends only on the null sets of $\mu$, and so `$\| h \|_{\infty, \mu} = \| h \|_{\infty, \nu}$` whenever $\mu$ and $\nu$ are mutually absolutely continuous.

With this norm in hand, we can define a (quasi-)metric `$d_{DP}$` on `$\mathcal{P}(Y)$` as follows:
  * If `$\mu, \nu \in \mathcal{P}(Y)$` and `$\mu \ll \nu \ll \mu$`, then define `$d_{DP}(\mu, \nu) = \| \ln \frac{d\mu}{d\nu} \|_{\infty, \mu}$`. Note that this may be infinite.
  * If `$\mu, \nu \in \mathcal{P}(Y)$` are _not_ mutually absolutely continuous, define `$d(\mu, \nu) = \infty$`.

This is a quasi-metric in the sense that `$d(\mu, \nu) = 0$` if and only if `$\mu = \nu$`, it is symmetric `$d(\mu, \nu) = d(\nu, \mu)$`, and satisfies the triangle inequality `$d(\mu, \rho) \leq d(\mu, \nu) + d(\nu, \rho)$`. When $\mu$ and $\nu$ are mutually absolutely continuous, the first two points follow directly from the fact that they must have the same null sets and the Radon-Nikodym theorem. When they are not mutually absolutely continuous, $d(\mu, \nu) = d(\nu, \mu) = \infty$ by definition, so the first two points are obvious.

As for the triangle inequality, when `$\mu$`, `$\nu$`, and `$\rho$` are not mutually absolutely continuous, then the triangle inequality is obvious. On the other hand, if `$\mu$`, `$\nu$`, and `$\rho$` are all mutually absolutely continuous, then the Radon-Nikodym theorem tells us that `$\frac{d\mu}{d\rho} = \frac{d\mu}{d\nu} \cdot \frac{d\nu}{d\rho}$`, and so the triangle inequality for `$d_{DP}$` follows from the triangle inequality of `$\| \cdot \|$`.

Equipping $\mathcal{P}(Y)$ with this metric, we arrive at the following alternative characterization of differential privacy.

{{< thm >}}
The randomized function $f: X \to Y$ is $\varepsilon$-differentially private if and only if the associated map $f : X \to \mathcal{P}(Y)$ is $\varepsilon$-Lipschitz.
{{< /thm >}}

{{< proof >}}
Suppose that the map `$f : X \to \mathcal{P}(Y)$` is `$\varepsilon$`-Lipschitz. Then for each `$x, x' \in X$` we have that `$\frac{d\mu}{d\mu_{x'}}(y) \leq \exp(\varepsilon \cdot d(x, x'))$` except potentially on a set of `$\mu_{x'}$`-measure `$0$`. Then

$$
\mu_x(A) = \int_A \frac{d\mu}{d\mu_{x'}}(y) d\mu_{x'}(y) \leq \exp(\varepsilon \cdot d(x, x')) \int_A d\mu_{x'}(y) = \exp(\varepsilon \cdot d(x, x')) \mu_{x'}(A).
$$

On the other hand, suppose that the map $f : X \to \mathcal{P}(Y)$ is _not_ `$\varepsilon$`-Lipschitz. Then there are some $x, x' \in X$ some subset $E \subseteq Y$ of positive measure such that `$\left| \ln \frac{d\mu}{d\mu_{x'}}(y) \right| > \varepsilon$` for almost all $y \in E$. Letting $E_+$ be the set of all $y \in E$ such that $\ln \frac{d\mu}{d\mu_{x'}}(y) > 0$ and $E_-$ be the set of all $y \in E$ such that $\ln \frac{d\mu}{d\mu_{x'}}(y) < 0$, we see that at least one of $E_+$ and $E_-$ must have positive measure. By potentially swapping $x$ and $x'$, we may assume without loss of generality that $E_+$ has positive measure. Then

$$
\mu_x(E_+) = \int_{E_+} \frac{d\mu}{d\mu_{x'}}(y) d\mu_{x'}(y) > \exp(\varepsilon) \int_{E_+} d\mu_{x'}(y) = \exp(\varepsilon) \mu_{x'}(E_+).
$$
{{< /proof >}}


## The DP topology

We now have a metric `$d_{DP}$` on a set `$\mathcal{P}(Y)$`, and so it induces a topology, which we call the _differential privacy_ or _DP topology_. A natural question to ask is _which_ topology does it induce, and from there, why should _privacy_ be related to this topology as opposed to one of the other, more familiar topologies. In this section, we take $Y$ to be a second countable, complete metric space, e.g., $\mathbb{R}$ with its usual metric or any finite set with the discrete metric.

### Other topologies

There are a few common topologies discussed on the space of measures $\mathcal{P}(Y)$ when $Y$ is a metric space. The first is called the _topology of weak convergence_ or what we'll call the _WC topology_. In this topology, a sequence of probability measures $\mu_n \in \mathcal{P}(Y)$ converges to a probability measure $\mu \in \mathcal{P}(Y)$ whenever $$\int f(y) d\mu_n(y) \to \int f(y) d\mu(y)$$ for all continuous functions $f : Y \to [-1, 1]$.[^portmanteau] This topology is induced by a metric called the [_Prokhorov metric_](https://en.wikipedia.org/wiki/L%C3%A9vy%E2%80%93Prokhorov_metric). Specifically, if $\mu$ and $\nu$ are probability measures, we can define
`$$
  d_{WC}(\mu, \nu) = \inf \{ \varepsilon > 0 | \mu(A^\varepsilon) \leq \nu(A) + \varepsilon, \nu(A^\varepsilon) \leq \mu(A) + \varepsilon \text{ for all measureable } A \subseteq Y \}.
$$`
Here `$A^\varepsilon = \bigcup_{a \in A} B_\varepsilon(a)$` is the `$\varepsilon$`-_neighborhood_ of $A$, defined as the union of the balls of radius $\varepsilon$ around every point in $A$.

The second topology is the _total variation_ or _TV topology_. In this topology, a sequence of probability measures `$\mu_n \in \mathcal{P}(Y)$` converges to a probability measure `$\mu \in \mathcal{P}(Y)$` whenever `$$\lim_{n \to \infty} \sup_{f \in \mathcal{F}_{TV}} \left| \int f(y) d\mu_n(y) - \int f(y) d\mu(y)\right| \to 0$$` where $\mathcal{F}_{TV}$ is the set of all continuous functions $f : Y \to [-1, 1]$. Note that the distinction between this definition and weak convergence is the order of the quantifiers: weak convergence says that _for each function_ the difference $\left| \int fd\mu_n - \int fd\mu \right|$ must converge to $0$; total variation requires _all the differences_ to converge to $0$ at a uniform rate.

The TV topology on $\mathcal{P}(Y)$ is induced by the _total variation metric_ on $\mathcal{P}(Y)$. Specifically, $d_{TV}(\mu, \nu) = \sup_{A \subseteq Y} \left| \mu(A) - \nu(A) \right|$.[^tv_metric_proof] From the last sentence of the previous paragraph, it is clear that convergence in the TV topology will always yield weak convergence. However, the other direction is not always the case. For instance, the sequence of uniform discrete measures $\mu_n = \sum_{i=1}^n \frac{1}{n} \delta_{i/n}$ supported on $1/n, \ldots, n/n$ converges weakly to the uniform distribution $\mu$ on $[0, 1]$ (this essentially amounts to the statement that bounded continuous functions are Riemann integrable), but clearly $d_{TV}(\mu_n, \mu) = 1$ for all $n$.

[^portmanteau]: There are several other equivalent definitions of weak convergence, which are related by the so-called [portmanteau theorem](https://en.wikipedia.org/wiki/Convergence_of_measures#Weak_convergence_of_measures).

[^tv_metric_proof]: The proof of this fact follows the same basic outline as that of Theorem ???? and uses two key observations: since $Y$ is a metric space, every Borel probability measure is regular; and the function `$f(y) = \max\{ 0, 1 - \varepsilon d_Y(y, F) \}$` is continuous and bounded.

When $Y$ is also compact, we will consider a third topology: the _Earth Mover_ or _EM topology_, sometimes called the _Wasserstein-1 topology_. Here a sequence of probability measures $\mu_n \in \mathcal{P}(Y)$ converges to a probability measure $\mu \in \mathcal{P}(Y)$ whenever `$$\lim_{n \to \infty} \sup_{f \in \mathcal{F}_{EM}} \left| \int f(y) d\mu_n(y) - \int f(y) d\mu(y)\right| \to 0$$` where $\mathcal{F}_{EM}$ is the set of all _Lipschitz_ functions `$f : Y \to \mathbb{R}$` with Lipschitz constant $\mathrm{Lip}(f) \leq 1$. In a bit of a miracle, it turns out that the EM topology and the WC topology are identical![^check_this] However, the relative distances between measures can vary greatly. (??????????)

[^check_this]: The Wasserstein metrics can be defined even when $Y$ is not compact. However, this definition relies on the fact that the underlying metric on $Y$ is bounded. When $Y$ is compact, this is automatically true.


### The "form" of Differential Privacy

We have now seen three other metrics and the topologies they induce on the space of probability measures. Common to each of these cases was that a sequence of measures $\mu_n$ converged to $\mu$ if $\left| \int f d\mu_n - \int f d\mu \right| \to 0$ for all $f$ in some family $\mathcal{F}$ of (continuous) functions $f : Y \to \mathbb{R}$. In some cases, we could required this convergence to be _uniform_, i.e., `$\sup_{f \in \mathcal{F}} \left| \int f d\mu_n - \int f d\mu \right| \to 0$`. Does the DP topology have a similar characterization? The answer is yes.

We begin by taking `$\mathcal{F}_{DP}$` to be the set of continuous functions `$f : Y \to (0, \infty)$` which are bounded above, i.e., there is some `$M > 0$` such that for `$y \in Y$` we have `$f(y) \leq M$`. Note that this guarantees that `$\int f d\mu < \infty$` for all probability measures `$\mu$` on $Y$. Moreover, `$\{ y : f(y) > c \}$` must have positive measure for some $c > 0$ or else their union `$\{y : f(y) > 0 \} = Y$` would have measure $0$. Thus $\int_Y f d\mu > 0$. We say that a sequence of probability measures $\mu_n$ _converges in ratio_ to $\mu$ whenever the quantity `$\sup_{f \in \mathcal{F}_{DP}} \left| \int f d\mu_n / \int f d\mu \right| \to 1$`, or equivalently, `$\sup_{f \in \mathcal{F}_{DP}} \left| \ln \int f d\mu_n - \ln \int f d\mu \right| \to 0$`.

{{< thm >}}
A sequence of mutually absolutely continuous probability measures $\mu_n$ converges in ratio to a probability measure $\mu$ if and only if it converges in the DP topology.
{{< /thm >}}
{{< proof >}}
Suppose $\mu_n$ converges to $\mu$ in the DP topology. By DP convergence, the Radon-Nikodym derivative $\frac{d\mu_n}{d\mu}$ converges almost everywhere to $1$ pointwise, and in particular, for every $\varepsilon > 0$ we have for sufficiently large $n$ that $\frac{d\mu_n}{d\mu}(y) \in (\exp(-\varepsilon), \exp(\varepsilon))$ for almost all $y \in Y$. Thus for any `$f \in \mathcal{F}_{DP}$`, we have that
`$$
\int f d\mu_n = \int f \frac{d\mu_n}{d\mu} d\mu \leq \exp(\varepsilon) \int f d\mu,
$$`
and similarly for a lower bound. Thus, $\sup_{f \in \mathcal{F}} \left| \ln \int f d\mu_n - \ln \int f d\mu \right|$ is bounded above by $\varepsilon$, proving that $\mu_n$ converges in ratio to $\mu$.

In the other direction, suppose that $\mu_n$ does not converge to $\mu$ in the DP topology. Then there is a $C > 1$ such that for each sufficiently large $n$ there is a set $E_n$ of positive measure where the Radon-Nikodym derivative $\frac{d\mu_n}{d\mu}$ is either larger than $C$ or less than $1 / C$. (Recall that since $\mu_n$ and $\mu$ are mutually absolutely continuous, $E_n$ has positive measure with respect to any of the measures in question if and only if it has positive measure with respect to _all_ measures in question.) By passing to a subsequence, we may assume that _all_ the derivatives are either larger than $C$ or less than $1 / C$. Let's take the second case.

If we mimic the definition of differential privacy, we would consider the indicator function $I_{E_n}$ and say that $\int I_{E_n} d\mu_n / \int I_{E_n} d\mu < \frac{1}{C} \mu(E_n) / \mu(E_n) = \frac{1}{C} < 1$ which we would hope mean that $\mu_n$ does not converge in ratio to $\mu$. However, $I_{E_n}$ is not necessarily in $\mathcal{F}$, and so we need to create an approximation.

To approximate `$I_{E_n}$`, we will need several positive constants to be choosen later. We will name them `$\varepsilon > 0$`, which will be the constant associated with differential privacy and `$\rho > 0$` which will be a _regularity_ parameter. To begin, recall that every Borel probability measure on a metric space is regular, i.e., if `$\nu$` is a probability measure on a metric space `$(X, d_X)$`, `$E \subseteq X$` is a Borel measureable set, and `$\rho > 0$` is any constant, then there exists some closed set `$F \subseteq E$` and some open set `$G \supseteq E$` such that `$\nu(G - F) < \rho$`. Thus, for each `$n$`, we may choose `$F_n \subseteq E_n \subseteq G_n$` with `$\mu_n(G_n - F_n) < \rho$` and `$\mu(G_n - F_n) < \rho$`.

Now for any `$\varepsilon > 0$`, define the function
`$$ f(y) = f_{n\varepsilon}^{\rho}(y) = \max\{ \lambda, \exp(-\varepsilon d_Y(y, F_n)) \}. $$`
Here for any set $A \subseteq Y$ we define `$d_Y(y, A) = \inf_{y' \in A} d_Y(y, y')$`.

Note that this function is continuous and bounded above by $1$, so $f \in \mathcal{F}_{DP}$. Moreover, it is equal to $1$ precisely on `$F_n$`. Using these facts, we can write
`$$
\int_Y f(y) d\mu_n(y) =
\int_{F_n} f(y) d\mu_n(y) + \int_{G_n - F_n} f(y) d\mu_n(y) + \int_{Y - G_n} f(y) d\mu_n(y).
$$`
The first of these integrals is just `$\mu_n(F_n)$`, and by our assumptions, we know that $\mu_n(F_n) \leq \frac{1}{C} \mu(F_n)$. The second of these integrals is bounded above by $\mu_n(G_n - F_n) < \rho$ by assumption.

Finally, `$d_Y(y, F_n)$` is uniformly bounded away from $0$ for all $y \in Y - G_n$, i.e., there exists some $C' > 0$ such that $d_Y(y, F_n) > C'$ for all $y \in Y - G_n$. Thus, $f(y) < \exp(-\varepsilon C')$.

Thus, in total we have
$$
\int_Y f(y) d\mu_n(y) \leq
\frac{1}{C}\mu(F_n) + \rho + \exp(-\varepsilon C')\mu_n(Y - G_n).
$$
By letting $\rho \to 0$ and $\varepsilon \to \infty$ (dependent on $\rho$), we see that we may make this right hand quantity as close to $\frac{1}{C}\mu(F_n)$ as we want.

On the other hand, we can bound the denominator in our definition by
$$
\int_Y f d\mu \geq \int_{F_n} f d\mu = \mu(F_n).
$$
Thus, the ratio
$$
\frac{\int_Y f d\mu_n}{\int_Y fd\mu} \leq \frac{\frac{1}{C}\mu(F_n) + \rho + \exp(-\varepsilon C')\mu_n(Y - G_n)}{\mu(F_n)} \to \frac{1}{C}.
$$

To finish the proof, we must consider the case when $E_n$ is such that $\frac{d\mu_n}{d\mu} > C$. But then $\frac{d\mu}{d\mu_n} < 1/C$ on $E_n$ and a similar proof allows us to show that
$$
\frac{\int_Y f d\mu_n}{\int_Y fd\mu} \geq \frac{\mu_n(F_n)}{\frac{1}{C}\mu_n(F_n) + \rho + \exp(-\varepsilon C')\mu(Y - G_n)} \to C.
$$

In either case, for all $\varepsilon' > 0$, we have found a function `$f_n \in \mathcal{F}_{DP}$` such that `$\ln\left| \int_Y f d\mu_n / \int_Y f d\mu \right| > \ln(C) - \varepsilon'$` for all $n$. That is, $\sup_{f \in \mathcal{F}} \ln\left| \int_Y f d\mu_n / \int_Y f d\mu \right| > \ln(C)$ for all $n$. Thus, $\mu_n$ cannot converge in ratio to $\mu$ when $\mu_n$ does not converge in the DP topology to $\mu$.

{{< /proof >}}

{{< remark >}}
In the statement of the above theorem, we required that the `$\mu_n$` and `$\mu$` be mutually absolutely continuous. However, it is clear that in order to converge in the DP topology, for sufficiently large `$n$`, the `$\mu_n$` and `$\mu$` must be mutually absolutely continuous. Moreover, a similar approximation argument to the above shows that convergence in ratio _also_ requires `$\mu_n$` and `$\mu$` to be mutually absolutely continuous for sufficiently large $n$, so the condition can be dropped.
{{< /remark >}}


### Comparing the DP topology to the TV topology

In this section, we compare convergence in the DP topology to the TV topology. We begin with two examples showing that convergence in the TV topology does _not_ imply convergence in the DP topology.

{{< example >}}
Consider the probability measures $\mu_n$ on $[0, 1]$ which are defined by a probability density function equal to $n / (n-1)$ on $[1/n, 1]$ and $0$ on $[0, 1/n)$. These measures converge in total variation to the uniform measure, but $\mu_m \not\ll \mu_n$ for all $m > n$ and so they cannot converge the DP topology to any measure.
{{< /example >}}

You might object that these measures do not have the same support and so are running afoul of the "mutual absolute continuity" requirement.  But we can rectify this.

{{< example >}}
  Let $\mu$ and $\mu_n$ be as in the previous example. Then consider the sequence of probability measures $\nu_n = \frac{1}{n}\mu + \frac{n - 1}{n}\mu_n$. This sequence of measures _also_ converges in total variation to $\mu$, but does _not_ converge in the DP topology to $\mu$ as $|\ln \frac{d\mu_n}{d\mu}(y)| = n$ for all $n$ and all $y \in [0, 1/n)$, and so this sequence diverges in the DP distance.
{{< /example >}}

On the other hand, the DP topology is, in fact, coarser than the TV topology, which is to say:

{{< thm >}}
If a sequence of measures $\mu_n$ converges to $\mu$ in the DP topology, it converges in the TV topology, but not vice-versa.
{{< /thm >}}

{{< proof >}}

Suppose $\mu_n$ converges to $\mu$ in the DP distance. Then `$\left| \ln \frac{d\mu_n}{d\mu}(y) \right| \to 0$` for almost every $y$, or, to put it another way, `$\frac{d\mu_n}{d\mu}(y) \to 1$` almost everywhere. Since $\mu$ is a probability measure, the constant function $2$ is integrable, and so `$\left|\frac{d\mu_n}{d\mu}(y) - 1\right|$` is bounded above by an integrable function for sufficiently large `$n$` and, moreover, converges to the constant function `$0$` almost everywhere.

Thus,
`$$\left|\mu_n(A) - \mu(A)\right| = \left| \int_A \left(\frac{d\mu_n}{d\mu}(y) - 1\right) dy \right| \leq \int_A \left| \frac{d\mu_n}{d\mu}(y) - 1 \right| dy.$$`
By the dominated convergence theorem, this last integral converges to $0$ as $n \to \infty$.

{{< /proof >}}



## The Weak-DP Topology

As we saw in the previous section, the DP topology on `$\mathcal{P}(Y)$` is defined by saying that `$\mu_n$` converges to `$\mu$` when we have `$\sup_{f \in \mathcal{F}_{DP}} \left| \ln \int f d\mu_n - \ln \int f d\mu \right| \to 0$`. We noted that a similar definition defines the TV topology, by replacing the set of test functions $\mathcal{F}_{DP}$ with the set of test functions `$\mathcal{F}_{TV}$`. We also saw that the WC topology is defined by dropping the `$\sup$` in the definition above. By analogy, then, we can attempt to define a topology on `$\mathcal{P}(Y)$` by saying a sequence of measures $\mu_n$ converges to $\mu$ whenever for all `$f \in \mathcal{F}_{DP}$` we have `$\left| \ln \int f d\mu_n - \ln \int f d\mu \right| \to 0$`. This turns out to be the same topology as the weak topology.

To see why, recall that by assumption, if $f : Y \to (0, \infty)$ is continuous and bounded, there must be some $c > 0$ such that `$\{ y : f(y) > c \}$` has positive measure or else their union `$\{ y : f(y) > 0 \} = Y$` would have measure $0$. Thus `$\int f d\mu > 0$`. Then to show weak convergence implies convergence in the proposed topology, simply note that for sufficiently large $n$, `$\int fd\mu_n \to \int f d\mu > 0$` implies that `$\int f d\mu_n > 0$`, and since $\ln$ is continuous, `$\ln \int fd\mu_n \to \ln \int f d\mu$`. For the other direction, if $f : Y \to [-1, 1]$ is continuous and bounded, then the function $g(y) = f(y) + 2$ is a continuous, bounded function $g: Y \to (0, \infty)$, and a similar argument utilizing the fact that $\exp$ is continuous finishes the proof.

