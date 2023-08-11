---
title: 'JSUparameters: Elevating Johnson $S_U$ Distribution Parameter Estimation in R'
tags:
  - R
  - finance
  - stock returns
  - Johnson SU distribution
  - JSU distribution
authors:
  - name: C.J. Clarke
    orcid: 0009-0004-7722-5686
    affiliation: 1
affiliations:
  - name: School of Mathematics and Statistics, University College Dublin, Ireland
    index: 1
date: 11 August 2023
bibliography: paper.bib
---

# Summary

Numerous techniques are available for parameter estimation of statistical distributions, including the method of moments, the maximum likelihood method, and the least squares method. Despite the method of maximum likelihood being the conventional statistical choice, it does exhibit certain limitations. This work centres on the utilisation of Quantile-Quantile (QQ) plots, optimised through the least squares approach, for the purpose of parameter estimation pertaining to a specific distribution (namely, the Johnson $S_U$ distribution).

# Statement of need

Initially, the normal distribution was employed to model fluctuations in stock market returns [@Davis:2006]. However, the occurrence of significant stock market crashes highlighted that these returns exhibit heavier tails compared to those assumed by a normal distribution. This realisation sparked substantial academic interest in the modelling of stock market returns, with a primary objective of mitigating the inherent risk associated with stock market investments [@Eom:2019]. As early as 1963, Mandelbrot noted that the distribution of stock returns displayed heightened peakedness and more pronounced tails than expected from a normal distribution [@Eom:2019].

In 2008, Choi and Nam suggested the utilisation of the Johnson $S_U$ distribution to characterize financial data, considering its capacity to accommodate the non-normal characteristics inherent in such data. Their application of the Johnson $S_U$ distribution proved successful, as demonstrated by their exceeding ratio tests, which indicated that the "SU-normal is superior to the normal and Student-t distributions, which consistently underestimate both the lower and upper extreme tails" [@ChoiNam:2008].
  
Similar to any statistical distribution, various approaches exist for fitting a Johnson $S_U$ distribution to a dataset. Historically, two common methods for estimating the parameters of the Johnson $S_U$ distribution were the method of moments [@Hill:1976] and the method of maximum likelihood [@Olsson:1979]. In 1988, [@Storer:1988] conducted a comprehensive comparison of fitting techniques for Johnson distributions. This involved a contrast between the method of moments, the method of maximum likelihood, and several iterations of the least squares method (ordinary, weighted, and diagonally weighted). The method of maximum likelihood demonstrated notable performance in many scenarios; however, their findings indicated that "while the case for the MLE appears fairly strong, further sampling is necessary ... the MLE owes some of its superior performance to the knowledge of the parent distribution" [@Storer:1988].

Introducing `JSUparameters`, an R package designed to facilitate effortless parameter estimation of the most suitable Johnson $S_U$ distribution for a given dataset (typically representing stock market returns). This tool serves the dual purpose of aiding financial researchers and supporting students enrolled in financial mathematics courses.

# Johnson $S_U$ distribution

The Johnson Unbounded distribution (Johnson $S_U$ distribution) [@JSU:1969], for a random variable $x$, is defined such that $z$ has a standard normal distribution, where:

$$
z = \gamma + \delta \sinh^{-1} \bigg(\frac{x - \xi}{\lambda}\bigg).
$$

An increasing relationship between $z$ and $x$ is desired, meaning that $\delta$ and $\lambda$ share the same sign. Without loss of generality, we assume positive values for both, while $\gamma$ and $\xi$ can take any real value.

This is equivalent to:

\begin{equation}\label{curve}x = \xi +  \lambda \sinh \left( \frac{z - \gamma} \delta \right).\end{equation}

Equation (\ref{curve}) is the curve we seek to estimate. To be more precise, our objective is to estimate the values of the parameters $\gamma$, $\delta$, $\xi$, and $\lambda$ by minimising the sum of squared differences between the observed data points and the curve obtained through fitting. In other words, our aim is to minimise:

$$
SSQ = \sum_{t=1}^T \left\{ x_t - \xi -  \lambda \sinh \left( \frac{z_t - \gamma} \delta \right) \right\}^2.
$$

We can see immediately that the residuals are linear in $\xi$ and $\lambda$, signifying that once $\gamma$ and $\delta$ are known, we can deduce $\xi$ and $\lambda$ through analytical means. Leveraging the hyperbolic sine's addition rule and bearing in mind the odd-even properties of $\sinh$ and $\cosh$, it follows that:

$$
\sinh \left( \frac{z_t - \gamma} \delta \right)  =  \cosh \left (\frac \gamma \delta \right) \sinh \left( \frac{z_t} \delta \right) - \sinh \left( \frac \gamma \delta \right) \cosh \left( \frac{z_t} \delta \right).
$$

Thus, the objective function can be rewritten as:

$$
SSQ = \sum_{t=1}^T \left\{ x_t - \xi -  \lambda \cosh \left (\frac \gamma \delta \right) \sinh \left( \frac{z_t} \delta \right) + \lambda \sinh \left( \frac \gamma \delta \right) \cosh \left( \frac{z_t} \delta \right) \right\}^2.
$$

Assuming an initial estimate for $\delta$ is available, we can employ linear regression to derive the coefficients $\xi$, $\lambda \cosh \left( \frac \gamma \delta \right)$ and $- \lambda \sinh \left( \frac \gamma \delta \right)$. In essence, this approach allows us to iteratively deduce the parameters. The quotient of the last two coefficients yields $\tanh \left( \frac \gamma \delta \right)$, serving as the basis for inferring both $\gamma$ and, ultimately, $\lambda$. 

The software tool `JSUparameters` encompasses the fundamental Johnson $S_U$ distribution, along with the subsequent three limiting scenarios of the Johnson $S_U$ distribution. This comprehensive coverage results in the identification of the distribution that achieves the optimal fit for the provided dataset.

## Shifted Log-normal

When $\gamma$ assumes a large negative value, it follows that for fixed $\delta$, $\frac{z-\gamma}\delta$ assumes a large value, leading to:

$$
\sinh \left( \frac{z-\gamma}\delta \right) \approx \frac 1 2 \exp \left( \frac{z-\gamma}\delta \right).
$$

To circumvent the challenge of identifiability, we arbitrarily fix $\gamma = - \delta \ln (2)$ and define the shifted log-normal as:

$$
x = \xi + \frac{\lambda} 2 \exp \left( \frac {z + \delta\ln 2} \delta \right) = \xi + \lambda \exp \left( \frac z \delta \right). 
$$

While $\delta$ remains non-linear, we can, nevertheless, determine $\xi$ and $\lambda$ using linear regression.

In the case where $\xi = 0$, the distribution simplifies to a log-normal distribution. In a broader context, $\xi$ serves as a lower bound for the distribution, commonly assuming a negative value, particularly in applications related to investment returns.

## Shifted Negative Log-normal

When $\gamma$ assumes a large positive value, it follows that fixed $\delta$, $\frac{z-\gamma}\delta$ assumes a large negative value, leading to:

$$
\sinh \left( \frac{z-\gamma}\delta \right) \approx -\frac 1 2 \exp \left( \frac{\gamma-z}\delta \right). 
$$

To circumvent a similar identifiability challenge, we arbitrarily fix $\gamma = \delta \ln (2)$, defining the shifted negative log-normal as:

$$
x = \xi - \frac \lambda 2 \exp \left( \frac{\delta \ln 2 -z}\delta \right) = \xi - \lambda \exp \left( - \frac z \delta \right).
$$

The upper bound of this distribution is set by $\xi$, while it has the capacity to reach arbitrarily large negative values.

## Constant
When $\lambda = 0$, the variable $x$ assumes a constant value of $\xi$.

# Flags  
Two potential indicators are integrated within this algorithm to address specific circumstances. The initial flag, labelled as `flag1`, serves as a precautionary measure. It triggers a warning in cases where the golden section search for the minimum of the $SSQ$ (sum of squared differences) could potentially lead to multiple local minima. It is worth noting that we hold the belief that the sorted least squares problem inherently possesses a singular optimal solution. Nonetheless, recognizing the absence of explicit proof for this assertion, the inclusion of this flag is intended to inform users of the algorithm should the remote possibility of multiple local minima arise.

The second flag, designated as `flag2`, assumes an analogous role by issuing a cautionary alert. This flag operates in scenarios involving the shifted log-normal or shifted negative log-normal cases. It raises a warning if any observation(s) in the provided dataset surpass the minimum or maximum values, respectively. This circumstance could present potential challenges for users, warranting the necessity to forewarn users about this potential issue.

# Application

An application of the `JSUparameters` package's utility is accessible within the associated GitHub repository.

# Authorship Contributions 

The entirety of the code for the `JSUparameters` package was authored by C.J. Clarke. The present research draws upon material derived from C.J. Clarke's M.Sc. thesis, titled "How can we use QQ plots for estimation?", under the guidance of Professor Andrew Smith at University College Dublin. Professor Smith played a significant role in the formulation of mathematical derivations underpinning this project, subsequently leveraged by C.J. Clarke in the development of the `JSUparameters` package.

# Acknowledgements

I wish to express my gratitude for the valuable support and mentorship provided by my thesis supervisor, Professor Andrew Smith. His contributions were instrumental in enabling the realisation of this project, including the development of the resulting software package.

# References
