# Essential statistical tests for analysing experiments

- [Essential statistical tests for analysing experiments](#essential-statistical-tests-for-analysing-experiments)
  - [What to Expect from the Article](#what-to-expect-from-the-article)
  - [Why Bring Up This Topic?](#why-bring-up-this-topic)
  - [Statistical Tests Are About the Hypothesis They Check](#statistical-tests-are-about-the-hypothesis-they-check)
  - [What Hypothesis Do We Want to Check](#what-hypothesis-do-we-want-to-check)
    - [Why Is Mean Difference So Good](#why-is-mean-difference-so-good)
    - [Other Practical Options (Exceptions?)](#other-practical-options-exceptions)
  - [Who Are You, Mr. T-test?](#who-are-you-mr-t-test)
    - [T-test vs Z-test](#t-test-vs-z-test)
    - [T-test options](#t-test-options)
  - [Beyond T-test](#beyond-t-test)
    - [Main insight](#main-insight)
    - [Bootstrapping](#bootstrapping)
    - [What's next?](#whats-next)

## What to Expect from the Article
The culture of experimentation has advanced significantly over the last decade. We've seen a leap not only in the volume of experimentation but also in the knowledge around statistics, AB methods, and other related technical fields. Companies release papers, try new methods, and arrange analytical meetups around it.

This article contains a brief summary of the essential statistical tests you need to know, as well as the logic that you can use to navigate through the methods in your future research. I will try to convince you that despite the huge variety of statistical tests, most of them are not applicable or less effective for the actual business cases we face. "T-test" and "Bootstrap" are still the kings of the hill for most jobs that need to be done.

**Important diclaimer**

The article aims to provide a practitioner's view on common statistical tests. My argumentation relies on cases from generic product analytics and data science. Please be careful in adopting conclusions if you are from an academic background or industries with high domain specificity.


## Why Bring Up This Topic?
Actually, the idea for this article came to me after repeatedly seeing the following diagrams in the LinkedIn Feed:

<details>
<summary>click here to view diagram 1 </summary>
<img src="/assets/pictures/diagram_1.jpg" alt="diagram_1" width="600" height="auto">
</details>


Or something like this:


<details>
<summary>click here to view diagram 2</summary>
<img src="/assets/pictures/diagram_2.webp" alt="diagram_2" width="600">
</details>


I was not sure how to deal with them, but thanks to Linus, the internet got this meme just in time:



<details>
<summary>:-)) </summary>
<img src="/assets/pictures/linus_meme.jpg" alt="linus_meme" width="600" height="auto">
</details>


Jokes aside, I believe both pictures follow similar logic and create quite an unhealthy perspective on picking the right test. To justify this statement, we need to go deeper into the statistical test definition and review some practical cases we usually face in the fields.

## Statistical Tests Are About the Hypothesis They Check

Diagrams that I showed earlier tend to group tests by the assumptions they require to perform the analysis. In most cases, these assumptions are quite technical:

- How does the sampled data look (discrete/continuous)?
- Do we have enough sample size?
- Do we know the variance?
- ...

In my point of view, it creates a false perception that the tests solve the same "business problem," and we just need to find the best technical fit for the experiment we have. There is a grain of truth in that logic, but it misses the most important part: each statistical test answers a very specific question (hypothesis), and this question should be carefully assessed from the analytical perspective, not just technical assumptions.

Statistical tests with different hypotheses could not replace each other without changing the experimenter's question. Here are some examples:

- **Pearson Chi squared test**:  goodness of fit, homogeneity, and independence.
- **T-test**: absolute mean difference
- **Mann-Whitney U test**: homogeneity
- **Bootstrapping**: technically it's not a statistical test, but rather a flexible method to compute many statistics (from mean difference to quantile difference)
- ...

**Mean Difference Hypothesis**

The distribution equality hypothesis tests whether two groups come from the same distribution, without necessarily assuming that distribution's specific shape or parameters. The hypotheses are formulated as:


*Null Hypothesis: The means of the two groups are equal.*


$\text{H}_0: \mu_1 = \mu_2$


*Alternative Hypothesis: The means of the two groups are not equal.*


$\text{H}_1: \mu_1 \neq \mu_2$


Statistical tests such as the T-test, Mean Difference Bootstrapping, and Ordinary Least Squares (OLS) regression can provide a clear and interpretable answer. These methods are suited for assessing the likelihood of observing the current data under the null hypothesis. This approach is analogous to evaluating your chances in a casino, focusing on quantifying the expected difference or lack thereof.

**Distribution Equality Hypothesis**

The distribution equality hypothesis tests whether two groups come from the same distribution, without necessarily assuming that distribution's specific shape or parameters. The hypotheses are formulated as:


*Null Hypothesis: The two groups have identical distributions.*


$H_0: F_1(x) = F_2(x)$


*Alternative Hypothesis: The two groups do not have identical distributions.*


$H_a: F_1(x) \neq F_2(x)$


Tests like the Chi-squared test, Mann-Whitney U test, and Kolmogorov-Smirnov (KS) test provide a binary answer—whether there is any detectable difference in distribution between the two groups. However, these tests might not easily indicate the nature or direction of the difference, making it challenging to ascertain if there was any positive impact.

**Do not mix the hypothesis**

The biggest sin we can make is to iterate over different hypothesis in order to find significan results or fitting the method to the data we got. The statistical test should be picked according to the experiment design hypothesis.


## What Hypothesis Do We Want to Check

### Why Is Mean Difference So Good

Among all possible hypotheses, we picked confidence interval for the difference of the sample means.

The sample mean is an estimation of the expected value. When it comes to the estimation of metric changes, the expected value has a direct and clear business interpretation.

Let’s say our main business goal is to increase the number of orders at Glovo. There are dozens of ways to do that: increase the number of customers who make an order, increase how many orders each customer makes, etc. At the end of the day, the business estimates success by the final sum of orders we made. How we achieved it is in the details. You can replace orders with other positive actions, but the logic will be the same. Expected value statistics perfectly cover the measurement:

$$E[X] = \sum_{i=1}^{n} x_i p_i$$


Increasing the expected number of orders for a population increases the overall metric. So, when we run the classic AB experiment with control and test variants randomized, we sample homogeneous groups from the same population. Increasing/decreasing the sample mean by AB impact is the direct approximation of the changes in the expected value of that population. Since the population is fixed during the AB experiment, the change of the expected value is the direct sign of change on the total sum of the metric, in our simplified case - the number of orders created.

Changes in median, percentiles, or general sample distribution could not guarantee same changes in total. Significant results from criteria that use these statistics could mislead and make us rolling out treatments without overall positive effects. It's particularly hazardous for stakeholders who are distanced from the mathematical details and view significance as the criterion for an experiment's success.

<details>
<summary>Detailed examples </summary>
Examples that demonstrate how changes in the median, percentiles, or even the overall distribution of a dataset might not affect the overall sum of the data. Note: each number is a point from an imaginary population:

**Example 1: Median**

Consider the following two data sets:

Data Set A: {1, 2, 3, 4, 5}
Data Set B: {1, 2, 4, 4, 4}

In both data sets, the sum of the numbers is 15. However, the median of data set A is 3, while the median of data set B is 4. Even though the median has changed, the sum remains the same.

**Example 2: Percentiles**

Consider these two data sets:

Data Set A: {1, 2, 3, 4, 5, 6, 7, 8, 9, 10}
Data Set B: {1, 2, 3, 4, 5, 6, 7, 7, 10, 10}

Both data sets have a sum of 55. The 90th percentile in data set A is 9, whereas in data set B it is 10.

**Example 3: Distribution**

Consider these two data sets:

Data Set A: {1, 1, 1, 1, 5}
Data Set B: {2, 2, 2, 2, 1}

In both cases, the sum is 9. But dataset A is heavily skewed with a mode of 1, while dataset B is more evenly distributed with a mode of 2.

Even though the median, percentiles, and distributions change, the sum of the data points stays constant. This is because the sum is simply the addition of all the data points and is not influenced by their order or frequency.

On the other hand, the mean is calculated by summing all values in a dataset and dividing by the number of values. Since the number of values is fixed by experiment samples, changes in mean represent the overall result. This property is unique to the mean and does not hold true for other measures of central tendency like the median or mode. Therefore, the mean can be considered a representation of the sum of the dataset.
</details>


Homogeneity criteria are specifically dangerous for interpretation, while percentile changes can be useful to track in some specific contexts.


### Other Practical Options (Exceptions?)

Before choosing another hypothesis, make sure you cannot convert your experiment design into a mean difference question. The idea to apply homogeneity criteria usually comes from the poor experiment design that needs to be fixed. Here are some examples:

Sometimes we research areas where the domain already has some common ways to "model the data":

- Application latency distribution,
- Clients binned by maturity groups,
- CRM open/convert tables.

This may push us to model our experiments accordingly:

- Application latency distribution -> homogeneity test for any changes
- Clients binned by maturity groups -> goodness of fit/homogeneity
- CRM open/convert tables -> independence for contingency tables


However, in most cases, the best solution is transforming the design into aiming a specific question that we care about:

- Application latency distribution -> 95th percentile value OR proportion of users reaching a specific pre-experiment threshold
- Clients binned by maturity groups -> mean difference sliced by pre-experiment base segmentation
- CRM open/convert tables -> mean difference in proportion

These transformations make our hypothesis precise, CI measurable, and less noisy.

I believe the main exception for the "always test the mean difference" rule of thumb can become using a percentile-based metric to test some extreme outliers and other threshold values. It's specifically important in all sorts of time metrics, fraud detection, etc.


To sum up, I recommend using the difference of the sample means as the default option due to its high effectiveness and good case coverage.


## Who Are You, Mr. T-test?

Now, let's get closer to our options for the mean difference hypothesis. I would like to explain the confusion around Z-test, T-test, paired T-test, T-test with pooled variance, Z-test for proportions, and other instances of the T-test family. The diagrams often suggest quite specific rules to pick the test across this list:

- If you know the population variance -> use Z-test
- If samples have the same variance -> use pooled variance.
- ...

If you come from an academic background, these suggestions make complete sense. This is indeed the correct list of differentiative properties between the tests. However, the nuances are in the details. Let's have a closer look.

### T-test vs Z-test

**Z-test:**

$$
Z = \frac{\bar{x}_1 - \bar{x}_2}{\sqrt{\frac{\sigma_1^2}{n_1} + \frac{\sigma_2^2}{n_2}}}
$$


Also, you may encounter this formula which is usually called "Proportion Z-test"

$$
Z = \frac{\bar{p}_1 - \bar{p}_2}{\sqrt{\frac{\bar{p_1}(1-\bar{p_1})}{n_1} + \frac{\bar{p_2}(1-\bar{p_2})}{n_2}}}
$$



This may look like a completely different test, but it actually is the same Z-test we have above. Proportion metrics come from the Binomial distribution. The Binomial distribution has a unique property: the variance is derived from its expected value. So, when the original Z-test formula states $\sigma$ for standard deviation, the proportion formula just meant to be more specific with STD computation for the binomial distribution: $\sigma^2=p(1−p)$. This does not change anything from the computation logic; the equation still just uses standard deviation as one of the terms.

**T-test:**


$$
T = \frac{\bar{x}_1 - \bar{x}_2}{\sqrt{\frac{s_1^2}{n_1} + \frac{s_2^2}{n_2}}}
$$


**What's the difference?**

The key difference between tests is the standard deviation term. The Z-test assumes we use the known true value $\sigma$. On the other side, the T-test uses $s$ which means the estimation of the standard deviation from the sampled data. Wait, but if the formulas are identical and we just changed "the term name," how will it impact the final results? Where would the difference come from?


It might be confusing since both formulas output the same statistic values (Z or T) for the same input data. However, the difference appears at the next step. In order to convert statistics into the statistical test, we need to have a null hypothesis distribution. That's the key part of the evaluation when we compare our result (statistic) with the "measurement ruler" (null distribution) to compute the chances of getting this or a more extreme value (p-value).

Even though tests compute the same statistics, they use different null hypothesis distributions!

The Z-test null hypothesis is validated against a Standard Normal distribution, and the T-test uses Student's distribution. What's the difference? Here is the main plot twist:

<details>
<summary>Student VS Normal visualization</summary>
<img src="/assets/pictures/T_Density.png" alt="Student VS Normal" width="600">
</details>


*The Standard Normal Distribution, Norm(0,1), is illustrated in black, whereas the Student's distribution for various degrees of freedom (interpreted as sample size) is depicted in different colors. Here's the intuition: For smaller degrees of freedom, the Student's distribution exhibits a larger variance to encompass the additional uncertainty arising from the estimation of standard variance. This increased variance is observable through the wider "tails" of the distribution.

However, as the degrees of freedom (sample size) increase, it narrows the gap between the estimated standard deviation of the sample and the true standard deviation of the population:

$$
SE=\sqrt{\frac{s_1^2}{n_1}} \longrightarrow \sqrt{\frac{\sigma^2}{n_1}}
$$

The precision of the sample variance ($s^2$) as an estimator for the true variance ($\sigma^2$) improves with increasing sample size due to the law of large numbers. This increased precision is reflected in the decreased standard error of the sample variance.

<details>
<summary>More formal explanation</summary>

Let $T_n$ be a Student's distribution with $n$ degrees of freedom. Then by definition:

$T_n = \frac{X}{\sqrt{Y_n/n}}$, where:

- $X \sim N(0,1)$ is sourced from the initial Z statistics distribution.
- $Y_n \sim \chi^2_n$ accounts for the variance estimation distribution. This term introduces additional variability compared to the original distribution.

Since $Y_n$ is distributed as

$$\sum_{i=1}^{n}X_i^2$$

Where $X_i$ are i.i.d standard normal, by the law of large numbers:

$$\frac{Y_n}{n} \longrightarrow E[X_1^2] = 1$$

Therefore:

$$\sqrt{\frac{Y_n}{n}} \longrightarrow \sqrt{1} = 1$$

and finally:

$$T_n \longrightarrow \frac{N(0,1)}{1} \longrightarrow N(0,1)$$


</details>


**What is the practical conclusions?**

In experiments, we never know the true standard deviation and always rely on the estimations from samples. The only exception here is metrics from the binomial distribution (proportions). However, it doesn't matter for us. Practically speaking, product experiments should always have enough sample size to neglect any difference between Student's distribution and Standard Normal. Hence, Z-test, Z-test for proportions, and T-test become effectively the same equation which is usually run against Norm(0,1) for simplicity, and there is no reason to use Student's distribution or even differentiate these tests. I believe that's why we often see a bit of confusion around it: some people call it a Z-test, others always use the term T-test, but actually use Z from Norm(0,1).

Unless you have a unique case with some extremely low sample size (less than 30), we don't need to be bothered by extra branching logic in our test-picking process. From now on in this article, I will use the T-test term to combine both of these methods under the same logic.

**Pro Tip:** Understanding that Student's distribution can be replaced with Normal is especially helpful when you are building some AB pipeline within the infrastructure, where there is no built-in Student's function. It usually happens when you use some Database or more obscure programming language. Then Normal distribution is way more accessible to find and usually has better support.


### T-test options

Another interesting option we can often see in the diagrams are:

- Pooled variance T-test
- Paired T-test

**Pooled variance T-test**

Here, let's be short. Pooled variance T-test assumes the samples we compare have equal variance. Hence, it keeps only one parameter:

Original:


$$T = \frac{\bar{x}_1 - \bar{x}_2}{s_p \cdot \sqrt{\frac{1}{n_1} + \frac{1}{n_2}}}$$



Pooled:

$$T = \frac{\hat{x_{1}} - \hat{x_{2}}}{s_{p}\sqrt{\frac{1}{n_{1}}}+\frac{1}{n_{2}}}$$


In my practice, I would say that variance equality between AB groups is a very strong assumption. It's hard to imagine a treatment that carefully moves the sample mean without introducing any side effects to the variance. Any non-constant or segment effects will immediately change the variance. Hence, I do not recommend using a Pooled T-test unless you have a very strong reason and a special case.


**Paired T-test**

This case is a bit more interesting. What is a paired T-test?

$$T = \frac{\hat{d}}{\frac{s_d}{\sqrt{n}}}$$

Where:
- $t$ is the t-statistic,
- $\bar{d}$ is the mean difference between the paired samples,
- $s_d$ is the standard deviation of the differences.

What happens is that we move to a new random variable, which is a paired difference between observations from control and test samples.

When test and control samples are independent, this approach does not give any special features and behaves as the regular T-test we discussed before, no issues or benefits are gained. However, if we can introduce dependency between these groups, then a new formula should provide lower variance by explaining a part of it by the original relationship between pairs.

Originally, most use cases that you can find are explaining the usage of this implementation when we have actual before-after pairs. One of the most popular examples describes the patient's body temperature that is measured before and after taking the pill. That sounds like an extremely unlikely design for an average product experiment. But, what if we can introduce some artificial dependency as part of the sample stratification before the experiment has started?

There is a brilliant article that explains this approach from my former colleague and brilliant analyst:

---

Dmitriy Lunin, [Article about paired test design and other methods to reduce variance](https://habr.com/en/companies/avito/articles/571096/).
*Warning! it's in Russian, but an internal Google Chrome page translator works fine.*

---

As the brief intro: Dima suggests performing user sorting by some important metric before the split. The sorted array is split sequentially: 1st in control, 2nd in test, 3rd in control... It might not be obvious from the first glance, but it creates a paired dependency that can later be used with the paired criteria to reduce the variance. The beauty of the paired test is that it also works with regular samples; we do not lose anything except the need to have a paired sample size (but this can be done by bucketing, I can write about this method later).

Anyway, please read Dima's article; it's just a beautiful piece of analytical research.


## Beyond T-test

Wait a second. In the process of writing this article, we just eliminated most of the "popular" tests and made a big stress on the importance and applicability of the T-test. What about other opportunities? Should we stop here? No, let me try to elaborate.


### Main insight

The first and most important insight: with the proper experimentation design in mind, the T-test will actually cover a bit portion of the experiments you are going to face in the industry. In my career, I would say up to 90% of experiments haven't required any special care. It's way more useful to spend time designing a proper product question or experiment groups. I highly recommend spending time understanding every detail of how these tests work. The beauty is in its simplicity, but there are many things to crack: the Central Limit Theorem, the Law of Large Numbers, Standard Error vs. Standard Error of the Mean.

### Bootstrapping

My second recommendation would be to learn a bootstrapping technique. It's hard to call a statistical test since it's more like a general method to get an empirical distribution which can later be used for computing p-value or confidence interval.

Bootstrapping involves taking many samples from your original data, but with a twist: these samples are taken with replacement. This means when creating a new sample (a "bootstrap sample"), you randomly pick data points from your original dataset, but after picking a data point, you put it back, allowing it to be potentially picked again.

It's better to describe rather than provide a formula:

- Pick the statistics you want to compute: mean, percentile, mean of two ratio metrics. Calculate the statistics of the test and control groups, and then compute the difference. That's the result you got. Next, we need to compute a CI around this result.
- Bootstrap sampling. For both test and control, independently create thousands of bootstrap samples. It's done by repeatedly randomly sampling the original test and control samples with replacement. A bootstrap sample should have the same number of observations as the original group.
- Compute the statistics you want to study in each of the thousand bootstrap groups like you did with the original samples.
- After repeating the above step thousands of times, you will have a distribution of bootstrapped difference in statistics. We can extract a confidence interval by using the percentile of this bootstrap distribution. If we take the 2.5th and 97.5th percentiles, this range should be a 95% confidence interval for our statistic.

Bootstrap methods work for constructing confidence intervals (CIs) and providing accurate estimations due to the principle of resampling. At its core, the bootstrap theory relies on the idea that the sample you have is a good representation of the larger population from which it was drawn. By repeatedly sampling with replacement from your original dataset to create many bootstrap samples, you effectively simulate the process of obtaining new samples from the actual population.

Here is an excellent paper that will make you familiar with what's happening under the hood:

---

Tim C. Hesterberg, 2015 [What Teachers Should Know About the Bootstrap:Resampling in the Undergraduate Statistics Curriculum](https://doi.org/10.1080/00031305.2015.1089789)

---
That's cool, right? No math formulas or complex logic, bootstrap works with a wide variety of cases when the vanilla T-test could not do the job. For example:

- Ratio metrics
- Percentiles or other exotic statistics.

Bootstrap is very robust and versatile since its empirical nature frees us from caring too much about estimating the variance -> we just bootstrap things and see how it looks.

In my opinion, T-test and bootstrap together can cover your back in most regular cases you will face in general product experiment analysis. You can stop here and focus on mastering the theory of the tools and the design principles (experimentation theory).


### What's next?

I strongly believe that after this point, the biggest ROI you can get comes from learning "supplementary techniques" to strengthen the tests we already discussed. Instead of trying more obscure hypotheses and deviating from the original transparency of the T-test, it's way better to learn methods that can enhance it:

**Variance reduction**

- Stratification / pairing techniques (link for Dima's article above)
- [CUPED, CUPAC](https://medium.com/glovo-engineering/variance-reduction-in-experiments-using-covariate-adjustment-techniques-717b1e450185)
- Post-normalization to reduce sample biases

**Metric coverage**

- [Delta method](https://medium.com/@ahmadnuraziz3/applying-delta-method-for-a-b-tests-analysis-8b1d13411c22) for relative mean difference and ratio metrics
- [Linerization](https://www.researchgate.net/publication/322969314_Consistent_Transformation_of_Ratio_Metrics_for_Efficient_Online_Controlled_Experiments)

These topics give a huge advantage: you keep all the benefits of T-test / Bootstrap simplicity but have a huge room to increase sensitivity and coverage "for free" without any harm to your business needs.

That's all for today!