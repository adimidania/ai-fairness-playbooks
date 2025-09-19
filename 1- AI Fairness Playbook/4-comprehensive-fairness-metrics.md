# Comprehensive Fairness Metrics Tool 

## I. Purpose

This tool connects the **Fairness Definition Selection (FDS)** and **Bias Source Identification (BSI)** components to **quantitative fairness metrics**. It helps your team:

* Select the **right metrics** for your problem type.
* Implement and statistically validate those metrics.
* Interpret disparities **in context** of historical harms and fairness goals.

**What you should produce by the end:**

* A **Fairness Metrics Report** showing selected metrics, values, and confidence intervals.
* Visualizations highlighting disparities by group and intersection.
* A clear **interpretation summary**: which groups are impacted, how large the disparity is, and whether it aligns with FDS goals.

### **How to Navigate This Component**

This guide is detailed and has many sections, but you don’t have to read it linearly.

* **New to this process?** Start at **[Section II: Estimated Time & Effort](#ii-estimated-time--effort)** to get context on scope and effort required.
* **Returning user?** Jump straight to **[Section V: Detection Methodology](#v-detection-methodology)** or **[Section VII: Bias Reporting Template](#vii-bias-reporting-template)** if you are updating, validating, or monitoring work.
* Earlier sections provide context, definitions, and foundational tools, while later sections focus on prioritization, reporting, and validation.

#### **Quick Links**

1. [Introduction](#i-purpose)
2. [Estimated Time & Effort](#ii-estimated-time--effort)
3. [Who Should Be Involved](#iii-who-should-be-involved)
4. [Fairness Metrics](#iv-fairness-metrics)
5. [Metric Selection Framework](#v-metric-selection-framework)
6. [Statistical Validation](#vi-statistical-validation)
7. [Visualization and Reporting Templates](#vii-visualization-and-reporting-templates)
8. [Validation Checklist](#viii-validation-checklist)

## II. Estimated Time & Effort

*(Adjust by model complexity and number of protected groups.)*

* **Small/simple project:** 3–5 hours (1–2 people)
* **Medium project:** 8–13 hours (2–3 people)
* **Large/regulated project:** 2–5 days (cross-functional, with legal review)

**Breakdown for a medium project:**

* Metric selection from FDS mapping → 2 hours
* Coding & implementation → 2–3 hours
* Statistical validation (bootstrap or Bayesian) → 2 hours
* Visualization and reporting → 1–2 hours
* Review and sign-off → 1 hour

## III. Who Should Be Involved

* **Fairness lead / Audit owner** — decides which metrics best align with goals.
* **Data scientist / ML engineer** — implements and validates metrics in code.
* **Product or domain lead** — ensures metrics reflect real-world harm, not just math.
* **Legal / Compliance** — checks regulatory standards (e.g., EEOC, GDPR).
* **Optional:** UX researcher or social scientist to contextualize results.

> **Small team note:** This can be run solo. If so, document assumptions clearly.

## IV. Fairness Metrics

Fairness metrics are quantitative measures used to evaluate the model's fairness. They help identify disparities in outcomes, guide mitigation strategies, and track progress over time. The following is a selection of key fairness metrics commonly used in classification:

### 1. Demographic Parity Rate (Statistical Parity)
**Definition:** Equal positive prediction rate across groups.

**What It Measures:** Percentage of each group receiving positive decisions

**Business Translation:** Are we serving all customer segments equally?

**Executive Interpretation:** If 15% of Group A gets approved but only 8% of Group B, we have a disparity that needs explanation


**Formula:** $$P(\hat{Y}=1 \mid A=0) = P(\hat{Y}=1 \mid A=b)$$
Where Ŷ represents the model's prediction, Ŷ=1 means a positive decision, and A indicates the protected attribute (group membership)

**Statistical Parity Difference (SPD):** $$SPD = \big| P(\hat{Y}=1 \mid A=a) - P(\hat{Y}=1 \mid A=b) \big|$$

**Example:** If 20% of Group A and 10% of Group B receive loan approvals, the SPD = 0.10, signaling a potential fairness issue.

---
### 2. Equal Opportunity (True Positive Rate Parity)
**Definition:** All qualified individuals have an equal chance of receiving a positive outcome, regardless of group.

**What It Measures:** The true positive rate (TPR) across groups.

**Business Translation:** Are we fairly selecting top candidates from every group?

**When To Use:** Hiring, admissions, loans, or any merit-based decision process.

**Executive Interpretation:** If 90% of qualified Group A candidates are selected but only 70% of qualified Group B candidates are, talent is being overlooked.

**Formula:**

$$P(\hat{Y}=1 \mid A=0, Y=1) = P(\hat{Y}=1 \mid A=1, Y=1)$$

Where Y=1 indicates the individual is truly qualified.

**Example:** Among qualified applicants, all groups should be hired at the same rate.

---
### 3. False Positive Rate

**Definition:** The probability that an individual who should not receive a positive decision is incorrectly approved, compared across groups.

**What It Measures:** The chance that an unqualified individual is incorrectly given a positive outcome, measured across groups.

**Business Translation:** Are we approving risky or unqualified candidates more often for certain groups?

**Executive Interpretation:** If 5% of unqualified applicants from Group A are approved but 15% from Group B, we are applying inconsistent standards.

**Formula:**

$$P(\hat{Y}=1 \mid A=0, Y=0) = P(\hat{Y}=1 \mid A=1, Y=0)$$
Where Y=0 means truly unqualified/negative outcome

**Example:** Among unqualified loan applicants, we should incorrectly approve equal percentages from all groups

---
### 4. False Negative Rate

**Definition:** The probability that an individual who should receive a positive decision is incorrectly denied, compared across groups.

**What It Measures:** Errors where qualified individuals are rejected more often in some groups than others.s

**When To Use:** Hiring, lending, opportunity allocation

**Executive Interpretation:** If we incorrectly reject 10% of good candidates from Group A but 25% from Group B, we're discriminating

**Formula:**
$$P(\hat{Y}=0 \mid A=0, Y=1) = P(\hat{Y}=0 \mid A=1, Y=1)$$
Where Ŷ=0 means negative decision, Y=1 means truly qualified

**Example:** Among qualified candidates, we should incorrectly reject equal percentages from all groups

---
### 5. Equalized Odds

**Definition:** Predictions are fair if both the true positive rate and false positive rate are equal across groups.

**What It Measures:** Ensures that qualified individuals are selected at the same rate (TPR parity) **and** unqualified individuals are rejected at the same rate (FPR parity) across groups.

**Business Translation:** Are we applying the same standards to all groups for both approvals and rejections?

**Executive Interpretation:** If 90% of qualified Group A candidates are hired and 70% of unqualified are incorrectly approved, while Group B has 80% TPR and 50% FPR, the system is biased and needs intervention.

**Formula:**

$$
P(\hat{Y}=1 \mid A=0, Y=y) = P(\hat{Y}=1 \mid A=1, Y=y), \quad y \in \{0,1\}
$$

Where Y indicates actual outcome, Ŷ is predicted, and A is group membership.

**Example:** Among all applicants, both qualified and unqualified, the chance of positive predictions should match across demographic groups.

---
### 6. Calibration Score

**Definition:** The degree to which predicted probabilities match actual outcomes across groups.

**What It Measures:** How well the model’s predicted risk or success probabilities reflect reality for each group.

**Business Translation:** Do our scores “mean the same thing” for everyone, or are some groups systematically over- or under-estimated?

**Executive Interpretation:** If the model predicts an 80% chance of success, it should actually succeed 80% of the time for all demographic groups.

**Formula:**

$P(Y=1 \mid \hat{Y}=p, A) \text{ should be equal across groups for all probability levels } p$

Where Ŷ = predicted probability, Y = actual outcome, A = group membership

**Example:** A 60% credit risk score should correspond to a 60% likelihood of default for all groups, ensuring fair risk assessment.

## IV. Metric Selection Framework

Fairness metrics help translate abstract fairness goals into measurable outcomes. Choosing the right metric depends on the **problem type** and the **definition of fairness that matters most**. This decision tree guides your team to select the most appropriate metrics for classification, regression, and ranking tasks.

### Step 1: Determine Problem Type

* **Classification** → Go to Step 2
* **Regression** → Go to Step 5
* **Ranking** → Go to Step 8

### Classification: Primary Fairness Definition

**Step 2: Which fairness goal is most relevant?**

* **Demographic Parity / Statistical Parity** → Select:

  * Demographic Parity Difference (DPD)
  * Positive Prediction Rate per group

* **Equal Opportunity (TPR Parity)** → Select:

  * True Positive Rate (TPR) Difference
  * False Negative Rate (FNR) Difference

* **Equalized Odds (TPR + FPR Parity)** → Select:

  * True Positive Rate (TPR) Difference
  * False Positive Rate (FPR) Difference

* **Calibration / Probabilistic Fairness** → Select:

  * Calibration Slope / Reliability Curve
  * Predicted Probability vs. Actual Outcome per group

**Step 3: Are there concerns about false positives vs. false negatives?**

* False positives more harmful → Focus on FPR Difference metrics
* False negatives more harmful → Focus on FNR Difference metrics
* Both harmful → Track both FPR and FNR differences

**Step 4: Are intersectional or individual fairness considerations needed?**

* Yes → Add: Individual Consistency / Similar-Item Ranking / Input-Output Sensitivity
* No → Continue with group-level metrics

### Regression: Primary Fairness Definition

**Step 5: Which fairness goal is most relevant?**

* **Statistical Parity** → Select: Group Outcome Difference, Distribution Comparison metrics
* **Bounded Group Loss** → Select: Maximum Group Loss, Group Error Ratio
* **Individual Fairness** → Select: Individual Consistency Measure, Input-Output Sensitivity

**Step 6: Error Direction Importance**

* Overprediction more harmful → Add Positive Residual Difference
* Underprediction more harmful → Add Negative Residual Difference
* Both harmful → Add Absolute Residual Difference

**Step 7: Are uncertainty estimates provided?**

* Yes → Add Prediction Interval Coverage metrics
* No → No additional metrics

### Ranking: Primary Fairness Definition

**Step 8: Which fairness goal is most relevant?**

* **Exposure Parity** → Select: Exposure Ratio, Normalized Discounted Cumulative Gain (NDCG) Difference
* **Representation Parity** → Select: Group Representation Ratio, Top-k Proportion Difference
* **Individual Fairness** → Select: Rank-Consistency Score, Similar-Item Rank Distance

Use the template below to document your selections:

* **Your Problem Type:** \[Classification/Regression/Ranking]
* **Selected Fairness Definitions:** \[List]
* **Chosen Metrics:** \[List]

### Example

* **Loan approval classifier**

  * FDS output → Equal Opportunity (focus on false negatives for minority applicants)
  * Metric → FNR Difference (between protected group and baseline)
  * Computed result → FNR = 0.28 (group A) vs. 0.10 (group B) → Difference = **0.18** (critical)

## V. Statistical Validation

When you calculate a fairness metric on your dataset, it’s just one estimate. What if your sample is small, or random chance makes Group B look worse than it really is? Statistical validation checks if your metric is reliable, not just a fluke.

### Bootstrap Confidence Intervals (>100 samples)

Think of bootstrapping as “stress-testing” your metric:

1. **Resample with replacement:** Randomly pick rows from your dataset to make a new dataset of the same size. You can pick the same row more than once.
2. **Compute the metric:** Calculate your fairness metric on this new dataset.
3. **Repeat many times:** Do this thousands of times (e.g., 10,000) to see how the metric changes.
4. **Look at the results:** Check all the metric values from your resamples.
5. **Find the confidence interval:** Take the middle 95% of values — this gives a range where the true fairness metric likely falls.

**Why it helps:**

* If the interval is **narrow**, your metric is trustworthy.
* If it’s **wide**, the disparity might just be random fluctuation.

### Bayesian Estimation for Small Groups (<100 samples)

If some subgroups have very few samples, bootstrap alone isn’t enough. Bayesian estimation adds “guardrails” by starting with a mild assumption (a **prior**) that group rates shouldn’t be wildly different unless data strongly says so.

1. **Set a weak prior** – pick a starting assumption about the rate.
   *Example:* Assume the hiring rate is around 50%. This is a mild assumption that prevents extreme values from tiny data.

2. **Combine the prior with the actual data** – update your estimate using the subgroup’s observed results.
   *Example:* If 2 out of 4 candidates were hired, the prior prevents the rate from looking like 50% → 100% or 0%.

3. **Compute the posterior distribution** – this gives all plausible values of the metric given prior + data.

4. **Take the middle 95%** of the posterior – this is your **credible interval**, showing the range where the true value likely lies.
   *Example:* The 95% credible interval might be 30%–80%, meaning there’s a 95% chance the true hiring rate is within that range.

**Why it helps:**

* Prevents overreacting to tiny sample quirks.
* The credible interval says, “Given your data and assumptions, there’s a 95% chance the true disparity is inside this range.”


## VI. Visualization and Reporting Templates

Good charts make fairness results **immediately clear to both engineers and non-technical reviewers**. Show both **the disparity** and **how confident you are in it**.

### Fairness Disparity Chart

* **What it is:** A bar chart showing metric values by group, with error bars for uncertainty.
* **What it shows:**

  * Which groups are doing worse or better.
  * Whether gaps are large enough to be real (not just noise).

Here's practical Python/Matplotlib example you can leverage:

```python
import matplotlib.pyplot as plt
import numpy as np

# Example data
groups = ['Group A', 'Group B', 'Group C']
metric_values = [0.28, 0.10, 0.15]          # e.g., False Negative Rate
lower_ci = [0.24, 0.08, 0.12]               # 95% confidence interval lower bound
upper_ci = [0.33, 0.13, 0.18]               # 95% confidence interval upper bound

# Compute error bars (upper - metric, metric - lower)
error_bars = [metric_values - np.array(lower_ci), np.array(upper_ci) - metric_values]

# Plot
plt.figure(figsize=(6,4))
plt.bar(groups, metric_values, yerr=error_bars, capsize=5, color=['skyblue','orange','green'])
plt.ylabel('False Negative Rate')
plt.title('Fairness Disparity Chart by Group')
plt.axhline(y=0.2, color='red', linestyle='--', label='Policy Threshold')
plt.legend()
plt.show()
```
> **Pro tip:** Use color to highlight any disparity that crosses a policy or regulatory threshold.

### Intersectional Heatmap

* **What it is:** A grid showing disparities for combinations of attributes (e.g., gender × age).
* **What it shows:**

  * Whether bias appears only for single attributes or at intersections.
  * Where sample sizes are small (use lighter color or transparency to signal this).

Here’s a  Python example using **Seaborn** to create an intersectional heatmap showing metric disparities for combinations of two attributes (e.g., gender × age group), including sample size annotation:

```python
import seaborn as sns
import matplotlib.pyplot as plt
import pandas as pd
import numpy as np

# Example data: intersection of Gender x Age Group
data = pd.DataFrame({
    'Gender': ['Male', 'Male', 'Male', 'Female', 'Female', 'Female'],
    'AgeGroup': ['18-25', '26-35', '36-50', '18-25', '26-35', '36-50'],
    'FNR': [0.12, 0.18, 0.15, 0.25, 0.22, 0.20],      # e.g., False Negative Rate
    'SampleSize': [50, 80, 60, 40, 70, 55]             # to signal confidence
})

# Pivot table for heatmap
heatmap_data = data.pivot("Gender", "AgeGroup", "FNR")
sample_data = data.pivot("Gender", "AgeGroup", "SampleSize")

# Set transparency based on sample size (smaller sample → lighter color)
alpha = sample_data / sample_data.max().max()

plt.figure(figsize=(8,5))
ax = sns.heatmap(heatmap_data, annot=sample_data, fmt="d", cmap="coolwarm", alpha=alpha, cbar_kws={'label': 'FNR'})
plt.title("Intersectional Fairness Heatmap (FNR)")
plt.ylabel("Gender")
plt.xlabel("Age Group")
plt.show()
```
> **Pro tip:** Annotate each cell with sample size or interval width — so nobody mistakes noise for a real issue.

## VIII. Validation Checklist

* [ ] **Have we linked fairness definitions to metric choices?**
* [ ] **Were metrics computed on the full validation/test set?**
* [ ] **Do we report confidence/credible intervals for every metric?**
* [ ] **Do charts clearly show disparities by group and intersection?**
* [ ] **Do we explain who is harmed and how?**
* [ ] **Does any critical gap (>0.1 FNR, >20% DP) have a mitigation owner?**
* [ ] **Are metrics wired into ongoing monitoring?**
* [ ] **Is the Fairness Metrics Report ready for handoff?**

**Pass rule:** At least 80% complete. Any **critical disparity (>0.1 FNR gap, >20% DP gap, etc.)** must have a mitigation plan or owner assigned.
