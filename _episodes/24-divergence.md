---
title: "Cross Entropy and Divergence"
teaching: 
exercises:
questions:
objectives:

keypoints:

# Cross Entropy and Divergence

Entropy is a property of random variables that quantifies the amount
of information knowing the variable's distribution provides about the
variable.  Cross-entropy, on the other hand, quantifies how much
information knowing a different distribution provides about a
variable.  Divergence, also known as relative entropy, quantifies how
much information we lose in going from the variable's distribution to
another distribution.

Because they involve multiple distributions, cross entropy and
divergence are defined over distributions rather than over random
variables.  We are going to take the somewhat unorthodox approach of
motivating the definitions from entropy and random variables.

## Cross entropy

If $$Y$$ is a random variable, its entropy is defined as the expected value of its negative log density,
$$
\textrm{H}[Y]
\ = \ \mathbb{E}[- \log p_Y(Y)].
$$

But what if we use a density function $$q(y)$$ other than $$p_Y(y)$$?  The
quantity
$$
\mathrm{X}[Y, q]
\ = \
\mathbb{E}[-\log q(Y)]
$$
is known as the *cross entropy*.^[Cross-entropy is not a widely used
notion among information theory, which takes divergence as primitive.
As such, there's not a conventional unambiguous notation for it.  We
use $$\mathrm{X}[Y, q]$$ here, which is unconventional in being defined
in terms of a random variable $$Y$$ and probability function $$q$$.]

Cross entropy has the same units as entropy.  It reduces to entropy
when $$q = p_Y,$$
$$
\begin{array}{rcl}
\mathrm{H}[Y]
& = & \mathrm{X}[Y, p_Y]
\\[4pt]
& = & \mathbb{E}[-\log p_Y(Y)].
\end{array}
$$

A result known as *Gibbs inequality* states that cross entropy is
always at least as large as entropy, with equality holding only for
the true probability function.   In symbols, the Gibbs inequality
states that
$$
\textrm{H}[Y] \leq \mathrm{X}[Y, q],
$$
with equality holding only if $$q = p_Y.$$

If we have a vector quantity $$Y = Y_1, \ldots, Y_N$$, we can scale the
cross entropy to a *cross-entropy rate* by dividing by $$N$$.  This lets
us more easily compare results across data-set sizes $$N.$$

## Estimating cross-entropy with simulation

If we can sample $$y^{(1)}, \ldots, y^{(M)}$$ according to $$p_Y(y)$$ and
we can evaluate $$\log q(y),$$ then we can estimate cross-entropy as
$$
\textrm{X}[Y, q]
\ \approx \
\frac{1}{M} \sum_{m = 1}^M - \log q\!\left( y^{(m)} \right).
$$

## Evaluation via cross-entropy

Cross-entropy is an appealing evaluation tool in practice for both
practical and theoretical reasons.  Assume we have observed some data
$$y_1, \ldots, y_N$$ which we are modeling
with a distribution $$q(y).$$^[The modeling distribution $$q(y)$$ often has some
parametric form involving unknown parameters, such as a linear or
logistic regression.  It may also involve predictors $$x_n.$$]

If we assume the $$y_n$$ are drawn from the distribution of interest at
random, we can use them to estimate the cross entropy of a model $$q(y)$$ as
$$
\textrm{X}[Y, q]
\ \approx \
\frac{1}{N} \sum_{n=1}^N - \log q(y_n).
$$
For example, $$q$$ might be the posterior predictive distribution of a
parametric model estimated using observed data and prior knowledge.

We generally assume the lower the cross cross entropy, the better the
model $$q(y)$$ approximates the true data generating process $$p_Y(y).$$

## Divergence

Having defined cross-entropy, the divergence of a random variable $$Y$$
to a distribution with probability function $$q(y)$$ can be defined as
the difference between the cross-entropy and the entropy.  Because of
Gibbs inequality, this value must be non-negative, and is only zero when
$$q(y)$$ is the true generating probability function $$p_Y(y).$$

Divergence is typically defined in terms of two probability functions
$$p$$ and $$q$$ rather than between a random variable and a probability
function.  The *Kullback-Leibler divergence* from $$p$$ to $$q$$ is defined by
$$
\begin{array}{rcl}
\mathrm{D}_{\mathrm{KL}}[p \, || \, q]
& = &
\sum_{y \in \mathbb{Z}}
p(y) \cdot \log \frac{p(y)}{q(y)}
\\[4pt]
& = & 
\sum_{y \in \mathbb{Z}}
p(y) \cdot \left( \log p(y) - \log q(y) \right)
\\[4pt]
& = & 
\sum_{y \in \mathbb{Z}} p(y) \cdot \log p(y)
-
\sum_{y \in \mathbb{Z}} p(y) \cdot \log q(y). 
\end{array}
$$

If $$p$$ and $$q$$ are continuous, then
$$
\begin{array}{rcl}
\mathrm{D}_{\mathrm{KL}}[p \, || \, q]
& = &
\int_{\mathbb{R}}
p(y) \cdot \log \frac{p(y)}{q(y)} \ \textrm{d}y
\\[4pt]
& = & 
\int_{\mathbb{R}}
p(y) \cdot \left( \log p(y) - \log q(y) \right) \ \textrm{d}y
\\[4pt]
& = & 
\int_{\mathbb{R}} p(y) \cdot \log p(y) \ \textrm{d}y
-
\int_{\mathbb{R}} p(y) \cdot \log q(y) \ \textrm{d}y
\end{array}
$$

Suppose $$Y$$ is a random variable such that $$p_Y(y) = p(y).$$  Then
whether $$Y$$ is discrete or continuous (or a mixture), we can
express the KL-divergence as an expectation,
$$
\begin{array}{rcl}
\mathrm{D}_{\mathrm{KL}}[p_Y \, || \, q]
& = &
\mathbb{E}[\log p(Y) - \log q(Y)].
\\[4pt]
& = &
\mathbb{E}[\log p(Y)] - \mathbb{E}[\log q(Y)].
\\[4pt]
& = &
 - \textrm{H}[Y] - \mathbb{E}[\log q(Y)].
\\[4pt]
& = &
\mathbb{E}[-\log q(Y)] - \textrm{H}[Y].
\\[4pt]
& = &
\textrm{X}[Y, q] - \textrm{H}[Y].
\end{array}
$$
The final form is revealed as the difference between the cross entropy of $$Y$$ to $$q$$ and the entropy of $$Y.


## Computing divergence with simulation

If we can draw a sample $$y^{(1)}, \ldots, y^{(M)}$$ according to
$$p_Y(y),$$ and we can compute $$\log p_Y(y)$$ and $$\log q(y)$$, then we
can compute the KL-divergence from $$p_Y$$ to $$q$$ via simulation as
$$
\textrm{D}_{\textrm{KL}}[p_Y \, || \, q]
\ \approx \
\frac{1}{M} \sum_{m = 1}^M
- \log p_Y(y^{(m)})
+ \log q(y^{(m)}).
$$

## Divergence and cross-entropy with probability functions

The standard way to present divergence takes two arbitrary probability
functions $$p$$ and $$q$$ and defines the discrete case as
$$
\textrm{D}_{\textrm{KL}}[p \, || \, q]
\ = \
\sum_{y \in \mathbb{Z}} -p(y) \cdot \log \frac{q(y)}{p(y)}.
$$
and in the continuous case as
$$
\textrm{D}_{\textrm{KL}}[p \, || \, q]
\ = \
\int_{\mathbb{R}} -p(y) \cdot \log \frac{q(y)}{p(y)} \, \textrm{d}y.
$$

## Asymmetry of divergence

Divergence is an asymmetric notion.  $$\textrm{D}_{\textrm{KL}}[p
\, || \, q]$$ is the divergence from $$p$$ to $$q$$, whereas
$$\textrm{D}_{\textrm{KL}}[q \, || \, p]$$ is the divergence from $$q$$ to
$$p$$.  These are not usually equal.

The directionality of the definition can be visualized by considering
a bivariate normal distribution with unit scale and 0.9 correlation,
$$
p(y)
\ = \
\textrm{multinormal}\!\left(y \ \Bigg| \
0,
\begin{bmatrix}
   1.0 & 0.9
\\ 0.9 & 1.0
\end{bmatrix}
\right),
$$
along with another distribution $$q(y \mid \sigma)$$ that has diagonal
covariance with scale $$\sigma,$$^[This second distribution can be
defined as the product of two standard normals,
$$
q(y \mid \sigma)
\ = \
\textrm{normal}(y_1 \mid 0, \sigma)
\cdot
\textrm{normal}(y_2 \mid 0, \sigma).
$$
]
$$
q(y \mid \sigma)
\ = \
\textrm{multinormal}\!\left(y \ \Bigg| \
0,
\begin{bmatrix}
   \sigma & 0
\\ 0 & \sigma
\end{bmatrix}
\right).
$$


```{r}
M <- 1e4

y <- matrix(rnorm(2 * M, 0, 5), M, 2)
y_corr <- mvrnorm(M, c(0, 0), matrix(c(1, 0.9, 0.9, 1), 2, 2))

kl_div_corr_df <-
  data.frame(y1 = y_corr[ , 1], y2 = y_corr[ , 2],
             arg = rep("q", M))
kl_div_indy_df <-
  data.frame(y1 = y[ , 1], y2 = y[ , 2],
	     arg = rep("p", M))

kl_divergence_plot <-
 ggplot(kl_div_indy_df, aes(x = y1, y = y2)) +
 geom_point(data = kl_div_corr_df, aes(x = y1, y = y2), size = 0.2,
 alpha = 0.2) +
 geom_density_2d(h = 5, n = 200)
kl_divergence_plot
```

