# LiftTest: A Bootstrap Proportion Test for Brand Lift Testing to Quantify the Effectiveness of Advertising

We propose a new bootstrap test based on relative lift to test whether the means of two binomial distributions are the same or not. 
We apply the proposed the boostrap test to a real product Brand Lift Testing at LinkedIn to quantify the effectiveness of online advertising.
We developed an R package, namely LiftTest, and distributed it via Github. For more details and it applications, refer to the following paper

> Wanjun Liu, Xiufan Yu, Jialiang Mao, Xiaoxu Wu, and Justin Dyer. 2023. Quantifying the Effectiveness of Advertising: A Bootstrap Proportion Test
for Brand Lift Testing. In Proceedings of the 32nd ACM International Conference on Information and Knowledge Management (CIKM ’23).

To install the package, please use the following commands:
```
  devtools::install_github("TwoLittle/LiftTest")
  library(LiftTest)
```  
The package includes an introduction about the functions that can be used to carry out the conventional and proposed methods, as well as a simple example that demonstrates the usage. To check out the introduction and example, please use the following commands:
```
  help(package = 'LiftTest')
```
There are four main functions in the package:
```
gen.simu.data(n1, n2, p1, p2, summary=TRUE)
```
which generates binomial random samples for the control group (with sample size n1 and success probability p1) and the treatment group
(with sample size n2 and success probability p2).

```
proportion.test(data, method = 'bootstrap', B = 1000)
```
which implements the bootstrap proportion test together with serveal other methods that can be applied to Brand Lift Testing. In partibular, 4 methods are provided:
* `clt`: Absolute lift based Z-test and relative lift based Z-test. The limiting distribution of Z-statistics are derived from the central limit theorem.
* `bootstrap`: Absolute lift based bootstrap test (BS-A) and relative lift based bootstrap test (BS-R), see Liu et al., (2023).
* `bootstrapmean`: Absolute lift based bootstrap mean test and relative lift based bootstrap mean test. (Efron and Tibshirani 1994).
* `permutation`: Absolute lift based permutation test and relative lift based permutation test. (Efron and Tibshirani 1994).

```
get.asymp.power(n1, n2, p1, p2, method='relative', alpha=0.05)
```
which generates the asymptotic power of the proposed bootstrap test.

```
get.min.size(p1, p2, p_treat, method='relative', power=0.8, alpha=0.05)
```
which generates the minimum sample size required to obtain a statistically significant result for a given power.

### Which method should I use?
* `bootstrap` should be used when prior information such as lift is positive (i.e., treatment group mean - control group mean is positive) is available. In practice, we recommend using `bootstrap` for applications such as **Brand Lift Testing**, **Conversion Lift Testing**, **Sales Lift Testing**, etc.
* `bootstrapmean` has very similar performance to `bootstrap`, and can be easily extended to non-binary data. The key difference between `bootstrap` and `bootstrapmean` is that `bootstrap` tests whether control and treatment have the same distribution while `bootstrapmean` tests whether control and treatment has the same mean. With binary data, testing whether two groups have the same distribution is equivalent to testing whether the two groups have the same mean. Therefore `bootstrapmean` has very similar performance to `bootstrap`.
* `permutation` also has very similar performance to `bootstrap` but more computationally expensive. The nice propery of `permutation` is that it is an exact test while all other 3 methods are based on some asymptotic distribution or approximation.
* `clt` is the standard proportion test with central limit theorem. It should be when no prior information such as lift is positive (i.e., treatment group mean - control group mean is positive) is available and when the sample size is relatively large.

### How to use
```
n1 <- 100
n2 <- 100
p1 <- 0.1
p2 <- 0.2
set.seed(1)

# generate simulated data
sim.data <- gen.simu.data(n1, n2, p1, p2)
sim.data

# run the proportion test using method bootstrap
result <- proportion.test(sim.data, method = 'bootstrap', B = 1000)
relative.lift <- result$lift$relative
relative.lift.pval <- result$pvalue$relative

# get the asymptotic power with relative lift
get.asymp.power(n1, n2, p1, p2, method='relative')

# get the minimum sample size with power 80%
get.min.size(p1, p2, p_treat=0.5, method='relative', power=0.8, alpha=0.05)
```

### Unit test
We utilize the R package `testthat` to perform the unit test, which is the official/recommended approach to test a new R package.
We implemented 6 test cases and all of them pass the unit tests.
```
# load parameters
n1 <- 100
n2 <- 100
p1 <- 0.1
p2 <- 0.2
set.seed(1)

# generate simulated data
sim.data <- gen.simu.data(n1, n2, p1, p2)
# run the proportion test using method bootstrap
result <- proportion.test(sim.data, method = 'bootstrap', B=1000)
relative.lift <- result$lift$relative
relative.lift.pval <- result$pvalue$relative
```

* the asymptotic power under null is 0.05
```
test_that("the asymptotic power under null is 0.05", {
  expect_equal(get.asymp.power(100, 100, 0.1, 0.1, 'relative'), 0.05)
}) #Output = 0.05, Pass
```

* the asymptotic power under alternative is 1 when sample size is large
```
test_that("the asymptotic power under H1 is 1 when sample size is large", {
  expect_equal(get.asymp.power(2000, 2000, 0.1, 0.2, 'relative'), 1.0)
}) #Output = 1.0, Pass
```

* the summarized the simulation data should be 2x2 matrix
```
test_that("the summarized the simulation data should be 2x2 matrix", {
  expect_equal(dim(gen.simu.data(100, 100, 0.1, 0.2)), c(2, 2))
}) #Output = c(2,2), Pass
```

* the min sample size is infinity under null hypothesis
```
test_that("the min sample size is infinity under null hypothesis", {
  expect_equal(get.min.size(0.1, 0.1, 0.5, 'relative', 0.8, 0.05), Inf)
}) #Output = Inf, Pass
```

* the p-value should be <= 1
```
test_that("the p-value should be <= 1", {
  expect_lt(relative.lift.pval, 1)
}) #Output = 0.009, Pass
```

* the p-value should be >= 0
```
test_that("the p-value should be >= 0", {
  expect_gt(relative.lift.pval, 0)
}) #Output = 0.009, Pass
```
