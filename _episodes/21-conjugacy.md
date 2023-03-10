---
title: "Conjugate Priors"
teaching: 
exercises:
questions:
objectives:

keypoints:

---
# Conjugate Priors

## Uniform prior and binomial likelihood

Suppose we have a uniform prior for parameter $$\theta \in (0, 1)$$,
$$
\theta \sim \textrm{uniform}(0, 1),
$$
and combine it with a a likelihood for data $$y \in 0:N$$,
$$
y \sim \textrm{binomial}(N, \theta).
$$
We know from Bayes's rule that the posterior is proportional to the
likelihood times the prior.  Writing this out in symbols,
$$
\begin{array}{rcl}
p(\theta \mid y, N)
& \propto &
\displaystyle
\frac{p(y \mid \theta, N) \cdot p(\theta)}
     {p(y \mid N)}
\\[6pt]
& \propto &
p(y \mid \theta) \cdot p(\theta)
\\[4pt]
& = & \textrm{binomial}(y \mid N, \theta) \cdot \textrm{uniform}(\theta \mid 0,
1)
\\[4pt]
& = &
\displaystyle
\binom{N}{y} \cdot \theta^y \cdot (1 - \theta)^{N - y} \cdot 1
\\[4pt]
& \propto &
\displaystyle
\theta^y \cdot (1 - \theta)^{N - y}.
\end{array}
$$

Now that we know $$p(\theta \mid y) \propto \theta^y \cdot (1 -
\theta)^{N - y},$$ we can follow Laplace in applying Euler's beta
function to normalize,^[Euler's beta function is defined by
$$
\begin{array}{rcl}
\textrm{B}(\alpha, \beta)
& = &
\displaystyle
\int_0^1 u^{\alpha - 1} \cdot (1 - u)^{\beta - 1} \mathrm{d}u
\\[4pt]
& = &
\displaystyle
\frac{\Gamma(\alpha) \cdot \Gamma(\beta)}
     {\Gamma(\alpha + \beta)},
\end{array}
$$
where the gamma function is defined by
$$
\textstyle \Gamma(\gamma) = \int_0^{\infty} u^{\gamma - 1} \cdot \exp(-u) \, \textrm{d}u.
$$
The gamma function generalizes the integer factorial function, satisfying
$$
\Gamma(u + 1) = u!
$$
for all integers $$u \in \mathbb{N}.$$
]
$$
\begin{array}{rcl}
p(\theta \mid y, N)
& = &
\displaystyle
\frac{\theta^y \cdot (1 - \theta)^{N - y}}
     {\int_0^1 \theta^y \cdot (1 - \theta)^{N - y} \, \textrm{d}\theta}
\\[6pt]
& = &
\displaystyle
\frac{1}
     {\mathrm{B}(y, N - y)}
\cdot \theta^y \cdot (1 - \theta)^{N - y}
\\[6pt]
& = &
\displaystyle
\frac{\Gamma(N)}
     {\Gamma(y) \cdot \Gamma(N - y)}
\cdot \theta^y \cdot (1 - \theta)^{N - y.}
\end{array}
$$

Another way of arriving at the same result is to just work through
Bayes's rule by brute force,
$$
p(\theta \mid y)
= \frac{p(y \mid \theta) \cdot p(\theta)}
       {\int_0^1 p(y \mid \theta) \cdot p(\theta) \, \textrm{d}\theta}.
$$
The integral in the denominator is just the beta function given above.


## Beta prior and binomial likelihood produces a beta posterior

A $$\textrm{beta}(1, 1)$$ distribution is identical to a $$\textrm{uniform}(0,
1)$$ distribution, because
$$
\begin{array}{rcl}
\textrm{beta}(\theta \mid 1, 1)
& \propto &
\theta^{1 - 1} \cdot (1 - \theta)^{1 - 1}
\\[4pt]
& = &
1
\\[4pt]
& = &
\textrm{uniform}(\theta \mid 0, 1).
\end{array}
$$

Now suppose we assume a beta prior with parameters $$\alpha, \beta >
0,$$
$$
\theta \sim \textrm{beta}(\alpha, \beta).
$$
When combined with a binomial likelihood,
$$
y \sim \textrm{binomial}(N, \theta),
$$
the result is a posterior of the following form
$$
\begin{array}{rcl}
p(\theta \mid y, N)
& \propto &
p(y \mid N, \theta) \cdot p(\theta)
\\[4pt]
& = &
\textrm{binomial}(y \mid N, \theta) \cdot \textrm{beta}(\theta \mid
\alpha, \beta)
\\[4pt]
& \propto &
\left( \theta^y \cdot (1 - \theta)^{N - y} \right)
\cdot
\left( \theta^{\alpha - 1} \cdot (1 - \theta)^{\beta - 1} \right)
\\[8pt]
& = &
\theta^{y + \alpha - 1} \cdot (1 - \theta)^{N - y + \beta - 1}
\\[6pt]
& \propto &
\textrm{beta}(y + \alpha, N - y + \beta).
\end{array}
$$
The rearrangement of terms in the penultimate step^[This uses the rule
of exponents from algebra,
$$\theta^u \cdot \theta^v = \theta^{u = v}.$$] lets us collect a
result that matches the kernel of a beta distribution.

The takeaway message is that if the prior is a beta distribution and
the likelihood is binomial, then the posterior is also a beta
distribution.

## Conjugate priors

In general, a family $$\mathcal{F}$$ of priors is said to be *conjugate*
to a family $$\mathcal{G}$$ of likelihood functions, if $$p(\theta) \in
\mathcal{F}$$ and $$p(y \mid \theta) \in \mathcal{G}$$ imply that
$$p(\theta \mid y) \in \mathcal{F}$$.  We have already seen one example
of conjugacy, with the family of beta priors and binomial likelihoods, 
$$
\begin{array}{rcl}
\mathcal{F} & = &
\{ \, \textrm{beta}(\alpha, \beta) \mid \alpha, \beta > 0 \, \}
\\[6pt]
\mathcal{G} & = &
\{ \, \textrm{binomial}(N, \theta) \mid N \in \mathbb{N}, \
\theta \in (0, 1) \, \}.
\end{array}
$$

It is no coincidence that the likelihood and prior in our conjugate
example have matching forms,
$$
\begin{array}{r|rllll|l}
\textrm{prior}
& \textrm{beta}(\theta \mid \alpha, \beta)
& \propto &
\theta^{\alpha - 1}
& \cdot &
(1 - \theta)^{\beta - 1}
&  p(\theta)
\\[6pt]
\textrm{likelihood} 
& 
\textrm{binomial}(y \mid N, \theta)
& \propto &
\theta^y
& \cdot &
(1 - \theta)^{N - y}
& p(y \mid \theta)
\\[4pt] \hline
\textrm{posterior} 
&
\textrm{beta}(\theta \mid y + \alpha, \ N - y + \beta)
& \propto &
\theta^{y + \alpha - 1}
& \cdot &
(1 - \theta)^{N - y + \beta - 1}
& p(\theta \mid y)
\end{array}
$$

Thinking of the exponents $$y$$ and $$N - y$$ as success and failure
counts respectively, we can think of the exponents $$\alpha - 1$$ and
$$\beta - 1$$ as the prior number of successes and failures.^[The prior
counts are $$\alpha - 1$$ and $$\beta - 1$$ for a total prior count of
$$\alpha + beta - 2$$.  The reason for the subtraction is that
$$\textrm{beta}(1, 1)$$ is the uniform distribution, so $$\alpha = 1, \beta
= 1$$ corresponds to a prior count of zero.]  We then just add the
prior successes and the likelihood successes to get $$y + \alpha - 1$$
posterior successes; the prior failures work similarly, with $$\beta -
1$$ prior failures and $$N - y$$ observed failures producing a $$N - y +
\beta - 1$$ posterior failure count.


## Beta-Bernoulli conjugacy

The Bernoulli distribution is just a single trial binomial
distribution.  For a single binary observation $$y \in \{ 0, 1 \}$$ and
chance of success parameter $$\theta,$$
$$
\textrm{bernoulli}(y \mid \theta)
\ = \
\textrm{binomial}(y \mid 1, \theta).
$$
Therefore, the beta distribution must also be conjugate to the Bernoulli
distribution---if the prior is a beta distribution and the likelihood
is a Bernoulli distribution, then the posterior is also a beta
distribution.   This is evident if we recast the Bernoulli definition
in the same form as the beta and binomial distributions,
$$
\textrm{bernoulli}(y \mid \theta)
\ = \ \theta^y \cdot (1 - \theta)^{1 - y}.
$$

Working through the algebra, if $$p(\theta) = \textrm{beta}(\alpha, \beta)$$
and the likelihood is $$p(y \mid \theta) = \textrm{bernoulli}(y \mid \theta)$$, then the
posterior is $$p(\theta \mid y) = \textrm{beta}(\alpha + y, \beta + 1 -
y).$$

Said more simply, if the prior is $$\textrm{beta}(\alpha, \beta)$$ and we
condition on a single binary observation $$y$$;  if $$y = 1$$, the updated
distribution is $$\textrm{beta}(\alpha + 1, \beta);$$ if $$y = 0$$, the
updated distribution is $$\textrm{beta}(\alpha, \beta + 1).$$

## Chaining repeated observations

Suppose we do not have a single binary observation, but a whole
sequence $$y = y_1, \ldots, y_N$$, where $$y_n \in \{ 0, 1 \}.$$  Now
suppose we start with a prior $$p(\theta) = \textrm{beta}(\theta \mid
\alpha, \beta)$$.  Given no observations, our knowledge of the
parameter $$\theta$$ is determined by the prior,
$$
p(\theta) = \textrm{beta}(\theta \mid \alpha, \beta).
$$
After the first observation, $$y_1$$, we can update our knowledge of
$$\theta$$ conditioned on that observation to
$$
p(\theta \mid y_1)
\ = \
\textrm{beta}(\theta \mid \alpha + y_1, \beta + 1 - y_1).
$$

What is our knowledge of $$\theta$$ after two observations, $$y_1, y_2$$?
We can apply Bayes's rule, to see that
$$
p(\theta \mid y_1, y_2)
\propto
p(\theta \mid y_1) \cdot p(y_2 \mid \theta).
$$
Displayed this way, the distribution $$p(\theta \mid y_1)$$ is acting
like a prior with respect to the likelihood for the single observation
$$y_2$$.  Substituting in the actual distributions, we have
$$
\begin{array}{rcl}
p(\theta \mid y_1, y_2)
& = &
p(\theta \mid y_1) \cdot p(y_2 \mid \theta)
\\[4pt]
& = &
\textrm{beta}(\theta \mid \alpha + y_1, \beta + 1 - y_1)
\cdot
\textrm{bernoulli}(y_2 \mid \theta)
\\[4pt]
& \propto &
\textrm{beta}(\theta \mid \alpha + y_1 + y_2, \beta + 1 - y_1 + 1 - y_2)
\\[4pt]
& = & 
\textrm{beta}(\theta \mid \alpha + (y_1 + y_2), \beta + 2 - (y_1 + - y_2))
\end{array}
$$

We can visualize these chained updates as follows.

```{r, engine='tikz', out.width = "100%", fig.ext="pdf",  fig.cap="Progress of streaming updates with conjugate priors.  There is an initial prior $$\\textrm{beta}(\\alpha_0, \\beta_0)$$ and a stream of data $$y_1, y_2, \\ldots, y_N.$$  After each data point $$y_n$$ is observed, the prior parameters $$\\alpha_{n-1}, \\beta_{n-1}$$ are updated to the posterior parameters $$\\alpha_{n}, \\beta_n,$$ which then acts as a prior for subsequent data."}
\begin{tikzpicture}[->, auto, node distance=3cm, font=\normalsize]
\node[rectangle,draw,semithick, label = below:$$p(\theta)$$] (A) {$$\textrm{beta}(\alpha_0, \beta_0)$$};
\node[rectangle,draw,semithick, label = below:$$p(\theta \mid y_1)$$] (B) [right of = A] {$$\textrm{beta}(\alpha_1, \beta_1)$$};
\node[rectangle,draw,semithick, , label = below:$$p(\theta \mid y_{1:2})$$] (C) [right of = B] {$$\textrm{beta}(\alpha_2, \beta_2)$$};
\node[draw=none, fill=none] (D) [right of = C] {$$\cdots$$};
\node[rectangle,draw,semithick,, label = below:$$p(\theta \mid y_{1:N})$$] (E) [right of = D] {$$\textrm{beta}(\alpha_N, \beta_N)$$};
\path(A) edge [ ] node {$$y_1$$} (B);
\path(B) edge [ ] node {$$y_2$$} (C);
\path(C) edge [ ] node {$$y_3$$} (D);
\path(D) edge [ ] node {$$y_N$$} (E);
\end{tikzpicture}
```

Given a prior $$\textrm{beta}(\alpha_0, \beta_0)$$, we will let
$$\textrm{beta}(\alpha_n, \beta_n)$$ to be the distribution for $$\theta$$
conditioned on observations
$$
y_{1:n} = y_1, \ldots, y_n.
$$
The values $$(\alpha_n, \beta_n)$$ are defined inductively.  As a base
case $$(\alpha_0, \beta_0)$$ are our initial prior parameters.  Then
inductively, after observing $$y_{n + 1},$$ we have
$$
(\alpha_{n + 1}, \beta_{n + 1})
\ = \
(\alpha_n + y_{n + 1}, \beta_n + 1 - y_{n + 1}).
$$
Because $$y_n$$ is binary, we can expand this definition by cases and
also write
$$
(\alpha_{n + 1}, \beta_{n + 1})
\ = \
\begin{cases}
(\alpha_n + 1, \beta_n)
& \textrm{if} \ y_{n + 1} = 1, \ \textrm{and}
\\[4pt]
(\alpha_n, \beta_n + 1)
& \textrm{if} \ y_{n + 1} = 0.
\end{cases}
$$

All that is happening is counting---we just add one to the success
count if $$y_n = 1$$ and one to the failure count if $$y_n = 0.$$  By
the time we get to the end and have observed $$y_{1:N},$$ we have
$$
(\alpha_n, \beta_n)
\ = \
(\alpha_0 + \textrm{sum}(y), \
 \beta_0 + N - \textrm{sum}(y)).
$$

This is, not coincidentally, the same result we would have achieved
had we started with the prior $$\textrm{beta}(\alpha_0, \beta_0)$$ and
observed all $$y_1, \ldots, y_N$$ simultaneously.  In that case, we
could use the equivalence with the binomial,
$$
\textrm{binomial}(\textrm{sum}(y) \mid N, \theta)
\ \propto \
\theta^{\textrm{sum}(y)} \cdot (1 - \theta)^{N - \textrm{sum}(y)}.
$$

Another way of deriving this result is by direct expansion
$$
\begin{array}{rcl}
p(\theta \mid y_1, \ldots, y_N)
& \propto &
p(\theta) \cdot p(y_1, \ldots, y_N \mid \theta) 
\\[4pt]
& = &
p(\theta) \cdot p(y_1 \mid \theta) \cdots p(y_N \mid \theta).
\\[4pt]
& = &
\textrm{beta}(\theta \mid \alpha_0, \beta_0)
\cdot
\prod_{n=1}^N \textrm{bernoulli}(y_n \mid \theta).
\\[4pt]
& = &
\theta^{\alpha_0 - 1} \cdot (1 - \theta)^{\beta_0 - 1}
\cdot
\prod_{n=1}^N \theta^{y_n} \cdot \theta^{1 - y_n}.
\\[4pt]
& = &
\theta^{\alpha_0 - 1} \cdot (1 - \theta)^{\beta_0 - 1}
\cdot
\theta^{\sum_{n=1}^N y_n}
\cdot
(1 - \theta)^{\sum_{n=1}^N 1 - y_n}
\\[4pt]
& = &
\left( \theta^{\alpha_0 - 1} \cdot (1 - \theta)^{\beta_0 - 1} \right)
\cdot
\left( \theta^{\textrm{sum}(y)} \cdot (1 - \theta)^{N - \textrm{sum}(y)}
\right)
\\[4pt]
& = &
\theta^{\alpha_0 + \textrm{sum}(y) - 1} \cdot (1 - \theta)^{\beta_0 + N
- \textrm{sum}(y) - 1}
\\[4pt]
& \propto &
\textrm{beta}(\theta
        \mid \alpha_0 + \textrm{sum}(y), \beta_0 + N - \textrm{sum}(y)).
\end{array}
$$

## Gamma-Poisson conjugacy

The same line of reasoning that led from a binomial distribution to a
prior that behaved as prior data, we can reason from the Poisson
distribution backward to a conjugate prior.  Recall that for a count
$$y \in \mathbb{N}$$ and rate $$\lambda \in (0, \infty)$$,
$$
\textrm{poisson}(y \mid \lambda)
\ = \
\frac{1}{y!}
\cdot \lambda^y
\cdot \exp(-\lambda).
$$
Given this form, the gamma distribution provides a family of
conjugate priors, parameters by a shape $$\alpha$$ and rate (inverse
scale) $$\beta$$, as
$$
\textrm{gamma}(\lambda \mid \alpha, \beta)
\ = \
\frac{\beta^{\alpha}}{\Gamma(\alpha)}
\cdot
\lambda^{\alpha - 1}
\cdot
\exp(-\beta \cdot \lambda).
$$

Now let's work out the conjugacy.  Suppose we have a Poisson
likelihood with rate parameter $$\lambda$$,
$$
\begin{array}{rcl}
p(y \mid \lambda)
& = & \textrm{poisson}(y \mid \lambda),
\\[4pt]
& = &
\frac{1}{y!}
\cdot \lambda^y
\cdot \exp(-\lambda)
\end{array}
$$
with a gamma prior on the rate parameter,
$$
\begin{array}{rcl}
p(\lambda \mid \alpha, \beta)
& = & \textrm{gamma}(\lambda \mid \alpha, \beta)
\\[4pt]
& = &
\frac{\beta^{\alpha}}{\Gamma(\alpha)}
\cdot
\lambda^{\alpha - 1}
\cdot
\exp(-\beta \cdot \lambda).
\end{array}
$$
The posterior is equal to the product of the likelihood and prior up
to a proportion,
$$
\begin{array}{rcl}
p(\lambda \mid y, \alpha, \beta)
& = & p(y \mid \lambda) \cdot p(\lambda \mid \alpha, \beta)
\\[4pt]
& = &
\textrm{poisson}(y \mid \lambda)
\cdot \textrm{gamma}(\lambda \mid \alpha, \beta)
\\[4pt]
& = &
\left(
\frac{1}{y!}
\cdot \lambda^y
\cdot \exp(-\lambda)
\right)
\cdot
\left(
\frac{\beta^{\alpha}}{\Gamma(\alpha)}
\cdot
\lambda^{\alpha - 1}
\cdot
\exp(-\beta \cdot \lambda)
\right)
\\[4pt]
& \propto &
\left( \lambda^y \cdot \exp(-\lambda) \right)
\cdot
\left( \lambda^{\alpha - 1} \cdot \exp(-\beta \cdot \lambda) \right)
\\[4pt]
& = &
\lambda^{y + \alpha - 1} \cdot \exp(-(\beta + 1) \cdot \lambda)
\\[4pt]
& \propto &
\textrm{gamma}(\alpha + y, \beta + 1).
\end{array}
$$

Now suppose we have a sequence of count observations $$y_1, \ldots, y_N
\in \mathbb{N}.$$  Assuming a $$\textrm{poisson}(\lambda)$$ likelihood
with prior $$\textrm{gamma}(\lambda \mid \alpha_0, \beta_0)$$, we can
update with $$y_1$$ to produce a posterior
$$
p(\lambda \mid y_1, \alpha_0, \beta_0)
\ = \
\textrm{gamma}(\alpha_0 + y_1, \beta_0 + 1).
$$
Using this as a prior for $$y_2$$, the posterior becomes
$$
p(\lambda \mid y_1, y_2, \alpha_0, \beta_0)
\ = \
\textrm{gamma}(\alpha_0 + y_1 + y_2, \beta_0 + 2).
$$
Extending this logic, we can take a whole batch of observations $$y_1,
\ldots, y_N$$ at once to produce a posterior,
$$
p(\lambda \mid y_1, \ldots, y_N, \alpha_0, \beta_0)
\ = \
\textrm{gamma}(\alpha_0 + \textrm{sum}(y), \beta_0 + N).
$$
Worked out one step at a time, the posterior after observing $$y_1,
\ldots, y_n$$ is 
$$
p(\lambda \mid y_1, \ldots, y_n, \alpha_0, \beta_0)
\ = \
p(\lambda \mid \alpha_n, \beta_n)
$$
where $$(\alpha_0, \beta_0)$$ are given as initial priors, and
$$
(\alpha_{n + 1}, \beta_{n + 1})
\ = \
(\alpha_n + y_{n+1}, \beta_n + 1).
$$

Thinking of the gamma prior in terms of Poisson data,
$$\textrm{gamma}(\alpha, \beta)$$ represents a total of $$\beta$$ prior
observations, with a sum total of $$\alpha$$.  For example, if we have a
single prior observation $$y$$, that corresponds to a $$\textrm{gamma}(y,
1)$$ prior; if we have three prior observations $$12, 7, 9$$, that'd be
represented by a $$\textrm{gamma}(26, 3)$$ prior.