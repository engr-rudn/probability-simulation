---
title: "Discrete Markov Chains"
teaching: 
exercises:
questions:
objectives:

keypoints:

---

# Infinite Discrete Markov Chains

All of the Markov chains we have until now have had a finite number
of states.  In this chapter, we consider Markov chains with a
countably infinite number of states.  That is, they are still discrete,
but can take on arbitrary integer values.

## Drunkard's walk

The so-called *drunkard's walk* is a non-trivial Markov chain which
starts with value 0 and moves randomly right one step on the number
line with probability $$\theta$$ and left one step with probability $$1 -
\theta$$.

The initial value is required to be zero,

$$
p_{Y_1}(y_1) \ = \ 1 \ \mbox{ if } \ y_1 = 0.
$$

Subsequent values are generating with probability $$\theta$$ of adding
one and probability $$1 - \theta$$ of subtracting one,

$$
p_{Y_{t+1} \mid Y_t}(y_{t+1} \mid y_t)
\ = \
\begin{cases}
\theta & \mbox{if } \ y_{t + 1} = y_t + 1, \mbox{and}
\[4pt]
1 - \theta & \mbox{if } \ y_{t + 1} = y_t - 1.
\end{cases}
$$

Another way to formulate the drunkard's walk is by setting $$Y_1 = 0$$
and setting subsequent values to

$$
Y_{t+1} = Y_t + 2 \times Z_t - 1.
$$

where $$Z_t \sim \mbox{bernoulli}(\theta).$$ Formulated this way, the
drunkard's walk $$Y$$ is a transform of the Bernoulli process $$Z$$.  We
can simulate drunkard's walks for $$\theta = 0.5$$ and $$\theta = 0.6$$
and see the trend over time.

```
y[1] = 0
for (m in 2:M)
  z[m] = bernoulli_rng(theta)
  y[m] = y[m - 1] + (z[m] ? 1 : -1)
```

We'll simulate from both processes for $$M = 1000$$ steps and plot.

Drunkard's walks of 10,000 steps with equal chance of going left or right (blue) versus a sixty percent chance of going left (red).  The dotted line is drawn at the starting point. As time progresses, the biased random walk drifts further and further from its starting point.

![](../images/Drunkards_walk.jpg)

<!-- set.seed(1234)
M <- 10000
z1 <- rbinom(M, 1, 0.5)
z2 <- rbinom(M, 1, 0.6)
y1 <- cumsum(2 * z1 - 1)
y2 <- cumsum(2 * z2 - 1)

drunkards_df <-
  data.frame(x = c(1:M, 1:M),
             y = c(y1, y2),
             drunkard = c(rep("50% up / 50% down", M),
                          rep("60% up / 40% down", M)))

drunkards_plot <-
  ggplot(drunkards_df,
         aes(x = x, y = y, group = drunkard)) +
  geom_line() +
  geom_hline(yintercept = 0, linetype = "dotted") +
  facet_wrap(. ~ drunkard) +
  scale_x_log10(breaks = c(1, 10, 100, 1000, 10000)) +
  xlab("time") +
  ylab("position") +
  ggtheme_tufte() +
  theme(panel.spacing.x = unit(4, "lines"))
drunkards_plot -->


For the balanced drunkard, the expected drift per step is zero as
there is equal chance of going in either direction. After 10,000
steps, the expected position of the balanced drunkard remains the
origin.^[Contrary to common language usage, the expected position
being the origin after $$10,000$$ steps does not imply that we should
expect the drunkard to be at the origin. It is in fact very unlikely
that the drunkard is at the origin after 10,000 steps, as it
requires exactly 5,000 upward steps, the probability of which is
$$\mbox{binomial}(5,000 \mid 10,000, 0.5) = 0.008.$$] For the
unbalanced drunkard, the expected drift per step is $$0.6 \times 1 +
0.4 \times -1 = 0.2$$. Thus after 10,000 steps, the drunkard's
expected position is $$0.2 \times 10,000 = 2,000.$$




## Gambler's Ruin

Another classic problem which may be understood in the context of an
infinite discrete Markov chain is the gambler's ruin. Suppose a
gambler sits down to bet with a pile of $$N$$ chips and is playing a
game which costs one chip to play and returns one chip with a
probability of $$\theta$$.^[The original formulation of the problem,
involving two gamblers playing each other with finite stakes, was
analyzed in Christiaan Huygens. 1657. *Van Rekeningh in Spelen van
Geluck.* Here we assume one player is the bank with an unlimited
stake.] The gambler is not allowed to go into debt, so if the
gambler's fortune ever sinks to zero, it remains that way in
perpetuity. The results of the bets at times $$t = 1, 2, \ldots$$ can be
modeled as an independent and identically distributed random process
$$Z = Z_1, Z_2, \ldots$$ with

$$
Z_t \sim \mbox{bernoulli}(\theta).
$$

As usual, a successful bet is represented by $$Z_t = 1$$ and an
unsuccessful one by $$Z_t = 0$$.  The gambler's fortune can now be
defined recursively as a time series $$Y = Y_1, Y_2,
\ldots$$ in which the initial value is given by

$$
Y_1 = N
$$

with subsequent values defined recursively by

$$
Y_{n + 1}
\ = \
\begin{cases}
0 & \mbox{if} \ Y_n = 0, \ \mbox{and}
\[4pt]
Y_n + Z_n & \mbox{if} \ Y_n > 0.
\end{cases}
$$

Broken down into the language of Markov chains, we have an initial
distribution concentrating all of its mass at the single point $$N$$,
with mass function

$$
p_{Y_1}(N) = 1.
$$

Each subsequent variable's probability mass function is given by

$$
p_{Y_{t + 1} \mid Y_t}(y_{t + 1} \mid y_t)
\ = \
\begin{cases}
\theta & \mbox{if} \ y_{t + 1} = y_t + 1
\[4pt]
1 - \theta & \mbox{if} \ y_{t + 1} = y_t - 1.
\end{cases}
$$

These mass functions are all identical in that $$
p_{Y_{t+n+1} \mid Y_{t + n}} = p_{Y_{t + 1} \mid Y_t}.
$$  In other words, $$Y$$ is a time-homogeneous Markov chain.

We are interested in two questions pertaining to the gambler. First,
what is their expected fortune at each time $$t$$? Second, what is the
probability that they have fortune zero at time $$t$$.^[A gambler whose
fortune goes to zero is said to be *ruined.*]  Both of these
calculations have simple simulation-based estimates.

Let's start with expected fortune and look out $$T = 100$$ steps.
Suppose the chance of success on any given bet is $$\theta$$ and their
initial fortune is $$N$$. The simulation of the gambler's fortune is
just a straightforward coding of the time series.

```
y[1] = N
for (t in 2:T)
  z[t] = bernoulli_rng(theta)
  y[t] = y[t - 1] + (z[t] ? 1 : -1)
```

Now if we simulate that entire process $$M$$ times, we can calculate
the expected fortune as an average at each time $$t \in 1:T$$.

```
for (m in 1:M)
  y(m)[t] = N
  for (t in 2:T)
    z(m)[t] = bernoulli_rng(theta)
    y(m)[t] = y(m)[t - 1] + (z[t] ? 1 : -1)
for (t in 1:T)
  expected_fortune[t] = mean(y(1:M)[t])
```

Let's run $$M = 10,000$$ simulations for $$T = 50$$ starting with a stake
of $$N = 5$$ with several values of $$\theta$$ and plot the expected
fortunes.

Expected returns for gambler starting with stake $$N$$ and having a $$\theta$$ chance at each time point of increasing their fortune by 1 and a $$1 - \theta$$ chance of reducing their fortune by 1.  The horizontal dotted line is at the initial fortune and the dashed line is at zero.


<!-- set.seed(1234)
N <- 5
T <- 50
M <- 10000
Theta <- c(0.4, 0.5, 0.6)

df_ruin <- data.frame(x = c(), y = c(), theta = c())
for (theta in Theta) {
  y <- matrix(NA, M, T)
  for (m in 1:M) {
    y[m, 1] <- N
    for (t in 2:T) {
      if (y[m, t - 1] == 0) {
        y[m, t] <- 0
      } else {
        y[m, t] <- y[m, t - 1] + ifelse(rbinom(1, 1, theta), 1, -1)
      }
    }
  }
  expected_fortune <- rep(NA, T)
  for (t in 1:T) {
    expected_fortune[t] <- mean(y[1:M, t])
  }
  df_ruin <- rbind(df_ruin,
                   data.frame(x = 1:T,  y = expected_fortune,
                              theta = rep(paste("theta = ", theta), T)))
}

plot_ruin <-
  ggplot(df_ruin, aes(x = x, y = y, group = theta)) +
  geom_line() +
  geom_hline(yintercept = 5, linetype = 'dotted', size = 0.5) +
  geom_hline(yintercept = 0, linetype = 'dashed', size = 0.5) +
  facet_wrap(. ~ theta) +
  scale_x_continuous(breaks = c(1, 25, 50)) +
  scale_y_continuous(breaks = c(0, 5, 10, 15)) +
  xlab("time") +
  ylab("expected fortune") +
  ggtheme_tufte() +
  theme(panel.spacing.x = unit(4, "lines"))
plot_ruin -->


Next, we'll tackle the problem of estimating the probability that a
gambler has run out of money at time $$t$$. In symbols, we are
going to use simulations $$y^{(1)}, \ldots, y^{(M)}$$ of the gambler's
time series,
![](../images/Expected_return_for_gambler.jpg)
$$
\begin{array}{rcl}
\mbox{Pr}[Y_t = 0]
& = &
\mathbb{E}\left[ \mathrm{I}\left[ Y_t = 0 \right] \right].
\[6pt]
& \approx &
\displaystyle
\frac{1}{M} \sum_{m = 1}^M \, \mathrm{I}\left[ y_t^{(m)} = 0 \right].
\end{array}
$$

This last term can be directly calculated by adding the indicator
variables to the calculations before.

```
for (m in 1:M)
  y(m)[t] = N
  for (t in 2:T)
    z(m)[t] = bernoulli_rng(theta)
    y(m)[t] = y(m)[t - 1] + (z[t] ? 1 : -1)
    ruined(m)[t] = (y(m)[t] == 0)
for (t in 1:T)
  estimated_pr_ruin[t] = mean(ruined(1:M)[t])
```

So let's run that and plot the probability of ruin for the same three
choices of $$\theta$$, using $$M = 5,000$$ simulations.  But this time,
we'll run for $$T = 200$$ time steps.

Probability of running out of money for a gambler starting with stake $$N$$ and having a $$\theta$$ chance at each time point of increasing their fortune by 1 and a $$1 - \theta$$ chance of reducing their fortune by 1.  The horizontal dotted line is at 100 percent.

```
set.seed(1234)
N <- 5
T <- 200
M <- 5000
Theta <- c(0.4, 0.5, 0.6)

df_expect_ruin <- data.frame(x = c(), y = c(), theta = c())
for (theta in Theta) {
  y <- matrix(NA, M, T)
  for (m in 1:M) {
    y[m, 1] <- N
    for (t in 2:T) {
      if (y[m, t - 1] == 0) {
        y[m, t] <- 0
      } else {
        y[m, t] <- y[m, t - 1] + ifelse(rbinom(1, 1, theta), 1, -1)
      }
    }
  }
  pr_ruin <- rep(NA, T)
  for (t in 1:T) {
    pr_ruin[t] <- mean(y[1:M, t] == 0)
  }
  df_expect_ruin <-
    rbind(df_expect_ruin,
          data.frame(x = 1:T,  y = pr_ruin,
                     theta = rep(paste("theta = ", theta), T)))
}

plot_expect_ruin <-
  ggplot(df_expect_ruin, aes(x = x, y = y, group = theta)) +
  geom_line() +
  geom_hline(yintercept = 1, linetype = 'dotted', size = 0.5) +
  facet_wrap(. ~ theta) +
  scale_x_continuous(breaks = c(1, 100, 200)) +
  scale_y_continuous(lim = c(0, 1),
                     breaks = c(0, 0.25, 0.5, 0.75, 1),
		     labels = c("0", "1/4", "1/2", "3/4", "1")) +
  xlab("time") +
  ylab("probability of ruin") +
  ggtheme_tufte() +
  theme(panel.spacing.x = unit(4, "lines"))
plot_expect_ruin
```

Even in a fair game, after 50 bets, there's nearly a 50% chance that
a gambler starting with a stake of 5 is ruined; this probabiltiy goes
up to nearly 75% after 200 bets.


## Queueing

Suppose we have a checkout line at a store (that is open 24 hours a
day, 7 days a week) and a single clerk. The store has a queue, where
customers line up for service. The queue begins empty. Each hour a
random number of customers arrive and a random number of customers are
served.  Unserved customers remain in the queue until they are served.

To make this concrete, suppose we let $$U_t \in 0, 1, \ldots$$ be the
number of customers that arrive during hour $$t$$ and that it has a
binomial distribution,

$$
U_t \sim \mbox{binomial}(1000, 0.005).
$$

Just to provide some idea of what this looks like, here are 20
simulated values,
![](../images/simulated_values_20.jpg)

<!-- M <- 20
y <- rbinom(M, 1000, 0.005)
for (m in 1:M)
  printf("%2.0f ", y[m])
 -->

We can think of this as 1000 potential customers, each of which has a
half percent chance of deciding to go to the store any hour. If we
repeat, the mean number of arrivals is 5 and the standard deviation is
2.2.

Let's suppose that a clerk can serve up to $$V_t$$ customers per hour,
determined by the clerk's rate $$\phi$$,

$$
V_t \sim \mbox{binomial}(1000, \phi).
$$

If $$\phi < 0.005,$$ there is likely to be trouble.  The clerk
won't be able to keep up on average.

The simulation code just follows the definitions.

```
queue[1] = 0
for (t in 2:T)
  arrive[t] = binomial_rng(1000, 0.005)
  serve[t] = binomial_rng(1000, phi)
  queue[t] = max(0, queue[t - 1] + arrive[t] - serve[t])
```

The `max(0, ...)` is to make sure the queue never gets negative. If
the number served is greater than the total number of arrivals and
customers in the queue, the queue starts empty the next time step.

Let's try different values of $$\phi$$, the average server rate, and
plot two weeks of service.^[$$24 \mbox{hours/day} \ \times 14 \
\mbox{days} = 336 \mbox{hours}$$]


Multiple simulations of queue size versus time for a queue with $$\mbox{binomial}(1000, 0.005)$$ customers arriving per hour (an average of 5), and a maximum of $$\mbox{binomial}(1000, \phi)$$ customers served per hour, plotted for various $$\phi$$ (as indicated in the row labels).

![](../images/multiple_simulations_of_queue.jpg)

<!-- 
queue_df <- data.frame(t = c(), queue = c(), run = c(), phi = c())

set.seed(1234)
T <- 14 * 24
run <- 1
queue <- rep(NA, T)
for (m in 1:50) {
  for (phi in c(0.005, 0.0055, 0.006, 0.01)) {
    queue[1] <- 0
    for (t in 2:T) {
      arrived <- rbinom(1, 1000, 0.005)
      served <- rbinom(1, 1000, phi)
      queue[t] <- max(0, queue[t - 1] + arrived - served)
    }
    queue_df <-
      rbind(queue_df,
            data.frame(t = 1:T, queue = queue,
                       run = rep(run, T),
		       phi = rep(paste("phi = ", phi), T)))
    run <- run + 1
  }
}

queue_plot <-
  ggplot(queue_df, aes(x = t, y = queue, group = run)) +
  geom_line(alpha = 0.2, size = 0.2) +
  facet_wrap(. ~ phi) +  # facet_grid(rows = vars(phi)) +
  scale_x_continuous(breaks = c(1, 100, 200, 300)) +
  scale_y_continuous(breaks = c(0, 50, 100, 150)) +
  xlab("hours open") +
  ylab("queue size") +
  ggtheme_tufte() +
  theme(panel.spacing.y = unit(4, "lines")) +
  theme(legend.position = "none")
queue_plot -->


As can be seen in the plot, the queue not growing out of control is very sensitive to the average service rate per hour.  At an average rate of five customers served per hour (matching the average customer intake), the queue quickly grows out of control.  With as few as five and a half customers served per hour, on average, it becomes stable long term; with seven customers served per hour, things settle down considerably.  When the queue goes up to 50 people, as it does with $$\phi = 0.0055$$, wait times are over ten hours.  Because of the cumulative nature of queues, a high server capacity is required to deal with spikes in customer arrival.
