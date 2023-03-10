---
title: "Typical Sets"
teaching: 
exercises:
questions:
objectives:

keypoints:

---
# Typical Sets

In this chapter, we'll cover some important properties of sets of
random draws from a distribution that provides important insight
into the behavior of samplers, particularly in higher dimensions.

## Most likely versus expected outcomes

Suppose we take a sequence $$Y = Y_1, \ldots, Y_N$$ of random variables
which are independent and identically distributed (i.i.d.) according
to
$$
Y_n \sim \textrm{bernoulli}(\theta).
$$
For concreteness, let's suppose $$N = 100$$ and $$\theta = 0.8$$.^[That
means there's an 80% chance $$Y_n = 1$$ and a 20% chance $$Y_n = 0.$$]
Given the i.i.d. assumption, we can write the joint probability
function as
$$
\begin{array}{rcl}
p_Y(y)
& = &
\prod_{n=1}^{100} p_{Y_n}(y_n)
\\[8pt]
& = &
\prod_{n=1}^{100} 0.8^{y_n} \cdot (1 - 0.8)^{1 - y_n}
\\[8pt]
& = &
0.8^{\textrm{sum}(y)} \cdot 0.2^{\textrm{sum}(1 - y)}.
\end{array}
$$

What's the most likely value for $$Y$$?  For each $$Y_n,$$ the most
probable result is success (i.e., 1).  Because the $$Y_n$$ are
independent, that means the most probable joint result is all
successes.  In symbols, this run of all successes maximizes the joint
probability mass function $$p_Y(y)$$,
$$
\begin{array}{rcl}
y^*
& = &
\textrm{arg max}_y \ p_Y(y).
\\[4pt]
& = &
( \underbrace{1, 1, \ldots, 1}_{100 \ \textrm{times}} ).
\end{array}
$$
Although a straight run of successes is the most likely result,
given that it requires one hundred consecutive successes with only an
eighty percent chance each, the overall probability is still very low.
$$
p_Y( \, (1, 1, \ldots, 1) \, )
\ = \
0.8^{100} \cdot 0.2^{0}
\ \approx \ 2.0 \cdot 10^{-10}.
$$

In other words, we should be quite surprised to see one hundred
straight successes.^[Joe DiMaggio's record 56 consecutive baseball
games with a hit is one of sports most unbreakable records. An eighty
percent chance of success per game requires 4 at bats with a .333
batting average (i.e., chance of success), $$(1 - 0.333)^4 \approx 0.2.$$]

The following plot demonstrates the range of total successes we're
likely to see by simulating 100 trials and counting the number of
successes. This whole process is repeated multiple times to produce
the data for the histogram.

```{r fig.cap="Histogram of $$100\\,000$$ simulations of the number of successes in 100 independent binary trials with an 80 percent chance of success per trial.  In symbols, this is a histogram of draws from $$y \\sim \\textrm{binomial}(100, 0.8).$$"}

y <- rbinom(1e5, 100, 0.8)
binom_typical_df <- data.frame(y = y)

binom_typical_plot <-
  ggplot(binom_typical_df, aes(x = y)) +
  geom_histogram(color = 'black', fill = '#ffffe6', size = 0.2,
                 binwidth = 1, center = 0.5) +
  scale_x_continuous(lim = c(0, 100), breaks = seq(0, 100, by = 20)) +
  scale_y_continuous(breaks = c()) +
  xlab("y (number of successes)") +
  ylab("count") +
  ggtheme_tufte()
binom_typical_plot  
```


What is the expected number of successes in one hundred independent
trials where each has an eighty percent chance of success? Eighty, as
the following derivation shows.
$$
\begin{array}{rcl}
\mathbb{E}[Y_1 + \cdots + Y_{100}]
& = &
\mathbb{E}[Y_1] + \cdots + \mathbb{E}[Y_{100}]
\\[4pt]
& = & \underbrace{0.8 + \cdots + 0.8}_{100 \ \textrm{times}}
\\[4pt]
& = &
80.
\end{array}
$$

How do we reconcile the fact that we expect to see eighty successes
when one hundred successes being the most likely outcome?  By
considering the fact that there is only a single sequence among the
$$2^{100} \approx 10^{30}$$ possible outcomes that constitutes all
successes, whereas there are a whole lot of sequences $$Y$$ with eighty
successes and twenty failures.  In fact, there are a whopping
$$
\binom{100}{80}
\ = \
\frac{100!}{80! \cdot (100 - 80)!}
\ \approx \ 5 \cdot 10^{20}
$$
possible ways to sequence eighty successes and twenty failures.
Therefore, the total probability of seeing eighty successes is the
product of the number of combinations with eighty successes (i.e.,
$$\binom{100}{80}$$) times the chance of any given outcome with eighty
successes (i.e., $$0.8^{80} \cdot 0.2^{20}$$),
$$
\begin{array}{rcl}
\mbox{Pr}[\textrm{sum}(Y) = 80]
& = &
\binom{100}{80}
\cdot 0.8^{80} \cdot 0.2^{20}
\\[4pt]
& \approx & 0.10
\end{array}
$$
So while there is almost no chance of seeing the most likely outcome
with all successes, there's nearly a ten percent chance of seeing an
outcome with eighty successes.^[This highlights a semantic point. Even
though eighty successes represents the expectation of
$$\textrm{sum}(Y),$$ there's still only a ten percent chance of
observing exactly eighty successes. So we don't necessarily expect to
see the expected value. This is even clearer with continuous
distributions, where the probability of seeing the expected value is
zero.]

The probability of $$y$$ successes in $$N$$ independent trials with a $$\theta$$ chance of success is given by $$\textrm{binomial}(y \mid N, \theta).$$  Working this out for $$N = 100$$ and $$\theta = 0.8$$, we have,
$$
\underbrace{\textrm{binomial}(80 \mid 100, 0.8)}%
_{\textrm{probability 80 successes total}}
\ = \
\underbrace{\ \ \ \binom{100}{80} \ \ \ }%
_{\textrm{order 80 successes}}
\cdot
\underbrace{\ \ 0.8^{80} \ \ }%
_{\textrm{80 specific successes}}
\cdot
\underbrace{\ \ 0.2^{20} \ \ }%
_{\textrm{20 specific failures}}
.
$$


To visualize how the number of sequences and probability of each of
the sequences interacts, we can plot everything.  The plots on the
left are on the linear scale and those on the right on the log scale.


```{r fig.asp = 0.4, fig.cap = "Total number of binary sequences of length one hundred in which exactly eighty values are one, i.e., $$\\binom{100}{y} = \\frac{100!}{80! \\cdot 20!}$$."}
choose_df <- function(Ys, theta) {
  N <- max(Ys)
  Ns <- rep(N, length(Ys))
  Cs <- choose(N, Ys)
  Ls <- theta^Ys * (1 - theta)^(N - Ys)
  Ps <- Cs * Ls
  data.frame(list(y = Ys, N = Ns, combos = Cs, L = Ls, P = Ps))
}

choose_plot <- function(df, logy = FALSE) {
  plot <- 
    ggplot(df, aes(x = y, y = combos)) +
    geom_point(size = 0.3) +
    scale_x_continuous(breaks = seq(0, 100, by = 20)) +
    xlab("y") +
    ylab(ifelse(logy, "log scale", 
                       "(100 choose y)")) +
    ggtheme_tufte() +
    theme(axis.title.x = element_text(size = 14),
          axis.title.y = element_text(size = 14),
	  axis.text.y=element_blank(),
          axis.ticks.y=element_blank())
  if (logy) plot <- plot + scale_y_log10()
  plot
}

choose100_df <- choose_df(0:100, 0.8)
cp <- choose_plot(choose100_df, FALSE)
lcp <- choose_plot(choose100_df, TRUE)
grid.newpage()
grid.arrange(ggplotGrob(cp), ggplotGrob(lcp),
             ncol = 2)
```

Next, we make parallel plots of the probability of single specific sequence with $$y$$ successes.^[For example, this might be the sequence where all the successes come first, $$( \, \underbrace{1, 1, \ldots 1}_{y \ \textrm{times}},
   \underbrace{0, 0, \ldots, 0}_{N - y \ \textrm{times}}).$$
The probability of a sequence of successes isn't dependent on the order of the successes, only their total number.]

```{r fig.asp = 0.4, fig.cap = "Probability of a specific single sequence which has $$y$$ successes."}
seq_plot <- function(df, logy = FALSE) {
  p <-
    ggplot(df, aes(x = y, y = L)) +
    geom_point(size = 0.3) +
    scale_x_continuous(breaks = seq(0, 100, by = 20)) +
    xlab("y") +
    ylab(ifelse(logy, expression("log scale"),
                      expression(0.8^y * 0.2^(N - y)))) +
    ggtheme_tufte() +
    theme(axis.title.x = element_text(size = 14),
          axis.title.y = element_text(size = 14),
          axis.text.y=element_blank(),
          axis.ticks.y=element_blank())
  if (logy) p <- p + scale_y_log10()
  p
}

pp <- seq_plot(choose100_df, FALSE)
lpp <- seq_plot(choose100_df, TRUE)
grid.newpage()
grid.arrange(ggplotGrob(pp), ggplotGrob(lpp),
             ncol = 2)
```

On the linear scale, we multiply the number of possible sequences with $$y$$
successes by the probability of a single sequence with $$y$$ successes,
to get the probability of drawing any sequence with $$y$$ successes.  On
the log scale, we add the logarithm of these quantities to get the
resulting log-scale plot.

```{r fig.asp = 0.4, fig.cap = "Probability of $$y$$ successes in 100 independent trials each of which has an 80 percent chance of success. The result is the probability mass function for $$\\textrm{binomial}(y \\mid 100, 0.8).$$"}

joint_plot <- function(df, logy = FALSE) {
  plot <-
    ggplot(df, aes(x = y, y = P)) +
    geom_point(size = 0.3) +
    scale_x_continuous(breaks = seq(0, 100, by = 20)) +
    xlab("y") +
    ylab(ifelse(logy, "log scale", "Pr[y]")) +
    ggtheme_tufte() +
    theme(axis.title.x = element_text(size = 14),
          axis.title.y = element_text(size = 14),
	  axis.text.y=element_blank(),
          axis.ticks.y=element_blank())
  if (logy) plot <- plot + scale_y_log10()
  plot
}

jp <- joint_plot(choose100_df, logy = FALSE)
ljp <- joint_plot(choose100_df, logy = TRUE)
grid.arrange(ggplotGrob(jp),  ggplotGrob(ljp),
             ncol = 2)
```

## Discrete typical sets and log probability

Continuing our running example, we still suppose
$$
Y_1, \ldots, Y_{100} \sim \textrm{bernoulli}(0.8),
$$
so that
$$
\textrm{sum}(Y) \sim \textrm{binomial}(100, 0.8).
$$
Now let's consider a sequence of simulations of $$Y$$,
$$y^{(1)}, \ldots, y^{(M)}.$$  Each such $$Y^{(m)}$$ consists of 100
$$\textrm{bernoulli}(0.8)$$ draws, $$Y^{(m)}_1, \ldots, Y^{(m)}_{100}.$$
Let's look at a scatterplot of the log probabilities of the simulated
$$Y$$, that is, a scatterplot of the joint log probability of all one
hundred elements,
$$
\begin{array}{rcl}
\log p_Y(y^{(m)})
& = &
\sum_{n = 1}^{100} \log p_{Y_n}(y^{(m)}_n)
\\[4pt]
& = &
\sum_{n = 1}^{100} \log \textrm{bernoulli}(y^{(m)}_n \mid 0.8).
\end{array}
$$
Here's the histogram.

```{r fig.cap = "Histogram of $$\\log p_Y(y^{(m)})$$ for simulations $$y^{(m)}$$ of one hundred binary trials with an eighty percent chance of success.  The histogram is spikey because there are only a hundred trials, and thus a small number of possible outcomes.  The number of observed outcomes is even smaller as they are concentrated around the expected value of 80 successes. The dashed line is at the log density of the mode (most likely value for $$Y$$), which consists of 100 successes.  The observed draws have log probability between -80 and -20, whereas the mode has a log probability greater than 0."}

set.seed(1234)
M <- 1e5
N <- 100
lp <- rep(NA, M)
for (m in 1:M) {
  y_m <- rbinom(N, 1, 0.8);
  lp[m] <- sum(dbinom(y_m, 1, 0.8, log = TRUE))
}

bern100df <- data.frame(x = lp)

bern100plot <-
  ggplot(bern100df, aes(x = x)) +
  geom_histogram(bins = 191,
                 color = "black", fill = "#ffffe6", size = 0.2) +
  geom_vline(xintercept = log(sum(dbinom(rep(1, 100), 1, 0.8))),
             linetype = "dashed", size = 0.2) +
  xlab("log p(y)") +
  ggtheme_tufte() +
  theme(axis.text.y=element_blank(),
        axis.ticks.y=element_blank())
bern100plot  
```

The takeaway message here is that the log probability of the observed draws fall in a narrow interval that's bounded far away from the log probability of the most probabiltiy element (the sequence consisting of all successes).

