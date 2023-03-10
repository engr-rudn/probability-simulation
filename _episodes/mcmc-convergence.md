---
title: "Monitoring Approximate Convergence"
teaching: 
exercises:
questions:
objectives:

keypoints:

---
# Monitoring Approximate Convergence

We know that if we run an ergodic Markov chain long enough, it will
eventually approach stationarity.  We can even use the entire chain for
estimation if we run long enough because any finite error will become
infinitesimal in the asymptotic regime.

However, we do not live in the asymptotic regime.  We need results
that we can trust from finitely many draws.  If we happened to know
our chains were geometrically ergodic, so that we know they converged
very quickly toward the stationary distribution, then our lives would
be easy and we could proceed with a single chain and as long as there
weren't numerical problems, we could run it a long time and trust it.

We are also faced with distributions defined implicitly as posteriors,
whose geometric ergodicity is difficult, if not impossible, to
establish theoretically.  Therefore, we need finite-sample diagnostics
in order to test whether our chains have reached a stationary
distribution which we can trust for estimation.

## Running multiple chains

One way to diagnose non-convergence is to run multiple chains and
monitor various statistics, which should match among the different
chains.  As an example, consider the two-dimensional Metropolis
example.  Starting four chains in the corners and running for various
number of time steps shows how the chains approach stationarity.

```{r fig.width = 9, fig.asp = 0.4, out.width = "100%", fig.cap = "The evolution of four random walk Metropolis Markov chains, each started in a different corner of the plot.  The target density is bivariate normal with correlation 0.9 and unit variance;  the random walk step size is 0.2.  After $$M = 50$$ iterations, the chains have not arrived at the typical set.  After $$M = 500$$ iterations, the chains have each arrived in the typical set, but they have not had time to mix.  After $$M = 5000$$ iterations, the chains are mixing well and have visited most of the target density."}
sigma <- 0.2
theta0s <- list(c(4, 4), c(4, -4), c(-4, 4), c(-4, -4))
conv_df <- data.frame(t = c(), theta1 = c(), theta2 = c(), id = c())
for (M in c(50, 500, 5000)) {
  set.seed(1234)
  for (theta0 in theta0s) {
    theta_sim <- random_walk_metropolis(binorm_lpdf, theta0, sigma, M)
    conv_df <-
      rbind(conv_df,
            data.frame(t = 1:M, theta1 = theta_sim[ , 1], theta2 = theta_sim[ , 2],
	               M = rep(paste("M = ", M), M),
                       id = rep(sprintf("(%1f, %1f)", theta0[1], theta0[2]), M)))
  }
}
conv_plot <-
  ggplot(conv_df, aes(x = theta1, y = theta2, color = id, group = id)) +
  facet_wrap(. ~ M) +
  geom_path(size = 0.5, alpha = 0.5) +
  scale_x_continuous(lim = c(-5, 5), breaks = c(-4, 0, 4)) +
  scale_y_continuous(lim = c(-5, 5), breaks = c(-4, 0, 4)) +
  xlab(expression(theta[1])) +
  ylab(expression(theta[2])) +
  ggtheme_tufte() +
  theme(legend.position = "none") +
  theme(panel.spacing.x = unit(1, "lines")) +
  theme(panel.spacing.y = unit(1, "lines"))
conv_plot
```
