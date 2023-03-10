---
title: "Calibration and Sharpness"
teaching: 
exercises:
questions:
objectives:

keypoints:

---

# Calibration and Sharpness

## Forecasting the weather

Meteorologists make predictions about the weather every day.  Some of
these forecasts are probabilistic.  For example, such a forecast might
be an 80% chance of rain over the next 24 hours in some geographic
area such as Dayton, Ohio.^[This is trickier to measure than it
sounds, but let's suppose for now that we can reliably determine if it
rained in a given area within a given period of time.]  We can't tell
much about the meteorologist from a single forecast and single
outcome---if it rains, we have a bit more faith in the forecaster and
if it doesn't, a bit less faith.

Suppose we have 100 days on which the meteorologist predicted an 80%
chance of rain.  Let's further suppose, for the sake of this thought
experiment, that the chance of rain is independent on those 100
days.^[In reality, weather in neighboring regions is related, as is
weather hour to hour and day to day.]  How many days do we expect to
be rainy?  About 80.  But we don't expect that number to be exact,
because we are dealing in probabilities.  If there really was an
independent 80% chance of rain on 100 days, we would expect the
distribution of rainy days to be $$\mbox{binomial}(100, 0.8)$$.

Another forecast we consider is temperature.  Let's say we're
forecasting the temperature at noon on February 1, 2019 on Bondi Beach
in Sydney. A probabilistic forecast of temperature might take the form
that there's a 90% chance the high temperature will be between 20.1
and 24.8 degrees celsius.  We can consider calibration of such a
forecast in terms of its event probabilities, just like the chance of
rain.  That 90% forecast is still very broad.  A *sharper* forecast is
one with a narrower interval, such as $$(22.3, 23.4)$$.  Given two
calibrated forecasts, a sharper forecaster is preferable as it
provides more information.  For binary events, like rain or not rain,
we'd prefer the probability forecasts to be as close to 0 or 1 as
possible.

**EXAMPLE OF HOW THIS IS POSSIBLE**

## Calibration 

With statistical models, forecasts take on the form of posterior
probability distributions conditioned on some observed data.


Given a prior distribution $$p(\theta)$$ and sampling function $$p(y \mid
\theta)$$, we observe data $$y$$ and calculate a posterior distribution
over the model's parameters with density $$p(\theta \mid y)$$.
Although the posterior is a distribution, we calculate it by means of
a finite number of simulated values $$\theta^{(1)}, \ldots,
\theta^{(M)}$$.

A posterior distribution provides probability statements about the
parameters $$\theta$$ conditioned on observing data $$y$$.  For example,
they provide interval probabilities, such as $$\mbox{Pr}[\theta > 0.5
\mid y]$$ or $$\mbox{Pr}[0.21 < \theta < 0.32 \mid y]$$.

We would like these event probabilities to be calibrated in the sense
that if $$\mbox{Pr}[\theta > 0.5]$$, then there is a 50% chance that
$$\theta$$ is greater than 0.5.

If our model has a prior
distribution with density $$p(\theta)$$ and a sampling distribution with
probability function $$p(y \mid \theta)$$, then the posterior density is
$$p(\theta \mid y)$$.  

The posterior distributiondensity $$p(\theta \mid y)$$ given data $$y$$ and model
parameters $$\theta$$ provides predictions about the value of $$\theta$$ 


Inference provides posterior draws $$\theta^{(m)}$$ drawn according to the posterior density 
Given some observed data $$y$$ and a model $$p(y \mid \theta) \times
p(\theta)$$ with parameters $$\theta$$, our inference