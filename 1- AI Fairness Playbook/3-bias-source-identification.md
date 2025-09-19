# **Bias Source Identification Tool (BSI)**

## I. **Purpose**

This step helps your team **find where bias enters the system** — whether in data, features, model design, evaluation, or deployment — and decide which issues to fix first. You’ll turn **HCA (Historical Context Analysis)** insights and **FDS (Fairness Definition Selection)** decisions into:

* **Detection tests** to uncover bias
* **Evidence tables** that clearly document findings
* **Priority scores** to guide remediation

**Outputs you should produce:**

* **Bias Sources Report** listing bias types, evidence, and priorities

### **How to Navigate This Component**

This guide is detailed and has many sections, but you don’t have to read it linearly.

* **New to this process?** Start at **[Section II: Estimated Time & Effort](#ii-estimated-time--effort)** to get context on scope and effort required.
* **Returning user?** Jump straight to **[Section V: Detection Methodology](#v-detection-methodology)** or **[Section VII: Bias Reporting Template](#vii-bias-reporting-template)** if you are updating, validating, or monitoring work.
* Earlier sections provide context, definitions, and foundational tools, while later sections focus on prioritization, reporting, and validation.

#### **Quick Links**

1. [Introduction](#i-purpose)
2. [Estimated Time & Effort](#ii-estimated-time--effort)
3. [Who Should Be Involved](#iii-who-should-be-involved)
4. [Bias Type Taxonomy](#iv-bias-type-taxonomy)
5. [Detection Methodology](#v-detection-methodology)
6. [Prioritization Framework](#vi-prioritization-framework)
7. [Bias Reporting Template](#vii-bias-reporting-template)
8. [Validation Checklist](#viii-validation-checklist)


## II. **Estimated Time & Effort**

*(Adjust for project size and complexity.)*

* **Small/simple project:** 5–7 hours total (2 people)
* **Medium project:** 8–14 hours total (3–4 people, 1–2 days)
* **Large/regulated project:** 3–5 days (cross-functional + external expert input)

**Typical breakdown (medium project):**

* Review HCA & FDS outputs → 1–2 hours
* Understand bias type sources → 1–2 hours
* Run detection procedures → 2–4 hours
* Prioritize & score findings → 1–2 hours
* Document & plan remediation → 1–2 hours

## III. **Who should be involved**

* **Fairness lead / Audit owner** — coordinates the investigation and owns the report.
* **Data scientist / ML engineer** — runs audits and explains technical findings.
* **Product manager / Domain lead** — validates operational relevance.
* **Legal / Compliance** — flags regulatory constraints.
* **Optional:** UX researcher, social scientist, Ops for deployment checks.

> **Small team note:** You can run this solo or in a small group. Just document **assumptions and gaps clearly** if roles overlap.

## IV. **Bias Type Taxonomy**

Bias can enter AI systems at multiple stages. Understanding these sources helps us know where to look for potential problems and how to prevent or mitigate them.

### 1. Historical Bias

**What It Is:** Bias resulting from pre-existing social inequities, regardless of sampling or feature selection.

**Example:** Historical hiring data shows fewer women in leadership because of past discrimination.

**Why It's Critical:** Reproduces societal inequities in your decisions.

**Business Impact:** Creates legal liability and excludes qualified candidates.

**Detection Method:** Compare historical data patterns to known demographic distributions.

**Executive Action Required:** Audit all historical training data for known discrimination patterns.

### 2. Representation Bias

**What It Is:** Bias arising from how populations are sampled and measured in training data.

**Example:** A medical diagnostic system trained primarily on data from young adult males may perform poorly for elderly female patients.

**Why It's Critical:** AI cannot generalize to groups it has rarely or never seen.

**Business Impact:** Excludes potential talent or user segments and degrades user experience.

**Detection Method:** Audit dataset demographics vs. target population; identify “data deserts.”

**Executive Action Required:** Explore data augmentation strategies to ensure adequate representation for all relevant groups.

### 3. Measurement Bias

**What It Is:** Bias arising from how attributes are measured, proxied, or operationalized.

**Example:** Using standardized test scores as a proxy for aptitude may disadvantage groups with less access to test preparation resources.

**Why It Matters:** Systematic errors appear objective but disadvantage specific groups.

**Business Impact:** Discriminatory outcomes even if the AI appears neutral.

**Detection Method:** Analyze feature meaning across groups; perform correlation and validity tests.

**Executive Action Required:** Standardize measurement processes and adjust proxies to reflect true performance.

### 4. Deployment Bias

**What It Is:** Bias arising from how systems are implemented and used in practice.

**Example:** Recruiters treat AI recommendations as final decisions instead of guidance.

**Why It Matters:** Amplifies bias and creates outcomes not considered during model design.

**Business Impact:** Legal exposure, unintentional discrimination, and reduced system trust.

**Detection Method:** Monitor actual usage vs. intended workflow; audit post-deployment outcomes.

**Executive Action Required:** Implement clear usage guidelines, ongoing audits, and user training.

### 5. Aggregation Bias

**What It Is:** One-size-fits-all models applied to heterogeneous populations.

**Example:** Using the same medical diagnostic AI for all ethnicities when disease presentation varies.

**Why It Matters:** Favors majority patterns, reducing accuracy for minority groups.

**Business Impact:** Subpar service or selection outcomes for underrepresented groups.

**Detection Method:** Evaluate model performance by subgroup; consider specialized models if gaps are significant.

**Executive Action Required:** Explore subgroup-specific tuning or separate models for distinct populations.

### 6. Evaluation Bias

**What It Is:** Test or validation data doesn’t match real-world deployment.

**Example:** AI validated on past hires, not on current applicant pool demographics.

**Why It Matters:** Gives false confidence in fairness and performance.

**Business Impact:** Deployment failures, unexpected disparities, reputational damage.

**Detection Method:** Compare test data demographics to deployment environment; track intersectional metrics.

**Executive Action Required:** Use representative, up-to-date test data reflecting actual deployment conditions.

### 7. Learning Bias

**What It Is:** Bias arising from modeling choices that amplify or create disparities.

**Example:** A hiring recommendation model trained on resumes from a diverse applicant pool starts favoring candidates with certain educational pathways because the optimization process inadvertently weights features correlated with majority group patterns.

**Why It Matters:** Even with fair data, the model may perform worse for minority or underrepresented groups, creating inequitable outcomes.

**Business Impact:** Reduced diversity in hiring or customer outcomes, legal and reputational risks, and decreased trust in AI systems.

**Detection Method:** Analyze model behavior specifically for violations of your selected fairness definitions, for example, in the case of demographic parity definitions, you should examine overall prediction rate differences.

**Executive Action Required:** Audit modeling choices; ensure optimization objectives align with fairness goals; apply regularization or other model-level techniques to mitigate amplified disparities.

## V. **Detection Methodology**

### **1. Historical Bias**

* **What to do:**

  * Review findings from the Historical Context Assessment (HCA).
  * Focus on groups historically disadvantaged (e.g., women in tech roles, underrepresented racial groups).
  * Look at past patterns of discrimination that are documented in your sector.

* **Key questions to ask:**

  * Are our AI recommendations reflecting historical injustices?
  * Do disparities persist even when inputs seem “neutral”?
  * Which groups are most at risk of being unfairly affected today?

* **How to check:**

  * Compare outcome distributions across groups identified as high-risk in the Historical Context Assessment.
  * Analyze correlations between system predictions and historical patterns documented in the assessment.
  * Test whether current data distributions match historically documented disparities.

### **2. Representation Bias**

* **What to do:** Use the **Historical Context Assessment (HCA)** outputs to guide demographic audits. Examine dataset counts, coverage, and data quality for each subgroup.

* **Key questions:**

  * Which groups identified in the HCA are missing or underrepresented in the dataset?
  * Are the data distributions aligned with historical benchmarks, and are subgroups large enough to support reliable modeling?

* **Quantitative checks:**

  * Compare dataset demographics to population benchmarks established from historical context.
  * Analyze missing data patterns for correlation with protected attributes.
  * Assess data quality metrics across demographic groups identified in the historical assessment.

### **3. Measurement Bias**

**What to do:**

* Use the **Fairness Definition Selection Tool** to identify which measurement biases are most relevant.
* For **individual fairness**, focus on detecting inconsistent proxies across similar individuals.
* For **group fairness**, prioritize detecting systematic differences in how features or labels are measured across groups.

**Key questions:**

* Do features or labels measure the same concept equally across all groups?
* Are proxies unintentionally favoring or penalizing certain groups?
* Are similar individuals being treated consistently according to the fairness definitions you selected?

**How to check / Quantitative techniques:**

* Test proxy variables for differential accuracy across groups based on selected fairness criteria.
* Analyze feature distributions to identify encoding or transformation schemes that create disparities.
* Measure label consistency across annotators for different demographic groups.

### **4. Aggregation Bias**

**What to do:**

* Break down model performance by **demographic or user subgroups**.
* Test whether a single model adequately captures patterns for all groups.
* Consider whether specialized models or adjustments are needed for minority or niche populations.

**Key questions:**

* Are some groups consistently underserved by the current model?
* Would separate models or group-specific tuning improve fairness and performance?

**How to check:**

* Compare accuracy, false positive/negative rates, or other relevant metrics **for each subgroup**.
* Look for systematic underperformance among smaller or underrepresented groups.
* Assess whether model simplifications (e.g., generic features) disadvantage any subgroup.

### **5. Learning Bias**

**What to do:**

* Analyze model behavior specifically for violations of your selected fairness definitions. 
    * For equal opportunity definitions, focus on false negative rate disparities.
    * For demographic parity definitions, examine overall prediction rate differences.

**Key questions:**

* Are fairness constraints from the definition selection being respected in practice?

**How to check:**

* Measure model performance across groups according to your selected fairness metrics.
* Test regularization effects on minority group performance.
* Analyze model behavior against fairness constraints documented in your definition selection.

### **6. Evaluation Bias**

**What to do:**

* Audit your validation and test datasets to ensure they reflect the **actual deployment population**.
* Incorporate fairness metrics alongside standard accuracy measures.
* Pay attention to **rare cases, small subgroups, and intersectional identities** that may be underrepresented in test sets.
* Reference your **selected fairness definitions** to identify which evaluation metrics matter most for group- or individual-level fairness.

**Key questions:**

* Does the test data match the demographic and feature distribution of real-world deployment?
* Are small or unseen groups adequately represented in evaluation data?
* Do fairness metrics indicate disparities that overall accuracy might hide?

**How to check:**

* Compare test set demographics to deployment data or population benchmarks.
* Track subgroup-specific metrics (false positives/negatives, prediction rates) aligned with your fairness definitions.
* Conduct disaggregated analysis for intersectional groups to identify hidden disparities.


### **7. Deployment Bias**

**What to do:**

* Observe how the AI system is actually used in the field compared to its **intended design**.
* Monitor whether outputs are followed, ignored, or misinterpreted by users.
* Interview end-users and stakeholders to understand real-world practices and deviations.
* Map deployment environments to ensure **coverage, accessibility, and proper integration** with workflows.

**Key questions:**

* Are AI outputs being ignored, overridden, or misapplied in practice?
* Are recommendations delivered **at the right time and context** to the intended users?
* Do real-world constraints (infrastructure, training, workflow) affect fairness outcomes?

**How to check:**

* Monitor usage logs and compare observed behavior with intended processes.
* Audit the impact of real-world decisions on different demographic groups.
* Track post-deployment fairness metrics and identify gaps between expected and actual performance.

## VI. **Prioritization Framework**

After identifying potential bias sources in your system, the next step is to **prioritize them**. Not all biases are equally urgent or easy to fix, this framework helps teams focus on the most critical areas first, ensuring resources are spent effectively and the greatest harms are addressed early.

> **Tip:** For each score, document the rationale so that decisions are transparent and justifiable. This makes it easier to review, update, or defend prioritization choices later.

### **Assessment Dimensions**

Score each bias source on a **1–5 scale** based on the following dimensions:

* **Severity:** Potential harm if the bias remains unaddressed (e.g., legal, reputational, or economic consequences).
* **Scope:** Proportion of decisions or individuals affected by the bias.
* **Persistence:** Likelihood that the bias compounds over time through feedback loops or repeated use.
* **Intervention Feasibility:** Relative ease or cost of detection and remediation.
* **Historical Alignment:** Connection to documented historical patterns of discrimination or exclusion.

### **Priority Calculation**

Compute a **Priority Score** to guide which bias sources to tackle first:

$$
\text{Priority Score} = (Severity \times 0.3) + (Scope \times 0.2) + (Persistence \times 0.2) + (Historical Alignment \times 0.2) + (Intervention Feasibility \times 0.1)
$$

> **Tip:** For each dimension, note the reasoning behind your rating. This provides transparency for stakeholders and supports later reassessments.

### **Priority Categories**

* **High Priority:** Score ≥ 4.0 → Address immediately.
* **Medium Priority:** 3.0 ≤ Score < 4.0 → Plan remediation in near-term cycles.
* **Low Priority:** Score < 3.0 → Monitor and reassess periodically.

## VII. **Bias Reporting Template**

After detecting and prioritizing biases, use the following template to document and track mitigation efforts. This ensures transparency, accountability, and easy follow-up.

| **Bias Type**    | **Bias Source**   | **Evidence / Detection**                                                                            | **Impact (Who / How)**                                | **Priority** | **Proposed Fix**                            | **Owner**    | **Timeline** | **Monitoring Plan**                     |
| ---------------- | ----------------- | --------------------------------------------------------------------------------------------------- | ----------------------------------------------------- | ------------ | ------------------------------------------- | ------------ | ------------ | --------------------------------------- |
| Measurement Bias | ZIP as race proxy | Feature audit: ZIP correlates 0.8 with race → disaggregated errors confirm disparate rejection rate | Rural minority borrowers harmed → higher loan denials | High         | Remove ZIP or aggregate regionally; retrain | Data Science | 2 weeks      | Track subgroup approval rates quarterly |


> Tip: Always fill in the **rationale** for your priority scores and mitigation choices, so stakeholders can clearly understand decisions and progress.

## VIII. Validation Checklist

Before finalizing your Bias Source Report and action plan, confirm the following:

* [ ] **Historical Alignment:** Did we evaluate all bias sources against the Historical Context Assessment (HCA) and link them to our selected fairness definitions (FDS)?
* [ ] **Comprehensive Coverage:** Have we reviewed each bias type (Historical, Representation, Measurement, Aggregation, Learning, Evaluation, Deployment) and documented evidence, or confirmed none exist?
* [ ] **Detection Audit:** Did we save all detection tests and outputs, ensuring they are reproducible and clearly linked to specific bias sources?
* [ ] **Prioritization Transparency:** Have we scored all identified biases on severity, scope, persistence, historical alignment, and feasibility, and documented the rationale?
* [ ] **Mitigation Planning:** Did we assign an owner, propose a concrete fix, and set a realistic timeline for each high- or medium-priority bias?
* [ ] **Monitoring & Metrics:** Have we created a monitoring plan for each bias source, including subgroup/intersectional metrics, review frequency, and escalation procedures?
* [ ] **Stakeholder Readiness:** Did we ensure the Bias Source Report is complete, understandable to business and technical stakeholders, and ready for handoff?

**Pass Criteria:** Checklist is ≥80% complete, and every high-priority issue has an assigned owner and mitigation plan.

---

### ⏭️ Next Action: Proceed to [**Comprehensive Fairness Metrics**](./4-comprehensive-fairness-metrics.md)