# Case Study: Applying the Fairness Intervention Playbook to Loan Approval System

## I. Introduction

In this case study, we explore how the **Fairness Intervention Playbook** can be practically applied to address fairness issues in real-world AI systems. The selected use case is a **loan approval algorithm** used by a commercial bank to predict the likelihood of loan default and decide approvals.

Recent internal audits revealed a **stark gender disparity** in approval rates:

* **Men:** 76% approval rate
* **Women:** 58% approval rate

This gap persists even among applicants with **equivalent qualifications**, suggesting potential bias in the model’s decision-making process.

The bank’s leadership set a clear fairness goal:

> “Ensure **equal opportunity**, where applicants with similar qualifications have equal approval chances, regardless of gender.”

To meet this goal, the data science team applied the **Fairness Intervention Playbook**—a structured, four-component framework designed to guide fairness interventions through a combination of data, modeling, and evaluation techniques.

This case study documents their journey, detailing how each component of the playbook was used to uncover, understand, and mitigate gender bias in the loan approval system. It provides a **step-by-step, practical walkthrough** serving as a **template for teams facing similar challenges** in other AI systems.

### Quick Links
1. [Introduction](#i-introduction)
2. [Casual Fairness Analysis](#ii-casual-fairness-analysis)
3. [Pre-Processing Interventions](#iii-pre-processing-interventions)
4. [Pre-Processing Interventions](#iv-in-processing-interventions)
5. [Post-Processing Interventions](#v-post-processing-interventions)
6. [Conclusion](#vi-conclusion)

## II. Causal Fairness Analysis

The **Causal Fairness Toolkit** was applied to the bank’s **loan approval algorithm**, which predicts default probability using applicant data. Internal fairness audits revealed that men had a **76% loan approval rate**, while equally qualified women had only **58%**, raising concerns about potential gender bias.

The goal of this component was to uncover **why** this disparity occurs, not just whether it exists, and to pinpoint **causal pathways** where gender influences outcomes, directly or indirectly.

The team created a **Causal Fairness Report (4 pages)** including:

1. A variable map (protected, proxy, mediator, legitimate predictor, outcome).
2. A causal diagram summarizing observed dependencies.
3. Counterfactual test results using synthetic applicant pairs.
4. A classification of fair vs. unfair pathways.
5. Clear recommendations for intervention points and follow-up actions.

### I. Causal Modeling Template

#### **Step 1 — Variable Identification & Mapping**

Below is the team’s mapping of key variables in the loan dataset:

```markdown
* **Protected attributes**
  *Variables:* [Gender]
  *Justification:* Gender should not directly affect loan approval. The fairness concern arises because women appear to have systematically lower approval rates despite equal creditworthiness.

* **Mediators**
  *Variables:* [Income, Employment gaps, Part-time status]
  *Justification:* These factors are causally influenced by gender (e.g., wage disparities, caregiving interruptions) and impact the model’s predicted default risk.

* **Proxies**
  *Variables:* [Industry sector, Job title, Work tenure]
  *Justification:* These variables correlate with gender due to occupational segregation patterns (e.g., healthcare vs. engineering).

* **Outcomes**
  *Variables:* [Loan approval (Yes/No)]
  *Justification:* The system’s final decision variable, which should depend on legitimate financial risk indicators — not demographic characteristics.

* **Legitimate predictors**
  *Variables:* [Debt-to-income ratio, Credit score, Existing debt, Savings balance]
  *Justification:* These are fair indicators of repayment ability and should ethically influence outcomes.
```

#### **Step 2 — Identifying Discrimination Mechanisms**

**1. Direct Discrimination**

* **Causal mechanism:** Gender was *not* explicitly used as a feature in the model. However, some data preprocessing steps (like encoding of employment type) indirectly revealed gender information.
* **Evidence strength:** Low–Medium (no direct use, but correlation found in feature embeddings).
* **Ethical assessment:** Problematic if embeddings encode gender cues.
* **Intervention priority:** Medium — to be validated during model auditing.

**2. Indirect Discrimination (via Mediators)**

* **Causal mechanism:** Gender → Income → Loan approval.
  Women’s lower reported income led to higher predicted default probability.
* **Evidence strength:** High (synthetic regression showed income accounts for ~40% of approval disparity).
* **Ethical assessment:** Unfair, as income differences reflect systemic wage gaps, not creditworthiness.
* **Intervention priority:** High.

#### **3. Proxy Discrimination**

* **Causal mechanism:** Gender → Industry sector → Loan approval.
  Certain female-dominated industries (e.g., healthcare, education) had slightly higher model-predicted risk.
* **Evidence strength:** Medium (correlation r = 0.42 between gender and industry risk factor).
* **Ethical assessment:** Unfair; sector-based penalties indirectly disadvantage women.
* **Intervention priority:** Medium–High.

#### **Step 3 — Causal Graph Construction**

The team’s synthetic causal map (simplified):

```
Gender → Income → Loan Approval
Gender → Employment Gaps → Loan Approval
Gender → Industry Sector → Loan Approval
Credit Score → Loan Approval
Debt-to-Income Ratio → Loan Approval
```

*Dashed edges* (not shown here) represented uncertain links, such as whether industry affects credit score.
The team annotated assumptions and assigned confidence levels (e.g., “High confidence: Gender → Income”).

### II. Counterfactual Analysis

#### **Step 1 — Counterfactual Queries**

The team generated synthetic applicant pairs differing only in gender.

```markdown
- **Case ID:** 0021  
- **Base case:**  
  - Gender: Female  
  - Credit Score: 720  
  - Income: 52,000  
  - Employment Gaps: 1 year  
  - Model Outcome: Denied  

- **Counterfactual scenario:**  
  - Gender: Male  
  - Credit Score, Income, Employment Gaps held constant  
  - Model Outcome: Approved  

- **Expected fair outcome:** No change in approval decision.  
- **Actual model outcome:** Changed from Denied → Approved.  
- **Notes:** Indicates potential gender-based influence through correlated features.
```

Out of 100 matched synthetic pairs, **23 showed different outcomes solely due to gender change**, signaling causal unfairness.

#### **Step 2 — Pathway Classification**

| Pathway                                  | Type       | Fair/Unfair | Evidence                           | Intervention                                        |
| ---------------------------------------- | ---------- | ----------- | ---------------------------------- | --------------------------------------------------- |
| Gender → Income → Loan approval          | Mediator   | **Unfair**  | High (systemic wage gap reflected) | Adjust thresholds or fairness constraints           |
| Gender → Employment gap → Loan approval  | Mediator   | **Unfair**  | Medium                             | Add fairness-aware regularization or reweighting    |
| Gender → Industry sector → Loan approval | Proxy      | **Unfair**  | Medium                             | Rebalance dataset or cluster industries differently |
| Credit score → Loan approval             | Legitimate | **Fair**    | High                               | Keep                                                |
| Debt-to-income ratio → Loan approval     | Legitimate | **Fair**    | High                               | Keep                                                |

## III. Pre-Processing Interventions

fter uncovering key unfair pathways in the causal analysis, the team moved to the **Pre-Processing Toolkit** to address these biases **before model training**. The goal of this stage was to reduce the influence of gender-related variables (income, employment gaps, industry sector) while preserving important predictors of creditworthiness.

This phase focused on creating a **more balanced training dataset** and establishing a **fairer data foundation**, ensuring the model’s decisions would not simply replicate existing societal inequalities.

The team produced a **Fair Data Package**, including:

1. A bias profile report with subgroup distribution metrics.
2. Synthetic data reweighting tables.
3. Normalization and sampling strategies for gender parity.
4. Feature-level fairness impact report (documenting trade-offs).

### I. Pre-Processing Steps

#### **Step 1 — Data Profiling and Bias Detection**

Using the output of the causal analysis, the team examined the training dataset’s gender representation.

```markdown
Total applicants: 20,000
→ Male: 12,000 (60%)
→ Female: 8,000 (40%)

Approval rate gap: 18 percentage points  
Average income gap: $9,500  
Employment gap occurrence: 24% women vs. 6% men  
Industry distribution skew: Healthcare (70% women), Engineering (82% men)
```

A **bias report** was generated showing how correlated each feature was with gender.

| Feature              | Correlation with Gender | Fairness Impact | Action                         |
| -------------------- | ----------------------- | --------------- | ------------------------------ |
| Income               | 0.63                    | High            | Apply reweighting              |
| Employment gaps      | 0.55                    | High            | Consider feature normalization |
| Industry sector      | 0.42                    | Medium          | Cluster re-encoding            |
| Credit score         | 0.12                    | Low             | Retain as-is                   |
| Debt-to-income ratio | 0.07                    | Low             | Retain as-is                   |

#### **Step 2 — Bias Mitigation Techniques Applied**

**a. Reweighting**

The team applied synthetic reweighting to equalize subgroup influence.
Each female applicant was assigned a weight factor:

[
w_i = \frac{P(\text{gender})}{P(\text{gender}|\text{income, sector})}
]

Result: The weighted dataset produced balanced approval probabilities (gap reduced from 18% → 6%).

**b. Resampling**

To further address under-representation, stratified resampling was performed to increase the proportion of women in high-income categories.
→ Added 1,500 synthetic samples generated using SMOTE-like augmentation while preserving credit score and debt ratios.

**c. Feature Normalization**

Employment gap durations were standardized within gender groups to remove residual gender signals:

[
\text{NormGap} = \frac{\text{GapMonths} - \mu_{\text{gender}}}{\sigma_{\text{gender}}}
]


#### **Step 3 — Fairness Validation (Pre-Model Check)**

The fairness metrics were recalculated after pre-processing:

| Metric                             | Before | After Pre-Processing |
| ---------------------------------- | ------ | -------------------- |
| Approval rate gap                  | 18%    | 6%                   |
| Income mean difference             | $9,500 | $2,100               |
| Gender–Industry correlation        | 0.42   | 0.19                 |
| Dataset fairness index (0–1 scale) | 0.61   | 0.84                 |

Results showed a clear improvement in group balance and feature independence from gender.

To ensure fairness extended across **intersectional subgroups**, the team conducted additional validation on combinations of gender and race within industries. For example, in the engineering sector:

| Subgroup    | Approval Rate (Before) | Approval Rate (After) |
| ----------- | ---------------------- | --------------------- |
| White men   | 78%                    | 74%                   |
| Black men   | 64%                    | 70%                   |
| White women | 55%                    | 68%                   |
| Black women | 48%                    | 66%                   |

After pre-processing, approval gaps narrowed significantly across intersecting identities — particularly between **Black men and white men** (gap reduced from 14% → 4%), and between **Black women and white women** (gap reduced from 7% → 2%).

These results confirmed that the pre-processing interventions improved not only gender parity but also **intersectional fairness**, creating a stronger foundation for the next modeling phase.

> ⚙️: This process is **iterative** — after retraining and evaluating the new model, the team may need to **revisit these pre-processing decisions**. If performance drops or residual bias reappears, thresholds, sampling ratios, or normalization strategies should be refined.

## IV. In-Processing Interventions
After creating a balanced dataset through pre-processing, the team proceeded to the **In-Processing Toolkit** phase to embed fairness directly into the model’s training process.
This stage focuses on ensuring that the model **learns to make fair predictions**, not just from balanced data, but as part of its optimization objectives.

The goal was to **reduce the approval gap between men and women** while maintaining predictive accuracy within acceptable limits.

### I. Model Overview

The loan approval system used a **binary classification model** predicting the likelihood of loan default.
Initial architecture and evaluation were as follows:

| Model Type                    | Metric   | Baseline Value |
| ----------------------------- | -------- | -------------- |
| Gradient Boosting Classifier  | Accuracy | 0.87           |
| F1-score                      | 0.84     |                |
| AUC                           | 0.89     |                |
| Demographic Parity Difference | 0.18     |                |
| Equal Opportunity Difference  | 0.21     |                |

Although performance was strong, fairness metrics indicated significant bias against female applicants.

### II. Fairness Method Selection

After evaluating compatibility and trade-offs, the team considered three viable options:

1. **Fairness Regularization** — compatible with tree-based ensemble models through custom loss modification.
2. **Adversarial Debiasing** — could be applied if migrating to a neural network architecture.
3. **Multi-Objective Optimization** — allowed balancing fairness and accuracy through adjustable weights.

Given the team’s resources and the model type, they selected **Fairness Regularization** as the main intervention method.

### III. Implementation: Fairness Regularization

A fairness penalty was added to the loss function to minimize the **difference in positive prediction rates** (Demographic Parity) and **true positive rates** (Equal Opportunity) between genders.

The modified loss function was defined as:

[
L_{\text{total}} = L_{\text{task}} + \lambda_{dp} \cdot | P(\hat{Y}=1|A=0) - P(\hat{Y}=1|A=1) | + \lambda_{eo} \cdot | TPR_{A=0} - TPR_{A=1} |
]

Where:

* ( A = 0 ) → male applicants
* ( A = 1 ) → female applicants
* ( \lambda_{dp} = 0.4 ), ( \lambda_{eo} = 0.6 )

Training was run over 50 epochs with early stopping based on validation AUC.

### IV. Results

After applying the fairness-regularized loss, the model produced the following outcomes:

| Metric                        | Before | After Regularization | Change |
| ----------------------------- | ------ | -------------------- | ------ |
| Accuracy                      | 0.87   | 0.84                 | -3%    |
| F1-score                      | 0.84   | 0.82                 | -2%    |
| AUC                           | 0.89   | 0.86                 | -3%    |
| Demographic Parity Difference | 0.18   | 0.06                 | ↓ 67%  |
| Equal Opportunity Difference  | 0.21   | 0.08                 | ↓ 62%  |

Fairness improved substantially, while the accuracy drop remained within the **≤5% threshold**, meeting the success criteria.

### VI. Validation and Robustness Checks

#### Subgroup Analysis

| Subgroup | Approval Rate | Default Rate | F1-score |
| -------- | ------------- | ------------ | -------- |
| Male     | 74%           | 9%           | 0.83     |
| Female   | 70%           | 8%           | 0.81     |

Approval rates were now much closer, indicating fairness improvement without harming risk accuracy.

#### Intersectional Evaluation

When combining gender with **race**, the model maintained parity across groups:

| Group              | Approval Gap (Before) | Approval Gap (After) |
| ------------------ | --------------------- | -------------------- |
| Black (M vs F) | 0.14                  | 0.05                 |
| White (M vs F) | 0.20                  | 0.07                 |

#### Hyperparameter Sensitivity

Changing (\lambda_{dp}) and (\lambda_{eo}) within ±0.2 affected fairness metrics by less than 5%, showing stability.

### VII. Overall Results
* **Fairness–accuracy trade-off:** Fairness improved dramatically with minimal loss in performance.
* **Best-performing method:** Regularization offered a stable, flexible balance between model complexity and fairness.
* **Stakeholder takeaway:** A fairness-aware model can achieve both compliance and trustworthiness without compromising credit risk prediction reliability.

> ⚙️: This step is **iterative** — after deployment or additional monitoring, the team may revisit fairness parameters (λ values) or test other in-processing methods like adversarial debiasing to strengthen results under new data distributions.

## V. Post-Processing Interventions

After retraining the fairness-regularized model, the team moved to the **Post-Processing Toolkit** to address **residual disparities** observed in the prediction outcomes.
Even though demographic and opportunity gaps were substantially reduced, the **final approval distribution** still showed a **4–5% difference** between male and female applicants.

The post-processing step ensured that **decisions made after model training** aligned with the organization’s fairness goal — *equal opportunity in loan approval* — while maintaining predictive consistency.

The key objective was to **align decision thresholds and adjust outcomes** in borderline cases to improve fairness metrics without altering the underlying model.

> “If two applicants have similar repayment likelihoods, their approval shouldn’t differ because of demographic signals.”

### I. Selected Methods

The team focused on two **practically applicable post-processing techniques**:

1. **Threshold Optimization (Group-Specific Thresholds)**
   Adjusting approval cutoffs per group to achieve equalized opportunity.

2. **Rejection Option Reclassification**
   Re-evaluating borderline predictions (where confidence is low) to favor the disadvantaged group — here, female applicants — in ambiguous cases.

### II. Step 1 — Threshold Optimization

After fairness regularization, predicted probabilities were analyzed across gender groups.

| Group  | Avg. Predicted Probability of Approval | Default Rate | Initial Threshold |
| ------ | -------------------------------------- | ------------ | ----------------- |
| Male   | 0.67                                   | 9%           | 0.50              |
| Female | 0.64                                   | 8%           | 0.50              |

Using the **Equal Opportunity Optimization method**, thresholds were adjusted to equalize **true positive rates (TPR)** across genders:

| Group  | Optimal Threshold | Resulting TPR | False Positive Rate |
| ------ | ----------------- | ------------- | ------------------- |
| Male   | 0.52              | 0.88          | 0.12                |
| Female | 0.47              | 0.87          | 0.13                |

The optimized thresholds yielded **nearly identical TPRs** for both genders, closing the opportunity gap from **8% → 1%**.

### III. Step 2 — Rejection Option Reclassification

For applicants near the decision boundary (prediction scores between 0.45–0.55), a **rejection-option strategy** was applied:

* Applicants with uncertain predictions were **re-evaluated**.
* When scores fell within the gray zone, and if the applicant belonged to a historically disadvantaged group (women or Black women), the system favored the positive (approval) outcome, assuming other risk indicators were acceptable.

| Range (Score) | Rule Applied                              | Outcome Adjustment |
| ------------- | ----------------------------------------- | ------------------ |
| 0.55+         | Approve (no change)                       | —                  |
| 0.45–0.55     | Approve if Female & no default indicators | +72 adjusted cases |
| < 0.45        | Deny (no change)                          | —                  |

This adjustment affected **3.6% of the total applications**, mostly borderline female applicants, improving fairness while keeping portfolio risk stable.

### IV. Validation After Post-Processing

| Metric                        | After In-Processing | After Post-Processing | Change |
| ----------------------------- | ------------------- | --------------------- | ------ |
| Demographic Parity Difference | 0.06                | **0.02**              | ↓ 67%  |
| Equal Opportunity Difference  | 0.08                | **0.01**              | ↓ 87%  |
| Accuracy                      | 0.84                | 0.83                  | -1%    |
| Portfolio Default Rate        | 8.5%                | 8.7%                  | +0.2%  |

Fairness improvements reached **near parity** across gender while accuracy and default risk remained virtually unchanged.

### V. Intersectional Evaluation

The team also validated fairness across intersectional groups (Gender × Race), confirming that parity was achieved without introducing reverse bias.

| Group       | Approval Rate (Before) | After Post-Processing | Change |
| ----------- | ---------------------- | --------------------- | ------ |
| White Men   | 74%                    | 73%                   | -1%    |
| Black Men   | 70%                    | 72%                   | +2%    |
| White Women | 68%                    | 71%                   | +3%    |
| Black Women | 66%                    | 70%                   | +4%    |

All intersectional subgroups improved, with **Black women** showing the largest relative gain — reflecting successful prioritization of fairness where it was most needed.

### VI. Summary of Post-Processing Outcomes

* Residual approval gap eliminated (now within ±2%).
* Equal opportunity achieved with negligible performance trade-off.
* Rejection-option method added transparency and ethical reasoning for borderline cases.

💡 **Team Note:**
This phase confirmed that **post-processing fairness tuning** can deliver strong equity outcomes **without model retraining**, making it a low-cost, high-impact intervention for regulated systems like banking.

> ⚙️: This step remains **iterative** — thresholds and fairness rules must be **monitored periodically** as new data distributions emerge. If approval or default patterns shift, recalibration or retraining might be required.

## VI. Conclusion

The loan approval case study demonstrated how the **Fairness Intervention Playbook** can be applied end-to-end to uncover, understand, and mitigate bias in a complex financial system.

Starting from a **causal diagnosis**, the team traced fairness issues to *income disparities, employment gaps,* and *industry sector biases*—pathways that unfairly linked gender to loan outcomes.
Each subsequent phase addressed these findings methodically:

* **Pre-Processing** rebalanced the dataset and neutralized gender-correlated features, improving the dataset fairness index from *0.61 → 0.84*.
* **In-Processing** embedded fairness constraints directly into the model’s loss, reducing the approval gap from *18% → 6%* with only a *3%* drop in accuracy.
* **Post-Processing** fine-tuned decision thresholds and applied a rejection-option rule, reaching **statistical parity** (gap within ±2%) and **equal opportunity** (TPR difference ≤1%).

Intersectional analysis confirmed that fairness gains generalized across subgroups—especially benefiting **Black women**, the most disadvantaged group in the baseline.
Importantly, predictive performance remained robust, with negligible change in portfolio risk.

> 💡 *Key takeaway:* Fairness intervention is most effective as an **iterative, layered process**.
> No single method eliminates bias, but a structured sequence—from causal understanding to post-decision calibration—builds trustworthy, equitable AI systems.

This case study illustrates that fairness in high-stakes domains like lending is **achievable without compromising accuracy** when interventions are guided by **causal reasoning, evidence-based tuning, and continuous validation**.
