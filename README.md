### 📊 Experimental Design: Hypothesis Testing, Power, and Independence


<img width="1024" height="1536" alt="image" src="https://github.com/user-attachments/assets/1126a258-0b7d-4398-8869-e9e9781ccfb4" />


This repository contains comprehensive notes and R-based demonstrations on Experimental Design, focusing on hypothesis testing errors, statistical power, multiple testing corrections, and independent measurements.

### 🔍 Topics Covered
1. Errors in Hypothesis Testing
* Type I Error (False Positive) and significance level (α)
* Type II Error (False Negative) and power (1 − β)
* Family-Wise Error Rate (FWER)
* Multiple testing problems
* Bonferroni correction
* False Discovery Rate (FDR)

2. Neyman–Pearson Framework
* Decision theory in hypothesis testing
* Neyman–Pearson Lemma
* Likelihood Ratio Test (LRT)
* Balancing α and β

3. Statistical Power Analysis
* Concept of power (1 − β)
* Factors affecting power (sample size, effect size, variance, α)
* A priori power analysis
* Post hoc power analysis
* Sensitivity analysis
* Power calculation using power.t.test() in R
* Power estimation through simulation
* Power curves (analytical and simulation-based)

4. Simulation-Based Approaches
* Generating normally distributed samples
* Running multiple t-tests (10,000 simulations)
* Estimating empirical power
* Comparing theoretical vs simulated power curves

5. Independent Measurements & Experimental Units
* Definition of experimental unit
* Replication vs sample unit
* Nested designs
* Practical examples involving animal and biological experiments


### 💻 Tools & Libraries Used
* R Programming Language
* car
* broom
* Base R statistical functions (t.test, power.t.test)
* Simulation techniques for empirical power estimation


### Learning Outcomes
By exploring this repository, you will:
* Understand hypothesis testing errors and their consequences.
* Learn how multiple testing inflates false positive rates.
* Apply correction methods such as Bonferroni and FDR.
* Conduct and interpret power analysis.
* Perform simulation-based power estimation.
* Correctly identify experimental units and independent measurements.
* Design statistically sound experiments
