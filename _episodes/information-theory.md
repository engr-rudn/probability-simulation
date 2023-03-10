---
title: "Entropy"
teaching: 
exercises:
questions:
objectives:

keypoints:

---
# Entropy

Information theory was developed as a means to deal with coding of
signals across noisy channels, such as telegraph signals in a discrete
case or or telephone signals in a continuous case.  We will largely be
using information theory to give us an alternative to variance to
describe the variability (entropy) of a distribution and two
alternatives to correlation to describe the similarity of two random
variables (mutual information) and two distributions (divergence).

## Entropy

The entropy of a univariate random variable $$Y$$ is defined as the
expectation of its negative log probability function,
$$
\textrm{H}[Y]
\ = \
\mathbb{E}[- \log p_Y(Y)].
$$

The logarithm can actually be computed in any base.  It is traditional
to work in base 2 for discrete probabilities and with natural
logarithms (base $$e$$) for continuous (or mixed) quantities.^[We use
the notation $$\log_2$$ for base 2 logarithms and continue using simply
$$\log$$ for natural logarithms.]
In base 2, the units are called *bits*, for reasons that will be clear
shortly; in base $$e$$, the units are called *nats*.


## Entropy in the discrete case

For a discrete distribution, $$p_Y$$ is a probability mass function and
the expectation expands to a sum over all possible integer values $$y
\in \mathbb{Z}$$,
$$
\begin{array}{rcl}
\textrm{H}[Y]
& = & \mathbb{E}\left[ \log_2 p_Y(y) \right]
\\[4pt]
& = & \sum_{y \in \mathbb{Z}} p_Y(y) \cdot \log_2 p_Y(y).
\end{array}
$$

In finite cases, the entropy is particularly simple to compute.  For
example, suppose $$Y \sim \textrm{bernoulli}(\theta).$$  Then
$$
\mathrm{H}[Y]
\ = \
\theta \cdot \log \theta
+ (1 - \theta) \cdot \log (1 - \theta).
$$

Let's look at the case where $$Y \sim \textrm{Bernoulli}\left(
\frac{1}{2} \right).$$ There is a 50% chance that $$Y = 1$$ and a 50%
chance that $$Y = 0.$$ The entropy is calculated as derived above, 
$$
\begin{array}{rcl}
\mathrm{H}\left[ Y \right]
& = &
- \frac{1}{2} \cdot \log_2 \, \frac{1}{2}
- \frac{1}{2} \cdot \log_2 \, \frac{1}{2}
\\[4pt]
& = & -\log_2 \, \frac{1}{2}
\\[4pt]
& = & -\log_2 \, 2^{-1}
\\[4pt]
& = & - - \log_2 \, 2
\\[4pt]
& = & \log_2 \, 2
\\[4pt]
& = & 1.
\end{array}
$$

The entropy of $$Y \sim \textrm{Bernoulli}\left( \frac{1}{2} \right)$$
is one bit.  This provides a natural scale for entropy---a single coin
flip is a single bit of information.^[Information theory was
originally applied to coding discrete symbols for transmission.  Morse
Code, for example, is a way to code the Latin alphabet using binary
symbols (conventionally written $$\cdot$$ and $$-$$).  The genetic code is
a way of coding amino acids using sequences of three base pairs, each
of which can take on four possible values (conventionally written `a`,
`c`, `g`, and `t`).  There is no computer code which can faithfully
encode the result a sequence of $$N$$ coin flips that uses fewer than $$N$$ bits on average.]

Now consider a categorical variable $$Z \sim
\textrm{categorical}(\theta)$$, where $$\theta = \left( \frac{1}{4},
\frac{1}{4}, \frac{1}{4}, \frac{1}{4} \right).$$ What is the entropy of
$$Z$$?^[You may want to take a guess based on the notion of bits
required to encode outcomes.]
$$
\begin{array}{rcl}
\textrm{H}\left[ Z \right]
& = &  \sum_{n=1}^4 -\frac{1}{4} \log_2 \frac{1}{4}
\\[4pt]
& = & - \log_2 2^{-2}
\\[4pt]
& = & 2.
\end{array}
$$

As we might have expected, the entropy of rolling a four-sided die is
$$\log_2 4 = 2$$ bits.  That's because we can use two computer bits to
encode a four-way outcome, with codes `00`, `01`, `10`, and `11`.  The
entropy of the outcome of rolling an eight-side die is $$\log_2 8 = 3$$
bits, with codes `000`, `001`, `010`, `011`, `100`, `101` `110`,
`111`.

What is the entropy of two coin flips, $$Y_1, Y_2 \sim
\textrm{Bernoulli}\left( \frac{1}{2} \right)?$$  We just combine them
into a joint variable $$Z = (Y_1, Y_2)$$ and proceed as usual, taking
$$
\textrm{H}\left[ Z \right]
\ = \ \mathbb{E}[-\log_2 p_Z(z)].
$$

The expectation is computed as usual, by enumerating possible values
for $$Z$$, which looks like
$$
\begin{array}{rcl}
\textrm{H}\left[ Z \right]
& = &
\sum_{y_1 = 0}^1 \sum_{y_2 = 0}^1 \, p_{Y_1, Y_2}(y_1, y_2) \cdot \log
p_{Y_1, Y_2}(y_1, y_2)
\\[4pt]
& = &
-\frac{1}{4} \cdot \log_2 \frac{1}{4}
-\frac{1}{4} \cdot \log_2 \frac{1}{4}
-\frac{1}{4} \cdot \log_2 \frac{1}{4}
-\frac{1}{4} \cdot \log_2 \frac{1}{4}
\\[4pt]
& = &
-\log_2 4^{-1}
\\[4pt]
& = & 2.
\end{array}
$$

So far, we've only considered balanced outcomes, as in a coin toss.
Now suppose that $$Y \sim \textrm{bernoulli}(\theta)$$.  The following plot shows the entropy $$\textrm{H}\left[ Y \right]$$ as a function of $$\theta.$$

```{r, out.width = "100%", fig.asp = 0.4, fig.cap = "Left) entropy of a variable $$Y \\sim \\textrm{bernoulli}(\\theta)$$ as a function of $$\\theta.$$ Right) standard deviation of the same variable as a function of $$\\theta.$$ The shapes are not identical, but share the same mode of $$\\theta = 0.5$$ and minima at $$\\theta = 0$$ and $$\\theta = 1.$$"}

theta <- seq(0.001, 0.999, by = 0.001)
H_theta <- - (theta * log(theta) + (1 - theta) * log(1 - theta))
sd_theta <- sqrt(theta * (1 - theta))

bernoulli_entropy_df <-
  rbind(data.frame(theta = theta, H = H_theta, sd = sd_theta),
        data.frame(theta = c(0, 1), H = c(0, 0), sd = c(0, 0)))
	
bernoulli_entropy_plot <-
  ggplot(bernoulli_entropy_df, aes(x = theta, y = H)) +
  geom_line(size = 0.5) +
  scale_x_continuous(breaks = c(0, 1/4, 1/2, 3/4, 1),
                     labels = c("0", "1/4", "1/2", "3/4", "1")) +
  xlab(expression(theta)) +
  ylab("H[Y]") +
  ggtheme_tufte()
  
bernoulli_sd_plot <-
  ggplot(bernoulli_entropy_df, aes(x = theta, y = sd)) +
  geom_line(size = 0.5) +
  scale_x_continuous(breaks = c(0, 1/4, 1/2, 3/4, 1),
                     labels = c("0", "1/4", "1/2", "3/4", "1")) +
  xlab(expression(theta)) +
  ylab("sd[Y]") +
  ggtheme_tufte()

grid.arrange(ggplotGrob(bernoulli_entropy_plot),
             ggplotGrob(bernoulli_sd_plot),
             ncol = 2)
```

The maximum entropy for $$Y \sim \textrm{bernoulli}(\theta)$$ occurs at
$$\theta = 0.5$$, when there is a 50% chance of each outcome.  This
represents the maximum amount of uncertainty possible.  At the other
extreme, $$\theta = 0$$ and $$\theta = 1$$ correspond to complete
certainty in the outcome and thus the entropy is zero.  Other values
are in between.  At $$\theta = 0.9$$, for example, the variable is much
more likely to be one than zero.  

## Differential entropy

In the continuous case, $$p_Y$$ is a probabiltiy density function and
the expectation expands to an integral over all possible real values
$$y \in \mathbb{R}$$,
$$
\begin{array}{rcl}
\textrm{H}[Y]
& = & \mathbb{E}[-\log p_Y(y)]
\\[4pt]
& = & \int_{\mathbb{R}} \ - \log p_Y(y) \cdot p_Y(y) \, \textrm{d}y.
\end{array}
$$

Because density values may be greater than one, differential entropy can be negative with very narrowly distributed variables, which makes it a less than  desirable general measure of information.

## Calculating entropy with simulation

Because entropy is defined as an expectation, it can be computed using
simulation.  Suppose $$y^{(1)}, \ldots, y^{(M)}$$ drawn according to
$$p_Y(y)$$.  The entropy of $$Y$$ is then calculated by plugging the draws
in for the random variable $$Y$$ and averaging,
$$
\begin{array}{rcl}
\textrm{H}[Y]
& = & \mathbb{E}[\log p_Y(y)]
\\[4pt]
& \approx &
\frac{1}{M} \sum_{m=1}^M \log p_Y\!\left(y^{(m)}\right).
\end{array}
$$

For example, let's calculate the entropy of a normally distributed random variable $$Y \sim \textrm{normal}(\mu, \sigma)$$ as a function of $$\sigma$$.^[As with standard deviation, the location parameter $$\mu$$ does not affect the entropy of a normally distributed variable.]  We just draw $$y^{(m)} \sim \textrm{normal}(0, \sigma)$$ and calculate the average $$\log \textrm{normal}(y^{(m)} \mid 0, \sigma).$$ value.

```{r, fig.cap = "Differential entropy of a normally distributed variable $$Y \\sim \\textrm{normal}(\\mu, \\sigma)$$ as a function of its scale $$\\sigma.$$"}
estimate_normal_entropy <- function(M, sigma) {
  y <- rnorm(M, 0, sigma)
  log_p_y <- dnorm(y, 0, sigma, log = TRUE)
  -sum(log_p_y)
}

set.seed(1234)
M <- 1e5
sigmas <- seq(0.1, 5, by = 0.1)
N <- length(sigmas)
H_sigma <- rep(NA, N)
for (n in 1:N) {
  H_sigma[n] <- estimate_normal_entropy(M, sigmas[n])
}

normal_entropy_df <- data.frame(sigma = sigmas, H = H_sigma)

normal_entropy_plot <-
  ggplot(normal_entropy_df, aes(x = sigma, y = H)) +
  geom_line(size = 0.5) +
  scale_y_continuous(breaks = c(-1e5, 0, 1e5, 2e5, 3e5),
           labels = c("-100,000", "0", "100,000", "200,000", "300,000")) +
  xlab(expression(sigma)) +
  ylab("H[Y]") +
  ggtheme_tufte()
normal_entropy_plot
```

The plot shows that differential entropy increases sublinearly as the
scale $$\sigma$$ increases. The plot also shows that for values of
$$\sigma$$, the differential entropy is negative.  Discrete entropy, in
contrast, is always positive.

## Maximum entropy distributions

Suppose we have a normally distributed variable with expectation $$\mu$$
and standard deviation $$\sigma,$$
$$
U \sim \textrm{normal}(\mu, \sigma).
$$
Now suppose we have another random variable $$V$$ and only know that it
also has an expectation of $$\mu$$ and standard deviation of $$\sigma$$,
$$
\mathbb{E}[V] = \mu \ \ \ \textrm{and} \ \ \ \textrm{sd}[V] = \sigma.
$$
Then we know that
$$
H[U] \geq H[V],
$$
with equality only if $$V$$ is also normally distributed, $$V \sim
\textrm{normal}(\mu, \sigma).$$ Another way of saying this is that
among all possible random variables with a given expectation and
standard deviation, a normally distributed one has the maximum
entropy.

## Conditional and joint entropy

Entropy may also be defined conditionally and jointly, just like
distributions.  We just take expectations of the relevant negative log
probability functions.

The joint entropy of $$X, Y$$ is defined as the expectation of the
negative log joint probability function,
$$
\textrm{H}[X, Y]
\ = \
\mathbb{E}[\log p_{X,Y}(X, Y)].
$$
For example, if $$U$$ and $$V$$ are both discrete, their joint entropy is
defined to be
$$
\textrm{H}[U, V]
\ = \
\sum_{u \in \mathbb{Z}}
\
\sum_{v \in \mathbb{Z}}
\
- \log p_{U, V}(u, v)
\cdot
p_{U,V}(u, v).
$$

Similarly, the conditional entropy is the expectation of the log
conditional,
$$
\textrm{H}[Y \mid X]
\ = \
\mathbb{E}[\log p_{Y \mid X}(Y \mid X)].
$$
The expectation is read in the usual way, by averaging over all of the
random variables, here $$X$$ and $$Y$$.  For example, if $$U$$ and $$V$$ are
both discrete,
$$
\textrm{H}[V \mid U]
\ = \
\sum_{u \in \mathbb{Z}}
\
\sum_{v \in \mathbb{Z}}
\
- \log p_{U, V}(u \mid v)
\cdot
p_{U, V}(u, v).
$$
As usual, the expectation of an expression is calculated by averaging
over all of the random variables in the expression weighted by their
probability function.  So whether we take the expectation of $$-\log
p_{U, V}(u, v)$$ or $$-\log p_{U \mid V}(u \mid v),$$ we average all
outcomes weighted by $$p_{U,V}(u, v).$$

Conditional and joint entropy satisfy the usual rules of
probability,^[The derivation is purely algebraic, 
$$
\begin{array}{rcl}
\textrm{H}[X] + \textrm{H}[Y \mid X]
& = &
\sum_x - \log p(x) \cdot p(x)
+
\sum_x \sum_y -\log p(y \mid x) \cdot p(x, y)
\\[4pt]
& = &
\sum_x - \log p(x) \cdot p(x)
+
\sum_x \sum_y -\log p(y \mid x) \cdot p(y \mid x) \cdot p(x)
\\[4pt]
& = &
\sum_x - \log p(x) \cdot p(x)
+
\sum_x \left( \sum_y -\log p(y \mid x) \cdot p(y \mid x) \right) \cdot p(x)
\\[4pt]
& = &
\sum_x
\left(
  -\log p(x) +
  \left(
    \sum_y -\log p(y \mid x) \cdot p(y \mid x)
  \right)
\right)  
\cdot p(x)
\\[4pt]
& = &
\sum_x \sum_y
\left( -\log p(x) \cdot p(y \mid x) - \log p(y \mid x) \cdot p(y \mid x) \right)
\cdot p(x)
\\[4pt]
& = &
\sum_x \sum_y -\log p(x, y) \cdot p(x, y)
\\[4pt]
& = &
\textrm{H}[X, Y].
\end{array}
$$
The only tricky step is pulling the summation over $$y$$ out in the
third to last step, which is made possible because
$$
\sum_y -\log p(x) \cdot p(y \mid x)
\ = \
-\log p(x).
$$
]
$$
\textrm{H}[X, Y]
\ = \
\textrm{H}[X]
+
\textrm{H}[Y \mid X]
$$
Entropies add rather than multiply because they are on the log
probability (or density) scale.




