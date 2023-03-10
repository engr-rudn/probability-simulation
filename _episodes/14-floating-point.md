---
title: "Floating Point Arithmetic"
teaching: 
exercises:
questions:
objectives:

keypoints:

---
# Floating Point Arithmetic

Contemporary^[Contemporary being 2019 as of this writing.] computers
use floating-point representations of real numbers and thus perform
arithmetic on floating-point representations.

## What is a floating point number?

A *bit* is the smallest discrete representational unit in a
computer---it takes on a value of 0 or 1.^[A *byte* consists of a
sequence of 8 bits.  The natural computing unit is a *word*, the size
of which is hardware dependent, but most computers nowadays (it's
still 2019) use 64-bit, or 8-byte words.]  Floating point numbers are
most commonly represented using 32 or 64 bits, which are known as
*single precision* and *double precision* respectively.^[Modern
machine learning floating point representations go as low as 8 bits
and high-precision applied mathematical calculations may use 1024 bits
or more.]

## Finite, not-a-number, and infinite values

A floating point number consists of a fixed number of bits for a
*significand* $$a$$ and a fixed number of bits for the *exponent* $$b$$ to
represent the real number $$a \times 2^b$$.  The significand
determines the precision of results and the exponent the range of
possible results.^[This exponent causes the decimal place to float,
giving the representation its name.]  The significand may be negative
in order to represent negative values.  The exponent may be negative
in order to represent fractions.  Both the significand and exponent
are represented using a single bit for a sign and the remaining bits
for the value in binary representation.  Standard double-precision
(i.e., 64 bit) representations allocate 53 bits for the significand
and 11 bits for the exponent.^[Zuras, D., Cowlishaw, M., Aiken, A.,
Applegate, M., Bailey, D., Bass, S., Bhandarkar, D., Bhat, M., Bindel,
D., Boldo, S. and Canon, S., 2008. *IEEE Standard 754-2008
(floating-point arithmetic).]

The standard also sets aside three special values, *not a number* for
ill-defined results, e.g., dividing zero by zero, *positive infinity*
for infinite results, e.g., dividing one by zero, and *negative
infinity* for negative infinity, e.g., dividing negative one by
zero.^[Technically, there are two forms of not-a-number and often two
forms of zero, but these distinctions are irrelevant in statistical
applications.]  These are all specified to have the expected behavior
with arithmetic and comparison operators and built-in
functions.^[Not-a-number typically propagates, but there are some
subtle interactions of not-a-number and comparison operators because
comparisons return integers in most languages.]

## Literals

Floating point numbers are written in computer programs using
*literals*, which can be integers such as `314`, floating point
numbers such as `3.14`, and scientific notation such as `0.314e+1`.
Scientific notation uses decimal notation, where `e+n` denotes
multiplication by $$10^n$$ and `e-n` by $$10^{-n}$$.  Multiplication by
$$10^n$$ shifts the decimal place $$n$$ places to the right;
multiplication by $$10^{-n}$$ shifts left by $$n$$ places.  For example,
`0.00314e+3`, `314e+2`, and `3.14` are just different literals
representing the same real number, $$3.14$$.

## Machine precision and rounding

If we add two positive numbers, we get a third number that's larger
than each of them.  Not so in floating point.  Evaluating


``` 1 == 1 + 10^-16 ```

produces the surprising result

```{r}
printf("%s", 1 == 1 + 10^-16)
```

When we add 1 and $$10^{-16}$$, we get 1.  This is not how arithmetic is
supposed to work.  We should get 1.0000000000000001.

The problem turns out to be that there's only so close to 1 we can get
with a floating point number.  Unlike with actual real numbers, where
there is always another number between any two non-identical numbers,
floating point numbers come with discrete granularity.  The closest we
can get to one is determined by the number of non-sign sigificand
bits, or $$2^{-52} \approx 2.2 \times 10^{-16}$$.  This is known as the
*machine precision* of the representation.  Writing out in decimal
rather than binary, the largest number smaller than one is

$$
1 - 2^{-52} = 0.\underbrace{999999999999999}_{\mbox{15 nines}}7,
$$

whereas the smallest number greater than one is 

$$
1 + 2^{-52} \approx 1.\underbrace{000000000000000}_{\mbox{15 zeros}}2.
$$

This numerical granularity and subsequent rounding of expressions like
`1 + 1e-20` to one is a serious problem that we have to fight in all
of our simulation code.


## Underflow and overflow

The most common problem we run into with statistical computing with
floating point is underflow.  If we try to represent something like a
probability, we quickly run out of representational power.  Simply
consider evaluating $$N = 2\,000$$ Bernoulli draws with a 50% chance of
success, 

```
p = 1
for (n in 1:N)
  p *= bernoulli(y[n] | 0.5)
print 'prob = ' p
```

The result should be $$0.5^{2000}$$.  What do we get?

```{r}
N <- 2000
theta <- 0.5
p <- 1
for (n in 1:N)
  p = p * dbinom(1, 1, theta)
printf('prob = %1.0f\n', p)
```

The result is exactly zero.^[It's not just rounding in the printing.]
What's going on?

Just as there's a smallest number greater than one, there's a smallest
number greater than zero.  That's the smallest positive floating point
number.  This number is defined by taking the largest magnitude negative
number for the exponent and the smallest number available for the
significand.  For the double-precision floating point in common use,
the number is about $$10^{-300}$$.^[$$10^{-322}$$ can be represented, but
$$10^{-323}$$ underflows to zero.]

## Working on the log scale

Because of the everpresent threat of underflow in statistical
calculations, we almost always work on the log scale.  Let's try that
calculation again, only now, rather than calculating

$$
\begin{array}{rcl}
p(y \mid \theta = 0.5)
& = & \prod_{n=1}^{2000} p(y_n \mid \theta = 0.5)
\\[6pt]
& = & \prod_{n=1}^{2000} \mbox{bernoulli}(y_n \mid 0.5)
\\[6pt]
& = & \prod_{n=1}^{2000} 0.5
\\[6pt]
& = & 0.5^{2000},
\end{array}
$$

we'll be calculating the much less troublesome

$$
\begin{array}{rcl}
\log p(y \mid \theta = 0.5)
& = & \log \prod_{n=1}^{2000} p(y_n \mid \theta = 0.5)
\\[6pt]
& = & \sum_{n=1}^{2000} \log p(y_n \mid 0.5)
\\[6pt]
& = & \sum_{n=1}^{2000} \log \mbox{bernoulli}(y_n \mid 0.5)
\\[6pt]
& = & \sum_{n=1}^{2000} \log 0.5
\\[6pt]
& = & 2000 \times \log 0.5.
\end{array}
$$

We can verify that it works by coding it up.

```
log_p = 0
for (n in 1:N)
  log_p += bernoulli(y[n] | 0.5)
print 'prob = ' log_p
```

We have replaced the variable `p` representing the probability with
a variable `log_p` representing the log probability.  Where `p` was
initialized to $$1$$, `log_p` is initialized to $$\log 1 = 0$$.  Where
the probability of each case was multiplied into the total, the log
probability is added to the total.  Let's see what happens with $$N =
2000$$.  

```{r}
N <- 2000
theta <- 0.5
log_p <- 0
for (n in 1:N)
  log_p = log_p + dbinom(1, 1, theta, log = TRUE)
printf('prob = %5.1f\n', log_p)
```

The result is indeed $$2000 \times \log 0.5 \approx -1386.29$$, as
expected.  Now we're in no danger of overflow even with a very large
$$N$$.  



## Logarithms of sums of exponentiated terms

When we take a logarithm, it is well known that it converts
multiplication into addition, so that

$$
\log \left( u \times v \right)
\ = \log u + \log v.
$$

But what if we have $$\log u$$ and $$\log v$$ and want to produce $$\log (u
+ v)$$?  It works out to

$$
\log \left( u + v  \right)
\ = \
\log \left( \exp(\log u) + \exp(\log v) \right).
$$

In words, it takes the logarithm of the sum of exponentiations.  This
may seem like a problematic amount of work, but there's an opportunity
here, as well.  By rearranging terms, we have

$$
\log (\exp(a) + \exp(b))
\ = \
\max(a, b) + \log (\exp(a - \max(a, b))
                   + \exp(b - \max(a, b))).
$$

This may seem like even more work, but when we write it out in code,
it's less daunting and serves the important purpose of preventing
overflow or underflow.

```
log_sum_exp(u, v)
  c = max(u, v)
  return c + log(exp(u - c) + exp(v - c))
```

Because $$c$$ is computed as the maximum of $$u$$ and $$v$$, we know that $$u
- c \leq 0$$ and $$v - c \leq 0$$.  As a result, the exponentiations will
not overflow.  They might underflow to zero, but that doesn't cause a
problem, because the maximum is preserved by being brought out front,
with all of its precision intact.

If we have a vector, it works the same way, only the vectorized
pseudocode is neater.  If `u` is an input vector, then we can compute
the log sum of exponentials as

```
log_sum_exp(u)
  c = max(u)
  return c + log(exp(u - c))
```

This is how we calculate the average of a sequence of values whose
logarithms are known.

```
log_mean(log_u)
  M = size(log_u)
  c = max(log_u)
  return -log(M) + c + log(exp(log_u - c))
```

## Failure of basic arithmetic laws and comparison

Because of the need to round to a fixed precision result, floating
point arithmetic does not satisfy basic transitivity or distributivity
laws of arithmetic.^[In general, we cannot rely on any of $$u + (v +
w) = (u + v) + w,$$ $$u \times (v \times w) = (u \times v) \times w, \
\mbox{or}$$, $$u \times (v + w) = u \times v = u \times w.$$]

One surprising artifact of this is rounding, where we can have $$u + v
= u$$, even for strictly positive values of $$u$$ and $$v$$.  For example,
$$u = 1$$ and $$v = 10^{-20}$$ have this property, as do $$u = 0$$ and $$v =
10^{-350}.$$

The upshot is that we have to be very careful when comparing two
floating point numbers, because even though pure mathematics might
guarantee the equality of two expressions, they may not evaluate to
the same result in floating point arithmetic.  Instead, we need to
compare floating-point numbers within tolerances.

With absolute tolerances, we replace exact comparisons with
comparisons such as `abs(u - v) < 1e-10`.  This tests that `u` and `v`
have values within $$10^{-10}$$ of each other.  This isn't much use if
the numbers are themselves much larger or much smaller than $$10^{-10}$$.

There are many ways to code relative tolerances.  One common approach
is to use `2 * abs(u - v) / (abs(u) + abs(v)) < 1e-10`.  For example,
the numers $$10^{-11}$$ and $$10^{-12}$$ are within an absolute tolerance
of $$10^{-10}$$ of each other,

$$
\begin{array}{rcl}
\left| 10^{-11} - 10^{-12} \right|
& = & 9 \times 10^{-12} 
\\[2pt]
& < & 10^{-10}
\end{array}
$$

but they are not within a relative tolerance of $$10^{-10}$$,

$$
\begin{array}{rcl}
\frac{\displaystyle 2 \times \left| 10^{-11} - 10^{-12} \right|}
     {\displaystyle \left| 10^{-11} \right| + \left| 10^{-12} \right|}
& \approx & 1.63
\\[6pt]
& > & 10^{-10}.
\end{array}
$$


## Loss of precision

Once we have precision, we have to work very hard not to lose it.
Even simple operations like subtraction are frought with peril.  When
taking differences of two very close numbers, there can be an
arbitrary amount of loss of precision.^[When the loss of precision is
great relative to the total precision, this is called *catastrophic
cancellation*.]  As a simple example, consider

$$
\begin{array}{rr}
  & 0.7595127881504595
\\
- & 0.7595127881504413
\\ \hline
& 0.0000000000000182
\end{array}
$$

We started with two numbers with 15 digits of precision and are
somehow left with only 3 digits of precision.  In statistical
computing, this problem arises in calculating variances, which involve
differences of variates from the mean---when variance is low relative
to the mean, catastrophic cancellation may arise.

