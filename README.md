# BrandLift: A Bootstrap Proportion Test for Brand Lift Testing to Quantify the Effectiveness of Advertising

We propose a new bootstrap test based on relative lift to test whether the means of two binomial distributions are the same or not. 
We apply the proposed the boostrap test to a real product Brand Lift Testing at LinkedIn to quantify the effectiveness of online advertising.
We developed an R package, namely BrandLift, and distributed it via Github. For more details and it applications, refer to the following papers

> Wanjun Liu, Xiufan Yu, Jialiang Mao, Xiaoxu Wu, and Justin Dyer. 2023. Quantifying the Effectiveness of Advertising: A Bootstrap Proportion Test
for Brand Lift Testing. In Proceedings of the 32nd ACM International Conference on Information and Knowledge Management (CIKM ’23).

To install the package, please use the following commands:
```
  devtools::install_github("TwoLittle/BrandLift")
  library(BrandLift)
```  
The package includes an introduction about the functions that can be used to carry out the conventional and proposed methods, as well as a simple example that demonstrates the usage. To check out the introduction and example, please use the following commands:
```
  help(package = 'BrandLift')
```
There are two functions in the package:
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
result <- proportion.test(sim.data, method = 'bootstrap')
relative.lift <- result$lift$relative
relative.lift.pval <- result$pvalue$relative
```
