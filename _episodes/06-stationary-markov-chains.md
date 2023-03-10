---
title: "Conjugate Posteriors"
teaching: 
exercises:
questions:
objectives:

keypoints:

---
# Stationary Distributions and Markov Chains

## Stationary Markov chains

A random process $$Y = Y_1, Y_2, \ldots$$ is said to be *stationary* if
the marginal probability of a sequence of elements does not depend on
where it starts in the chain.  In symbols, a discrete-time random
process $$Y$$ is stationary if for any $$t \geq 1$$ and any sequence
$$u_1, \ldots, u_N \in \mathbb{R}^N$$ of size $$N$$, we have

$$
p_{Y_1, \ldots, Y_N}(u_1, \ldots, u_N)
= p_{Y_t, \ldots, Y_{t + N}}(u_1, \ldots, u_N)
$$

None of the chains we will construct for practical applications will
be stationary in this sense, because we would need to know the
appropriate initial distribution $$p_{Y_1}(y_1)$$.  For example,
consider the fishes example in which we know the transition
probabilities, but not the stationary distribution.  If we run long
enough, the proportion of pike stabilizes


## Stationary distributions

Although we will not, in practice, have Markov chains that are
stationary from $$t = 1$$, we will use Markov chains that have
stationary distributions in the limit as $$t \rightarrow \infty$$.  For
a Markov chain to be stationary, there must be some $$q$$ such that

$$
p_{Y_t}(u) = q(u)
$$

for all $$t$$, starting from $$t = 0$$.  Instead, we will have an
equilibrium distribution $$q$$ that the chain approaches in the limit as
$$t$$ grows.  In symbols,

$$
\lim_{t \rightarrow \infty}
\
p_{Y_t}(u) = q(u).
$$

Very confusingly, this equilibrium distribution $$q$$ is also called a
*stationary distribution* in the Markov chain literature, so we will
stick to that nomenclature.  We never truly arrive at $$q(u)$$ for a
finite $$t$$ because of the bias introduced by the initial distribution
$$p_{Y_1}(u) \neq q(u)$$.  Nevertheless, as with our earlier
simulation-based estimates, we can get arbitrarily close after
suitably many iterations.^[The last section of this chapter
illustrates rates of convergence to the stationary distribution, but
the general discussion is in the later chapter on continuous-state
Markov chains.]

Reconsider the example of a process $$Y = Y_1, Y_2, \ldots$$ of fishes,
where 1 represents a pike and 0 a perch.  We assumed the Markov process
$$Y$$ was governed by

$$
\begin{array}{rcl}
\mbox{Pr}[Y_{t + 1} = 1 \mid Y_t = 1] & = & 0.20
\\[4pt]
\mbox{Pr}[Y_{t + 1} = 1 \mid Y_t = 0] & = & 0.05
\end{array}
$$

Rewriting as a probability mass function,

$$
p_{Y_{t + 1} \mid Y_t}(j \mid i) = \theta_{i, j},
$$

where $$\theta_{i, j}$$ is the probabiltiy of a transition to state $$j$$
given that the process is in state $$i$$.  For the pike and perch
example, $$\theta$$ is fully defined by

$$
\begin{array}{rcl}
\theta_{1, 1} & = & 0.20
\\
\theta_{1, 2} & = & 0.80
\\ \hline
\theta_{2, 1} & = & 0.05
\\
\theta_{2, 2} & = & 0.95.
\end{array}
$$

These numbers are normally displayed in the form of a *transition
matrix*, which records the transitions out of each state as a row,
with the column indicating the target state,

$$
\theta =
\begin{bmatrix}
0.20 & 0.80 \\
0.05 & 0.95
\end{bmatrix}.
$$

The first row of this transition matrix is $$(0.20, 0.80)$$ and the
second row is $$(0.05, 0.95)$$. Rows of transition matrices will always
have non-negative entries and sum to one, because they are the
parameters to categorical distributions.^[Vectors with non-negative
values that sum to one are known as *unit simplexes* and matrices in
which every row is a unit simplex is said to be a *stochastic matrix.*
Transition matrices for finite-state Markov chains are always
stochastic matrices.]

Now let's take a really long run of the chain with $$T = 1\,000\,000$$
fish to get a precise estimate of the long-run proportion of pike.

```{r}
set.seed(1234)
T <- 1e6
y <- rep(NA, M)
for (k in 0:1) {
  y[1] <- k
  for (t in 2:T) {
    y[t] <- rbinom(1, 1, ifelse(y[t - 1] == 1, 0.2, 0.05))
  }
  printf("initial state = %1f; simulated proportion of pike = %4.3f\n",
         k, sum(y) / T)
}
```

The initial state doesn't seem to matter. That's because the rate of
5.9% pike is the stationary distribution. More
formally, let $$\pi = (0.059, 1 - 0.059)$$ and note that^[In matrix
notation, if $$\pi$$ is considered a row vector, then $$\pi = \theta \, \pi.$$]

$$
\pi_i = \sum_{j = 1}^2 \pi_j \times \theta_{j, i}.
$$

If $$\pi$$ satisfies this formula, then it is said to be the *stationary
distribution* for $$\theta.$$

If a Markov chain has a stationary distribution $$\pi$$ and the initial
distribution of $$Y_1$$ is also $$\pi$$, then it is stationary.

## Reducible chains

The Markov chains we will use for sampling from target distributions
will be well behaved by construction.  There are, however, things that
can go wrong with Markov chains that prevent them from having
stationary distributions.  The first of these is reducibility.  A
chain is reducible if it can get stuck in a state from which other
states are not guaranteed to be revisited with probability one.

State diagram for a reducible finite Markov chain.  The chain will eventually get stuck in state 3 and never exit to visit states 1 or 2 again.

<!-- ```
\begin{tikzpicture}[->, auto, node distance=2cm, font=\footnotesize]
\node[circle,draw,semithick] (A) {1};
\node[circle,draw,semithick] (B) [left of=A] {2};
\node[circle,draw,semithick] (C) [right of=A] {3};
\path (A) edge [bend right] node[above] {1/2} (B);
\path (A) edge [bend left] node[above] {1/2} (C);
\path (B) edge [bend right] node[below] {1} (A);
\path (C) edge [loop above] node {1} (C);
\end{tikzpicture} -->
![reducible_finite_Markov_chain](../images/reducible_finite_Markov_chain.jpg)

If we start the chain in state 1, it will eventually transition to
state 3 and get stuck there.^[State 3 is what is known as a *sink
state.*]  It's not necessary to get stuck in a single state.  The same
problem arises if state 3 has transitions out, as long as they can't
eventually get back to state 1.

State diagram for another reducible finite Markov chain.  The chain will eventually get stuck in state 3 and 4 and never exit to visit states 1 or 2 again.


![](../images/another_reducible_finite_Markov_chain.jpg)


<!-- \begin{tikzpicture}[->, auto, node distance=2cm, font=\footnotesize]
\node[circle,draw,semithick] (A) {1};
\node[circle,draw,semithick] (B) [left of=A] {2};
\node[circle,draw,semithick] (C) [right of=A] {3};
\node[circle,draw,semithick] (D) [right of=C] {4};
\path (A) edge [bend right] node[above] {1/2} (B);
\path (A) edge [bend left] node[above] {1/2} (C);
\path (B) edge [bend right] node[below] {1} (A);
\path (C) edge [bend left] node[above] {1/2} (D);
\path (D) edge [bend left] node[below] {1/2} (C);
\path (C) edge [loop above] node {1/2} (C);
\path (D) edge [loop above] node {1/2} (D);
\end{tikzpicture}
  -->

In this example, the chain will eventually fall into a state where it
can only visit states 3 and 4.


## Periodic chains

A Markov chain can be constructed to cycle through states in a regular
(probabilistic) pattern.  For example, consider the following Markov
chain transitions.

State diagram for finite Markov chain generating periodic state sequences $$
\\ldots, 2, 3, 1, 2, 3, 1, 2, 3, 1, 2, \\ldots
$$.


![](../images/finite_Markov_chain_generating_periodic_state.jpg)
<!--
\begin{tikzpicture}[->, auto, node distance=2cm, font=\footnotesize]
\node[circle,draw,semithick] (A) {1};
\node[circle,draw,semithick] (B) [below left of=A] {2};
\node[circle,draw,semithick] (C) [below right of=A] {3};
\path (A) edge [bend right] node {1} (B);
\path (B) edge [bend right] node {1} (C);
\path (C) edge [bend right] node {1} (A);
\end{tikzpicture}
 -->

Regular cycles like this defeat the existence of a stationary
distribution.  If $$Y_1 = 2$$, the entire chain is deterministically
defined to be

$$
Y = 2, 3, 1, 2, 3, 1, 2, 3, 1, 2, 3, \ldots.
$$

Clearly $$p_{Y_t} \neq p_{Y_{t+1}}$$, as each concentrates all of its
probability mass on a different value.

On the other hand, this chain is what is known as wide-state
stationary in that using long-running frequency estimates are stable.
The expected value is $$\frac{1 + 2 + 3}{3} = 2$$ and the standard
deviation is $$\sqrt{\frac{1^2 + 0^2 + 1^2}{3}} \approx 0.47$$. More
formally, the wide-state expectation is calculated as

$$
\lim_{T \rightarrow \infty}
\
\frac{1}{T}
\sum_{t=1}^T Y_t
\rightarrow 2.
$$

The definition of periodicity is more subtle than just deterministic
chains.  For example, the following transition graph is also periodic.

State diagram for finite Markov chain generating periodic state sequences alternating between state 1 and either state 2 or state 3.


![](../images/finite_Markov_chain_generating_sequences_of_fishes.jpg)

<!--
\begin{tikzpicture}[->, auto, node distance=2cm, font=\footnotesize]
\node[circle,draw,semithick] (A) {1};
\node[circle,draw,semithick] (B) [left of=A] {2};
\node[circle,draw,semithick] (C) [right of=A] {3};
\path (A) edge [bend right] node[above] {1/2} (B);
\path (A) edge [bend left] node[above] {1/2} (C);
\path (B) edge [bend right] node[below] {1} (A);
\path (C) edge [bend left] node[below] {1} (A);
\end{tikzpicture}
 -->

Rather than a deterministic cycle, it cycles between the state 1 and
the pair of states 2 and 3.  A simulation might look like

$$
y^{(1)} = 1, 2, 1, 2, 1, 2, 1, 3, 1, 3, 1, 2, 1, 3, 1, 2, \ldots
$$

Every other value is a 1, no matter whether the chain starts in state
1, 2, or 3.  Such behavior means there's no stationary distribution.
But there is a wide-sense stable probability estimate for the states,
namely 50% of the time spent in state 1, and 25% of the time spent in
each of states 2 and 3.


## Convergence of finite-state chains

In applied statistics, we proceed by simulation, running chains long
enough that they provide stable long-term frequency estimates. These
stable long-term frequency estimates are of the stationary
distribution $$\mbox{categorical}(\pi)$$.  All of the Markov chains we
construct to sample from target distributions of interest (e.g.,
Bayesian posterior predictive distributions) will be well-behaved in
that these long-term frequency estimates will be stable, in
theory.^[In practice, we will have to be very careful with diagnostics
to avoid poor behavior due to floating-point arithmetic combined
with approximate numerical algorithms.]

In practice, none of the Markov chains we employ in calculations will
be stationary for the simple technical reason that we don't know the
stationary distribution ahead of time and thus cannot draw $$Y_1$$ from
it.^[In the finite case, we actually can calculate it either through
simulation or as the eigenvector of the transition matrix with
eigenvalue one (which is guaranteed to exist). An eigenvector of a
matrix is a row vector $$\pi$$ such that $$c \times \pi = \theta \,
\pi,$$ where $$c$$ is the eigenvalue. This is why Google's PageRank
algorithm is known to computational statisticians as the "billion
dollar eigenvector." One way to calculate the relevant eigenvector of
a stochastic matrix is by raising it to a power, starting from any
non-degenerate initial simplex vector $$\lambda$$, $$\lim_{n \rightarrow
\infty} \lambda \, \theta^n = \pi.$$ Each $$\theta^n =
\underbrace{\theta \times \theta \times \cdots \times
\theta}_{\textstyle n \ \mbox{times}}$$ is a transition matrix
corresponding to taking $$n$$ steps in the original transition matrix
$$\theta$$.] What we need to know is conditions under which a Markov
chain will "forget" its initial state after many steps and converge to
the stationary distribution.

All of the Markov chains we will employ for applied statistics
applications will be well behaved in the sense that when run long
enough, the distribution of each element in the chain will approach
the stationary distribution. Roughly, when $$t$$ is large enough, the
marginal distribution $$p_{Y_t}$$ stabilizes to the stationary
distribution.  The well-behavedness conditions required for this to
hold may be stated as follows

**Fundamental Convergence Theorem.** If a Markov chain $$Y = Y_1,
Y_2, \ldots$$ is (a) irreducible, (b) aperiodic, and (c) has a
stationary distribution $$\mbox{categorical}(\pi)$$, then

$$
\lim_{t \rightarrow \infty}
\
P_{Y_t}(u) \rightarrow \mbox{categorical}(u \mid \pi).
$$

What this means in practice is that we can use a single simulation,

$$
y^{(1)}
\ = \
y^{(1)}_1, y^{(1)}_2, \ldots, y^{(1)}_T
$$

to estimate the parameters for the stationary distribution.  More
specifically, if we define $$\pi$$ by

$$
\widehat{\pi}_i
=
\frac{1}{T} \sum_{t = 1}^T \mathrm{I}[y_t^{(1)} = i]
$$

then we can estimate the stationary distribution as
$$\mbox{categorical}(\widehat{\pi}).$$

As a coherence check, we often run a total of $$M$$ simulations of the
first $$T$$ values of the Markov chain $$Y$$.

$$
\begin{array}{rcl}
y^{(1)} & = & y_1^{(1)}, y_2^{(1)}, \ldots, y_T^{(1)}
\\[4pt]
y^{(2)} & = & y_1^{(2)}, y_2^{(2)}, \ldots, y_T^{(2)}
\\[2pt]
\vdots
\\[2pt]
y^{(M)} & = & y_1^{(M)}, y_2^{(M)}, \ldots, y_T^{(M)}
\end{array}
$$

We should get the same estimate from using $$y^{(m)}$$ from a single
simulation $$m$$ as we get from using all of the simulated chains
$$y^{(1)}, \ldots, y^{(M)}$$.^[We'd expect lower error from using all of
the chains as we have a larger sample with which to estimate.]

## How fast is convergence?

The fundamental theorem tells us that if a Markov chain $$Y = Y_1, Y_2,
\ldots$$ is ergodic (aperiodic and irreducible) and has a stationary
distribution, then the distribution of $$Y_t$$ converges to the
stationary distribution in the limit. But it doesn't tell us how fast.

As with everything else, we'll go at this by simulation to establish
intuitions. In particular, we'll consider three chains that have
$$\mbox{bernoulli}(0.5)$$ as their stationary distribution (a fair coin
toss).

First, we will consider a Markov chain producing independent Bernoulli
draws.

State diagram for finite Markov chain generating independent draws.
![](../images/Markov_chain_generating_independent_draws.jpg)

<!-- \begin{tikzpicture}[->, auto, node distance=1.75cm, font=\footnotesize]
\node[circle,draw,semithick] (A) {{\bf 0}};
\node[circle,draw,semithick] (B) [right of=A] {{\bf 1}};
\path (A) edge [bend left] node {0.5} (B);
\path (B) edge [bend left] node {0.5} (A);
\path (A) edge [loop above] node {0.5} (A);
\path (B) edge [loop above] node {0.5} (B);
\end{tikzpicture} -->


Whether it is currently in state 0 or state 1, there is a 50% chance
the next state is 0 and a 50% chance it is 1. Thus each element of the
process is generated independently and is identically distributed,

$$
Y_t \sim \mbox{bernoulli}(0.5).
$$

Therefore, the stationary distribution must also be $$\pi = (0.5, 0.5)$$,
because

$$
\begin{array}{rcl}
\pi_1 & = & \pi_1 \times \theta_{1, 1} + \pi_2 \times \theta_{2, 1}
\\[4pt]
0.5 & = & 0.5 \times 0.5 + 0.5 \times 0.5
\end{array}
$$

and

$$
\begin{array}{rcl}
\pi_2 & = & \pi_1 \times \theta_{1, 2} + \pi_2 \times \theta_{2, 2}
\\[4pt]
0.5 & = & 0.5 \times 0.5 + 0.5 \times 0.5.
\end{array}
$$


We can simulate 100 values and print the first 99 to see what the
chain looks like.


```{r}
print_3_rows_of_33 <-
function(y) {
  n <- 1
  for (i in 1:3) {
    for (j in 1:33) {
      printf("%1d ", y[n])
      n <- n + 1
    }
    printf("\n")
  }
}

sample_chain <- function(M, init_state, theta) {
  y <- c(init_state)
  for (m in 2:M)
    y[m] <- sample(x = 0:1, size = 1, prob = theta[y[m - 1] + 1, ])
  return(y)
}

traceplot_bool <- function (y) {
  df <- data.frame(iteration = 1:length(y), draw = y)
  plot <- ggplot(df, aes(x = iteration, y = draw)) +
    geom_line(aes(y = y)) +
    scale_x_continuous("iteration", breaks = c(1, 50, 100), expand = c(0, 0)) +
    scale_y_continuous("y", breaks = c(0, 1), expand = c(0.2, 0)) +
    ggtheme_tufte()
  return(plot)
}
```

```{r}
M <- 100
set.seed(1234)
y <- sample_chain(M, 1, matrix(c(0.5, 0.5,
                                   0.5, 0.5), 2, 2))

print_3_rows_of_33(y)
```

An initial segment of a Markov chain $$Y = Y_1, Y_2, \ldots, Y_T$$ can
be visualized as a traceplot, a line plot of the value at each
iteration.

Traceplot of chain producing independent draws, simulated for 100 time steps.  The horizontal axis is time ($$t$$) and the vertical axis the value of e iteration number and the value is the value ($$Y_t$$).
```
traceplot_bool <- function (y) {
  df <- data.frame(iteration = 1:length(y), draw = y)
  plot <- ggplot(df, aes(x = iteration, y = draw)) +
    geom_line(aes(y = y)) +
    scale_x_continuous("iteration", breaks = c(0, 50, 100),
                       expand = c(0, 0)) +
    scale_y_continuous("y", breaks = c(0, 1), expand = c(0.2, 0)) +
    ggtheme_tufte()
  return(plot)
}
traceplot_bool(y)
```

![](../images/Traceplot_of_chain_generating_independent_draws.jpg)

The flat segments are runs of the same value.  This Markov chain
occasionally has runs of the same value, but otherwise mixes quite
well between the values.

So how fast do estimates of the stationary distribution based on an
initial segment $$Y_1, \ldots, Y_T$$ converge to $$\frac{1}{2}$$? Because
each $$Y_t$$ is independent and identically distributed, the central
limit theorem tells us that the rate of convergence, as measured by
standard deviation of the distribution of estimates, goes down as
$$\frac{1}{\sqrt{T}}$$ with an initial segment $$Y_1, \ldots, Y_T$$ of the
chain goes down in error as $$\sqrt{T}$$

Now consider a Markov chain which is still symmetric in the states,
but with a tendency to stay in the same state.

State diagram for correlated draws."

<!-- \begin{tikzpicture}[->, auto, node distance=1.75cm, font=\footnotesize]
\node[circle,draw,semithick] (A) {{\bf 0}};
\node[circle,draw,semithick] (B) [right of=A] {{\bf 1}};
\path (A) edge [bend left] node {0.1} (B);
\path (B) edge [bend left] node {0.1} (A);
\path (A) edge [loop above] node {0.9} (A);
\path (B) edge [loop above] node {0.9} (B);
\end{tikzpicture} -->


It has the same stationary distribution of 0.5.  Letting $$\theta =
\begin{bmatrix}0.9 & 0.1 \\ 0.1 & 0.9 \end{bmatrix}$$ be the transition
matrix and $$\pi = (0.5, 0.5)$$ be the probabilities of the stationary
distribution we see that the general formula is satisfied by this
Markov chain,

$$
\begin{array}{rcl}
\pi_1 & = & \pi_1 \times \theta_{1, 1} + \pi_2 \times \theta_{2, 1}
\\[4pt]
0.5 & = & 0.5 \times 0.9 + 0.5 \times 0.1
\end{array}
$$

The same relation holds for $$\pi_2$$,

$$
\begin{array}{rcl}
\pi_2 & = & \pi_1 \times \theta_{1, 2} + \pi_2 \times \theta_{2, 2}
\\[4pt]
0.5 & = & 0.5 \times 0.1 + 0.5 \times 0.9
\end{array}
$$



We can simulate from the chain
and print the first 99 values, and then print the traceplot.

```{r}
set.seed(1234)
y <- sample_chain(M, 1, matrix(c(0.9, 0.1,
                                 0.1, 0.9), 2, 2))
print_3_rows_of_33(y)
```

<!-- {r fig.height=2, out.width = "40%", fig.cap = "Traceplot for chain with correlated draws."}
traceplot_bool(y) -->

![](../images/Traceplot_of_chain_with_correlated_draws.jpg)

As expected, there are now long runs of the same value being produced.
This leads to much poorer mixing and a longer time for estimates based
on the draws to converge.

Finally, we consider the opposite case of a symmetric chain that
favors moving to a new state each time step.

State diagram for anticorrelated draws.

![](../images/State_diagram_of_anticorrelated_draws.jpg)

<!-- \begin{tikzpicture}[->, auto, node distance=1.75cm, font=\footnotesize]
\node[circle,draw,semithick] (A) {{\bf 0}};
\node[circle,draw,semithick] (B) [right of=A] {{\bf 1}};
\path (A) edge [bend left] node {0.9} (B);
\path (B) edge [bend left] node {0.9} (A);
\path (A) edge [loop above] node {0.1} (A);
\path (B) edge [loop above] node {0.1} (B);
\end{tikzpicture} -->


Sampling, printing, and plotting the values produces
![](../images/sampling_figure-28.jpg)

Chain with anticorrelated draws.
![](../images/Traceplot_of_chain_with_anticorrelated_draws.jpg)

<!-- set.seed(1234)
y <- sample_chain(M, 1, matrix(c(0.1, 0.9,
                                 0.9, 0.1), 2, 2))
print_3_rows_of_33(y) -->


Traceplot of chain with anticorrelated draws.
<!--  -->traceplot_bool(y)


The draws form a dramatic sawtooth pattern as they alternate
between zero and one.

Now let's see how quickly estimates based on long-run averages from
the chain converge in a side-by-side comparison.
A single chain is enough to illustrate the dramatic differences.

Estimate of the stationary probability $$\\pi_1$$ of state 1 as a function of $$t$$ under three conditions, correlated, independent, and anticorrelated transitions.  For each condition, 25 simulations of a chain of size $$T = 10,000$$ are generated and overplotted.

![](../images/Estimate_of_the_stationary_probability.jpg)

<!-- set.seed(1234)
build_discrete_mcmc_df <- function(trans_matrix, label, J, M) {
  df <- data.frame(y = c(), x = c(), chain = c(), id = c())
  for (j in 1:J) {
    y <- cumsum(sample_chain(M, 1, trans_matrix)) /  (1:M)
    df <- rbind(df, data.frame(y = y, x = 1:M,
                               chain = rep(label, M), id = rep(j, M)))
  }
  df
}

corr_trans <- matrix(c(0.9, 0.1,
                       0.1, 0.9), 2, 2)
ind_trans <- matrix(c(0.5, 0.5,
                      0.5, 0.5), 2, 2)
anti_trans <- matrix(c(0.1, 0.9,
                       0.9, 0.1), 2, 2)

J <- 25
M <- 1e4
df_compare_discrete_mcmc <-
  rbind(build_discrete_mcmc_df(corr_trans, 'correlated', J, M),
        build_discrete_mcmc_df(ind_trans, 'independent', J, M),
        build_discrete_mcmc_df(anti_trans, 'anticorrelated', J, M)) -->




<!-- compare_discrete_mcmc_plot <-
  ggplot(df_compare_discrete_mcmc, aes(x = x, y = y, group = id)) +
  geom_hline(yintercept = 0.5, linetype = 'dotted', size = 0.5) +
  facet_wrap(vars(chain), ncol = 1) +
  geom_line(alpha = 0.4, size = 0.15) +
  scale_x_log10(lim = c(100, 10000), breaks = c(1e2, 1e3, 1e4),
                labels = c("100", "1,000", "10,000")) +
  scale_y_continuous(lim = c(.25, .75), breaks = c(0.25, 0.5, 0.75),
                     labels = c(".25", ".50", ".75")) +
  xlab('t') +
  ylab(expression(paste('estimated ', pi[1]))) +
  ggtheme_tufte() +
  theme(panel.spacing.y = unit(1, "lines"))
compare_discrete_mcmc_plot
 -->


## Reversibility

These simple Markov chains wind up being reversible, in that the
probability of being in a state $$i$$ and then transitioning to state
$$j$$ is the same as that of being in state $$j$$ and transitioning to
state $$i$$. In symbols, a discrete-valued Markov chain $$Y = Y_1, Y_2,
\ldots$$ is *reversible* with respect to $$\pi$$ if

$$
\pi_i \times \theta_{i, j}
\ = \
\pi_j \times \theta_{j, i}.
$$

Reversibility is sufficient for establishing the existence of a
stationary distribution.^[If a discrete Markov chain is reversible
with respect to $$\pi$$, then $$\pi$$ is also the unique stationary
distribution of the Markov chain.]  Markov chains can have stationary
distributions without being reversible.^[The reducible chains with we
saw earlier are examples with stationary distributions that are not
reversible.]  But all of the Markov chains we consider for practical
applications will turn out to be reversible.
