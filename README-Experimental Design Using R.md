# 📘 Experimental Design Using R

> **Author:** Ms. Mrunalini (Data Science Trainer)
> 📧 mrunalini0107@gmail.com | 📍 Mumbai – 400095

---

## 📋 Table of Contents

1. [Introduction](#introduction)
2. [Topics Covered](#topics-covered)
   - [1. Bonferroni Correction – Overall Alpha vs. Number of Tests](#1-bonferroni-correction--overall-alpha-vs-number-of-tests)
   - [2. Influence Index Plot](#2-influence-index-plot)
   - [3. Outlier Test](#3-outlier-test)
   - [4. Post-hoc Power Calculation](#4-post-hoc-power-calculation)
   - [5. A Priori Power Calculation](#5-a-priori-power-calculation)
   - [6. Analytical Power Curve](#6-analytical-power-curve)
   - [7. Power Calculation via Single Simulation](#7-power-calculation-via-single-simulation)
   - [8. Power via 10,000 Simulated t-tests (n = 20)](#8-power-via-10000-simulated-t-tests-n--20)
   - [9. Power via 10,000 Simulated t-tests (n = 40)](#9-power-via-10000-simulated-t-tests-n--40)
   - [10. Simulation Power Using Nested Loops](#10-simulation-power-using-nested-loops)
   - [11. Combined Power Curve – Analytical vs. Simulation](#11-combined-power-curve--analytical-vs-simulation)
   - [12. Sensitivity Analysis – Power Curve by Effect Size](#12-sensitivity-analysis--power-curve-by-effect-size)
3. [Learning Objectives](#learning-objectives)
4. [Project Structure](#project-structure)
5. [Requirements](#requirements)
6. [How to Run](#how-to-run)
7. [Conclusion](#conclusion)

---

## Introduction

This notebook provides a comprehensive, practical guide to **Experimental Design** using R, with a focus on two foundational pillars of rigorous research: **error rate control across multiple comparisons** and **statistical power analysis**.

In real-world experiments — clinical trials, ecological field studies, educational interventions — researchers routinely make dozens or hundreds of statistical decisions simultaneously. Without proper safeguards, the risk of drawing false conclusions escalates rapidly. This notebook teaches, step by step, how to quantify that risk, correct for it, and design experiments that are adequately powered to detect effects of scientific interest.

The notebook blends **theoretical derivation** with **hands-on R simulation**, covering everything from the mathematics of the Bonferroni correction to regression diagnostics and Monte Carlo power curves. Each section includes explicit R code, fully annotated interpretations, and visualisations that bridge statistical theory and applied practice.

Whether you are designing a new study, reviewing a published analysis, or learning statistics from scratch, this notebook equips you with the core tools for sound experimental reasoning.

---

## Topics Covered

### 1. Bonferroni Correction – Overall Alpha vs. Number of Tests

**Description:**
When multiple hypothesis tests are performed simultaneously, the probability of making at least one false positive (Type I error) increases beyond the nominal significance level. This section introduces the **Family-Wise Error Rate (FWER)** and demonstrates how the **Bonferroni correction** restores control of the overall error rate.

The adjusted significance threshold is:

$$\alpha_{\text{Bonferroni}} = \frac{\alpha}{k}$$

The overall Type I error rate after correction is given by:

$$\text{FWER} = 1 - \left(1 - \frac{\alpha}{k}\right)^k$$

R code plots this formula across values of $k$ from 4 to 50, showing graphically that the Bonferroni correction maintains the FWER close to the desired $\alpha = 0.05$ regardless of how many tests are performed. A reference line at $\alpha = 0.05$ is added to confirm effective control.

---

### 2. Influence Index Plot

**Description:**
Before drawing conclusions from a regression model, it is essential to check whether any individual observations exert undue influence on the fitted results. This section uses the `influenceIndexPlot()` function from the `car` package to produce a diagnostic dashboard for a linear regression model fitted to the **Duncan occupational prestige dataset**.

**Model fitted:**

$$\text{Prestige} = \beta_0 + \beta_1\,(\text{Income}) + \beta_2\,(\text{Education}) + \varepsilon$$

The influence index plot displays four key diagnostics side by side:

| Diagnostic | What It Detects |
|---|---|
| **Cook's Distance** | Overall influence of each observation on all fitted values |
| **Studentized Residuals** | Outliers in the response variable (Y-space) |
| **Hat Values (Leverage)** | Extreme predictor combinations (X-space) |
| **DFFITS** | Change in fitted value when each observation is removed |

Observations flagged across multiple diagnostics warrant careful scrutiny before final model interpretation.

---

### 3. Outlier Test

**Description:**
The `outlierTest()` function from the `car` package performs a **Bonferroni-adjusted outlier test** based on Studentized residuals. This section applies the test to the same Duncan dataset regression model to formally identify whether any observation constitutes a statistically significant outlier.

The procedure computes Studentized residuals for all observations, identifies the observation with the largest absolute residual, and applies a Bonferroni correction to account for the implicit multiple testing across all $n$ observations. A manual verification calculation — multiplying the raw $p$-value by the sample size ($0.0031772 \times 45$) — illustrates the Bonferroni adjustment directly.

The occupation *minister* is identified as having the largest Studentized residual (≈ 3.13), suggesting it deviates meaningfully from the fitted regression surface.

---

### 4. Post-hoc Power Calculation

**Description:**
**Post-hoc power analysis** evaluates the statistical power of a study *after* the data have been collected, using the observed sample size and effect size. This is commonly used to contextualise a non-significant result — was the study adequately powered to detect the effect of interest?

Using R's built-in `power.t.test()`, this section calculates the power of a two-sample t-test with:

| Parameter | Value |
|---|---|
| Sample size per group ($n$) | 15 |
| Effect size ($\delta$) | 1 |
| Significance level ($\alpha$) | 0.05 |
| Test type | Two-sided |

**Result:** Power ≈ **75.3%** — reasonably good, but below the conventional 80% threshold, suggesting the study was slightly underpowered.

---

### 5. A Priori Power Calculation

**Description:**
**A priori power analysis** is performed *before* data collection to determine the minimum sample size needed to achieve a target level of statistical power. This is the gold standard for research planning.

Using `power.t.test()` with the desired power specified and sample size left unknown:

| Parameter | Value |
|---|---|
| Desired power | 0.80 (80%) |
| Effect size ($\delta$) | 1 |
| Significance level ($\alpha$) | 0.05 |
| Test type | Two-sided |

**Result:** At least **17 observations per group** are required to achieve 80% power for this effect size and significance level.

---

### 6. Analytical Power Curve

**Description:**
A **power curve** visualises how statistical power changes as a function of sample size, for a fixed effect size, standard deviation, and significance level. This section generates the theoretical (analytical) power curve using `power.t.test()` across sample sizes from $n = 3$ to $n = 200$.

Parameters used:

| Parameter | Value |
|---|---|
| Effect size ($\delta$) | 1 (mean difference: 18 − 17) |
| Standard deviation ($\sigma$) | 2 |
| Significance level ($\alpha$) | 0.05 |
| Sample sizes | 3 to 200 |

**Key finding:** Power reaches the 80% threshold at approximately **n ≈ 64 per group**. Horizontal and vertical reference lines are added to the plot to highlight this operating point. The curve demonstrates the diminishing returns of increasing sample size once power is already high.

---

### 7. Power Calculation via Single Simulation

**Description:**
This section introduces simulation-based power estimation by generating a single pair of synthetic samples from two groups (Males and Females) with a known population mean difference of 1 unit (means: 18 and 17, $\sigma = 2$, $n = 15$), and performing a two-sample t-test.

```
Males   ~ N(18, 2²),  n = 15
Females ~ N(17, 2²),  n = 15
```

The observed sample difference was 1.34 (slightly above the true value of 1), yet the $p$-value of 0.1163 exceeded $\alpha = 0.05$, leading to a failure to reject $H_0$. This **Type II error** in a single experiment illustrates a crucial point: even when a true effect exists, a single underpowered study may miss it due to random sampling variability.

---

### 8. Power via 10,000 Simulated t-tests (n = 20)

**Description:**
Extending the single-simulation approach, this section runs **10,000 independent two-sample t-tests**, each on freshly simulated data, to estimate **empirical statistical power** at $n = 20$ per group.

Power is estimated as the proportion of simulations that correctly reject $H_0$:

$$\hat{\text{Power}} = \frac{\text{Number of simulations with } p < 0.05}{10{,}000}$$

**Result:** Empirical power ≈ **34.2%** at $n = 20$.

This means approximately **66% of experiments with this design would fail to detect the true effect** — a Type II error rate of 0.66. This result underscores the importance of adequate sample size planning before data collection begins.

---

### 9. Power via 10,000 Simulated t-tests (n = 40)

**Description:**
The previous simulation is repeated with the sample size doubled to $n = 40$ per group. All other parameters remain identical. This direct comparison allows learners to observe how increasing sample size reduces the Type II error rate.

**Result:** Empirical power ≈ **60%** at $n = 40$.

Doubling the sample size from 20 to 40 increased power from 34% to 60%, cutting the Type II error rate from 66% to 40%. While this represents a meaningful improvement, 60% power still falls short of the recommended 80% threshold, reinforcing the lesson that effect size and standard deviation jointly determine the required sample size.

---

### 10. Simulation Power Using Nested Loops

**Description:**
This section scales the simulation approach across **198 sample sizes** (from $n = 3$ to $n = 200$) using nested loops: the outer loop iterates over sample sizes, and the inner loop runs 1,000 simulated t-tests per sample size. This produces a **full empirical power curve** from simulation alone.

```r
for(j in 1:length(NN)){           # Outer loop: 198 sample sizes
  for(i in 1:1000){               # Inner loop: 1000 simulations per n
    A <- rnorm(NN[j], mean=18, sd=2)
    B <- rnorm(NN[j], mean=17, sd=2)
    pval[i] <- t.test(A, B, var.equal=TRUE)$p.value
  }
  Power2[j] <- mean(pval < 0.05)  # Empirical power
}
```

The resulting curve shows the same qualitative shape as the analytical curve — power increases with $n$ and approaches 1 for large samples — and reaches the 80% threshold at approximately $n \approx 64$ per group.

---

### 11. Combined Power Curve – Analytical vs. Simulation

**Description:**
This section overlays the **analytical power curve** (from `power.t.test()`) and the **simulated power curve** (from 1,000 t-tests per sample size) on a single plot, enabling a direct visual comparison between the two approaches.

| Element | Description |
|---|---|
| **Black dots** | Simulated (empirical) power — 1,000 t-tests per $n$ |
| **Blue line** | Analytical (theoretical) power from `power.t.test()` |
| **Horizontal dashed line** | 80% power reference |
| **Vertical dashed line** | Required sample size for 80% power (~64) |

The two curves closely overlap across all sample sizes, confirming that Monte Carlo simulation accurately recovers the theoretical power. Small deviations arise purely from simulation variance (Monte Carlo error), which decreases as the number of simulations per sample size is increased.

---

### 12. Sensitivity Analysis – Power Curve by Effect Size

**Description:**
The final section performs a **sensitivity analysis**: instead of varying sample size, the sample size is held fixed at $n = 20$ per group while the **effect size is varied** from a mean difference of 0.2 up to 4.0 in steps of 0.1. Power is estimated at each effect size via 1,000 simulated t-tests.

```
Baseline mean = 17, Effect sizes = 17.2, 17.3, ..., 21.0
n = 20 per group, sd = 2, alpha = 0.05
```

**Key finding:** With $n = 20$ and $\sigma = 2$, a mean difference of approximately **1.8** (corresponding to group mean ≈ 18.8) is required to achieve 80% power. Smaller effects are underpowered at this sample size; larger effects are detected reliably.

This analysis answers the practical question: *"Given the study I have already designed, what is the smallest effect I can realistically detect?"* — a critical consideration when interpreting non-significant results.

---

## Learning Objectives

By completing this notebook, learners will be able to:

- **Explain the multiple comparisons problem** and quantify how the Family-Wise Error Rate (FWER) inflates with the number of simultaneous tests.
- **Apply and interpret the Bonferroni correction**, understanding both its formula and its graphical representation across different numbers of tests.
- **Perform and interpret regression diagnostics** using Cook's Distance, Studentized residuals, leverage (hat values), and DFFITS to identify influential observations and outliers.
- **Conduct a formal Bonferroni-adjusted outlier test** using `outlierTest()` and manually verify the correction calculation.
- **Distinguish between post-hoc and a priori power analysis**, knowing when each is appropriate and how to perform both using `power.t.test()` in R.
- **Generate and interpret analytical power curves** as a function of sample size, identifying the minimum $n$ required to achieve a target power level.
- **Estimate statistical power through Monte Carlo simulation**, including single experiments, repeated large-scale simulations, and nested loop implementations across many sample sizes.
- **Compare analytical and simulation-based power estimates** and explain sources of discrepancy between them.
- **Conduct a sensitivity analysis** by fixing sample size and computing power as a function of effect size, identifying the minimum detectable effect for a given experimental design.
- **Connect power, sample size, effect size, and significance level** in an integrated framework and make principled sample size decisions for new experiments.

---

## Project Structure

```
Experimental-Design-Using-R/
│
├── Experimental_Design.ipynb        ← Main notebook (all 12 topics)
│
└── README.md
```

### Notebook Sections at a Glance

| # | Topic | Key Function(s) |
|---|---|---|
| 1 | Bonferroni Correction | `plot()`, `abline()` |
| 2 | Influence Index Plot | `influenceIndexPlot()` |
| 3 | Outlier Test | `outlierTest()` |
| 4 | Post-hoc Power | `power.t.test(n=, delta=)` |
| 5 | A Priori Power | `power.t.test(power=, delta=)` |
| 6 | Analytical Power Curve | `power.t.test()` in a loop |
| 7 | Single Simulation | `rnorm()`, `t.test()` |
| 8 | 10,000 Simulations (n=20) | `for` loop, `t.test()` |
| 9 | 10,000 Simulations (n=40) | `for` loop, `t.test()` |
| 10 | Nested Loop Power Curve | Nested `for` loops |
| 11 | Combined Power Curve | `power.t.test()` + nested loops |
| 12 | Sensitivity Analysis | Nested loops over effect sizes |

---

## Requirements

These notebooks use an **R kernel** in Jupyter. The following R packages are required:

| Package | Purpose |
|---|---|
| `car` | `influenceIndexPlot()`, `outlierTest()`, advanced regression diagnostics |
| `carData` | Duncan dataset and other companion datasets |
| Base R | `power.t.test()`, `rnorm()`, `t.test()`, `plot()`, `for` loops |

### Installation

```r
install.packages("car")
install.packages("carData")
```

> All power analysis functions (`power.t.test`) and simulation utilities (`rnorm`, `t.test`, `set.seed`) are included in base R — no additional installation required.

### Jupyter R Kernel Setup

```bash
# In R console:
install.packages("IRkernel")
IRkernel::installspec(user = TRUE)

# Then launch Jupyter:
jupyter notebook
```

---

## How to Run

1. **Clone the repository:**
   ```bash
   git clone https://github.com/your-username/experimental-design-using-r.git
   cd experimental-design-using-r
   ```

2. **Launch Jupyter Notebook:**
   ```bash
   jupyter notebook
   ```

3. **Open** `Experimental_Design.ipynb` in your browser.

4. **Run all cells sequentially** — each section builds on the conceptual foundation of the previous one.

> 💡 **Tip:** The nested loop simulations (Sections 10–12) involve up to 198,000 individual t-tests. On most modern machines this completes in under a minute, but setting `set.seed()` before these cells ensures fully reproducible results.

---

## Conclusion

This notebook builds a rigorous and practical understanding of two indispensable components of experimental design: **error rate control** and **statistical power**.

The journey begins with the Bonferroni correction — a simple but powerful formula that reveals how quickly false positive risk accumulates when many tests are performed simultaneously, and how a straightforward per-test threshold adjustment restores control. From there, the notebook transitions to regression diagnostics, showing that sound experimental analysis requires not just correct model selection, but also vigilance about which individual observations are driving the results.

The heart of the notebook is its treatment of statistical power. Starting from the intuitive `power.t.test()` function, learners progress through post-hoc evaluation (what power did my completed study have?), a priori planning (how many observations do I need?), and full analytical power curves. The simulation sections then reconstruct these same results from first principles — generating synthetic data, running thousands of hypothesis tests, and counting rejections — building a deep, mechanical understanding of what power actually means in practice.

The comparison between analytical and simulation-based power curves (Section 11) is particularly instructive: the near-perfect overlap between the two methods confirms that simulation is a valid and flexible tool for power analysis in settings where no closed-form formula exists. The final sensitivity analysis (Section 12) completes the picture by asking a question every experimentalist must answer: *given my fixed resources, what is the smallest effect I can reliably detect?*

Together, these twelve sections equip learners with a complete toolkit for designing experiments that are both statistically valid and scientifically productive — studies that control false positives, are adequately powered to detect effects of interest, and whose conclusions can be trusted.

---

*Created and maintained by Ms. Mrunalini, Data Science Trainer, Mumbai.*
