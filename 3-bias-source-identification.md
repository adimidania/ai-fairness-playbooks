# **Bias Source Identification Tool (BSI)**

## I. **Purpose**

This step helps your team **find where bias enters the system** — whether in data, features, model design, evaluation, or deployment — and decide which issues to fix first. You’ll turn **HCA (Historical Context Analysis)** insights and **FDS (Fairness Definition Selection)** decisions into:

* **Detection tests** to uncover bias
* **Evidence tables** that clearly document findings
* **Priority scores** to guide remediation

**Outputs you should produce:**

* **Bias Sources Report** listing bias types, evidence, and priorities
* **Action plan** with owners, timelines, and quick fixes vs. long-term changes
* **Monitoring plan** linking findings to metrics and dashboards

---

## II. **Estimated time & effort**

*(Adjust for project size and complexity.)*

* **Small/simple project:** 3–5 hrs total (2 people)
* **Medium project:** 6–12 hrs total (3–4 people, 1–2 days)
* **Large/regulated project:** 3–5 days (cross-functional + external expert input)

**Typical breakdown (medium project):**

* Review HCA & FDS outputs → 1–2 hrs
* Run detection procedures → 2–4 hrs
* Prioritize & score findings → 1–2 hrs
* Document & plan remediation → 1–2 hrs

---

## III. **Who should be involved**

* **Fairness lead / Audit owner** — coordinates the investigation and owns the report.
* **Data scientist / ML engineer** — runs audits and explains technical findings.
* **Product manager / Domain lead** — validates operational relevance.
* **Legal / Compliance** — flags regulatory constraints.
* **Optional:** UX researcher, social scientist, Ops for deployment checks.

> **Small team note:** You can run this solo or in a small group. Just document **assumptions and gaps clearly** if roles overlap.

---

## IV. **Bias Taxonomy (with explanations, examples, detection guidance)**

| **Bias Type**           | **What it is (plain)**                                             | **Why it matters**                                              | **Better Example**                                                                              | **How to detect it**                                                                                               |
| ----------------------- | ------------------------------------------------------------------ | --------------------------------------------------------------- | ----------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------ |
| **Historical Bias**     | Past discrimination embedded in data even if new data is clean.    | Keeps systemic injustices alive even if the AI seems neutral.   | Hiring model trained on decades of male-dominated resumes still screens out women.              | Check subgroup outcomes against known past discrimination; compare AI decisions with external fairness benchmarks. |
| **Representation Bias** | Missing or underrepresented groups in training data.               | If the model never "sees" a group, it can't serve them well.    | Face recognition works on light-skinned faces but fails on darker-skinned women.                | Audit dataset demographics; compare with population or user base; flag “data deserts.”                             |
| **Measurement Bias**    | Using bad proxies or features that don’t measure what you think.   | Features may be valid for one group but misleading for another. | Using cost of care to measure medical need → under-prioritizes Black patients with poor access. | Check feature meaning across groups; run correlation tests and subgroup validity checks.                           |
| **Aggregation Bias**    | A single model can't fit everyone equally well.                    | “One-size-fits-all” models favor majority patterns.             | Resume model treats all engineering roles the same, disadvantaging self-taught coders.          | Evaluate model separately by subgroup; consider specialized models if gaps are big.                                |
| **Learning Bias**       | Models optimize for average performance → minority errors ignored. | Models get “good” overall by being bad on small groups.         | Credit model works well overall but misclassifies women with nontraditional career paths.       | Compare false negative/positive rates per group; test impact of class imbalance.                                   |
| **Evaluation Bias**     | Test/validation data doesn't match real deployment.                | Leads to false confidence — “fair in lab, unfair in real life.” | Hiring model validated only on past hires, not on rejected but qualified candidates.            | Audit test sets; check group coverage; add intersectional fairness metrics.                                        |
| **Deployment Bias**     | Model is used differently than designed.                           | Even fair models fail if people misuse or bypass them.          | Recruiters ignore AI suggestions when hiring under pressure, skewing outcomes.                  | Observe real-world use; run post-deployment audits comparing intent vs practice.                                   |

---

## V. **Detection Methodology (organized per bias type)**

### **1. Historical Bias**

* **What to do:** Review HCA results. Check outcomes for groups historically disadvantaged.
* **Key questions:** Does AI mirror past injustice? Are disparities persisting despite “neutral” inputs?

### **2. Representation Bias**

* **What to do:** Run **dataset demographic audits** — count, coverage, and data quality per subgroup.
* **Key questions:** Who is missing? Are small groups big enough to learn meaningful patterns?

### **3. Measurement Bias**

* **What to do:** Audit features that could proxy protected traits (ZIP code → race).
* **Key questions:** Does the feature have different meanings for different groups? Does it work as intended everywhere?

### **4. Aggregation Bias**

* **What to do:** Break performance down by group. Test if one model is too crude.
* **Key questions:** Does “one-size-fits-all” fail certain subgroups? Do we need separate models?

### **5. Learning Bias**

* **What to do:** Audit training dynamics. Check if errors concentrate in minority groups.
* **Key questions:** Does optimizing for overall accuracy hurt smaller groups? Are class weights balanced?

### **6. Evaluation Bias**

* **What to do:** Inspect validation/test sets. Add fairness metrics beyond accuracy.
* **Key questions:** Does test data reflect deployment? Are rare cases or unseen groups missing?

### **7. Deployment Bias**

* **What to do:** Compare design assumptions vs real usage. Interview users if possible.
* **Key questions:** Is the AI decision overridden or ignored in practice? Are outputs delivered where/when needed?

---

## VI. **Prioritization Framework**

Score bias sources (1–5 each):

| **Dimension**            | **Meaning**                                  |
| ------------------------ | -------------------------------------------- |
| **Severity**             | Harm to rights, safety, or economic outcomes |
| **Scope**                | Number of people affected                    |
| **Persistence**          | Will it amplify over time?                   |
| **Historical Alignment** | Is it tied to real-world discrimination?     |
| **Fix Feasibility**      | Ease/cost of detection and remediation       |

---

## VII. **Report Template**

| **Bias Source**   | **Evidence / Detection**                                                                            | **Impact (Who / How)**                                | **Priority** | **Proposed Fix**                            | **Owner**    | **Timeline** | **Monitoring Plan**                     |
| ----------------- | --------------------------------------------------------------------------------------------------- | ----------------------------------------------------- | ------------ | ------------------------------------------- | ------------ | ------------ | --------------------------------------- |
| ZIP as race proxy | Feature audit: ZIP correlates 0.8 with race → disaggregated errors confirm disparate rejection rate | Rural minority borrowers harmed → higher loan denials | High         | Remove ZIP or aggregate regionally; retrain | Data Science | 2 weeks      | Track subgroup approval rates quarterly |

---

## VIII. **Step 4 Validation Checklist (as explicit questions)**

* [ ] **Have we linked historical context and fairness goals to this analysis?**
* [ ] **Have we reviewed each bias type in the taxonomy and documented evidence (or confirmed none)?**
* [ ] **Have we saved all detection runs with outputs for the audit trail?**
* [ ] **Have we scored and prioritized every identified bias with clear rationale?**
* [ ] **Do we have an owner and a fix or mitigation plan for each critical bias?**
* [ ] **Do we have a monitoring plan with specific metrics and a clear review cadence?**
* [ ] **Is our Bias Source Report ready for handoff with no unanswered red flags?**

**Pass rule:** At least 80% complete **and every critical issue has a named owner or mitigation plan.**