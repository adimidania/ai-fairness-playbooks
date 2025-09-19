# Fairness Definition Selection Tool

## I. Purpose

This step helps your team **choose, justify, and document what “fairness” should mean for your AI system**. The choice is context-dependent, stakeholder-sensitive, and involves ethical trade-offs as there is no one-size-fits-all answer.

**Why we do it:**
Different definitions of fairness can conflict mathematically, and choosing one definition over another always involves ethical trade-offs, stakeholder priorities, and legal considerations.

**Expected outcome:**  (what you should produce by the end):**

* A **Fairness Definition Document** that includes:

  * The fairness definition(s) you will enforce
  * Why they were chosen (context, risk, stakeholder priorities)
  * Trade-offs you accept
  * How you will measure and monitor fairness over time

### How to Navigate This Component
This guide is detailed and has many sections, but you don’t have to read it linearly.

* **New to this process?** Start at **[Section II: Estimated time & effort](#ii-estimated-time--effort)** as you need some context.
* **Returning user?** Jump straight to **[Section V: Fairness Definition Catalog](#vii-risk-classification-matrix)** or **[Section VIII: Trade-Off Documentation Template](#viii-how-to-write-the-historical-context-report-deliverables)** if you’re updating or validating work.
* Earlier sections set the stage, while later ones cover integration, validation, and practical tips.

### Quick Links
1. [Introduction](#i-introduction)
2. [Estimated time & effort](#ii-estimated-time--effort)
3. [Who should be involved](#iii-who-should-do-this-roles--responsibilities)
4. [How does this work?](#iv-how-does-this-work)
5. [Fairness Definition Catalog](#v-fairness-definition-catalog)
6. [Strategic Considerations for Fairness Selection](#vi-strategic-considerations-for-fairness-selection)
7. [Decision Tree for Selecting Fairness Definition](#vii-decision-tree-for-selecting-fairness-definition)
8. [Trade-Off Documentation Template](#viii-trade-off-documentation-template)
9. [Validation checklist](#ix-validation-checklist)

## II. Estimated time & effort

*(Adjust based on team size and complexity.)*

* **Small/simple project:** 6–8 hours total (2 people)
* **Medium project:** 8–15 hours total (3–4 people across 1–2 days)
* **Large/regulated project:** 3–5 days (cross-functional + possible external expert input)

Breakdown (typical medium project)

* **Review Historical Context outputs:** 1–2 hours

  * Summarize relevant findings (past harms, affected groups, regulatory constraints).

* **Review Fairness Definition Catalog:** 1–2 hours

  * Familiarize with available definitions, their trade-offs, and typical use cases.

* **Strategic Considerations for Fairness Selection:** 2–4 hours

  * Discuss business goals, legal requirements, and stakeholder priorities.
  * Capture context for the upcoming decision.

* **Decision Tree Walkthrough:** 2–3 hours

  * Cross-functional session to apply the tree and narrow down fairness definitions.

* **Trade-Off Documentation:** 1–2 hours

  * Complete structured template: selected definition, rationale, trade-offs, and impacted groups.

* **Alignment & Sign-Off:** 1–2 hours

  * Review with legal, compliance, or leadership.
  * Integrate decisions into project documentation. 

## III. **Who should be involved**

* **Fairness lead / Audit owner** — coordinates and records decisions.
* **Product manager / Domain lead** — ensures business goals are represented.
* **Data scientist / ML engineer** — confirms model behavior and metrics.
* **Legal / Compliance** — flags regulatory constraints.
* **Optional:** Social scientist, UX researcher, or domain expert — for high-stakes or nuanced contexts.


> **If you’re a small team**, don’t worry as you can still complete this by discussing openly and working through each cell. The process is clear and you can always reach out for help.

## IV. How does this work?

### **1. Stakeholder Mapping**

Identify **who is directly and indirectly affected** by your AI system. Write down what each group values most and note where their priorities may conflict. This step ensures fairness is grounded in real-world impact.

### **2. Explore Fairness Definitions**

Review the **catalog of fairness metrics** (demographic parity, equal opportunity, equalized odds, predictive parity, individual fairness, counterfactual fairness). Use the **decision tree** to narrow down which definition(s) best match your context.

### **3. Trade-Off Analysis**

**Document your fairness choice using the template, making sure to include:**

* The **fairness definition(s)** you selected
* The **reasoning behind the choice** (historical context, stakeholder needs, legal/policy constraints)
* The **trade-offs accepted** (e.g., accuracy, simplicity, or other fairness metrics)
* A **monitoring plan** for how fairness will be tracked and re-evaluated over time


### **4. Reporting**

Summarize findings in a Fairness Definition Selection Report, highlighting the chosen fairness definitions, why they fit given historical context, stakeholder needs, and legal constraints, the key trade-offs accepted (such as accuracy or other fairness metrics), and a clear monitoring plan to track fairness over time.

A checklist at the end confirms all steps are complete. If stuck, check the [**case study**](./5-case-study.md) and the [**appendix**](./6-appendix.md).

## V. Fairness Definition Catalog

Fairness isn’t one-size-fits-all. It’s a collection of sometimes competing concepts. Your team must decide explicitly which type of fairness aligns best with your business goals, stakeholder priorities, and legal requirements. Below is a catalogue of common fairness metrics—use it to understand the options, their trade-offs, and to guide your decisions throughout the project.

### **1. Demographic Parity (Equal Outcomes)**

**Formula:** `P(Ŷ=1 | A=a) = P(Ŷ=1 | A=b)`

**What It Means:** Equal percentages of all groups receive positive decisions, regardless of qualifications.

**Example:** If 10% of white applicants get hired, 10% of Black applicants should also get hired.

**When To Choose This:** Resource allocation, outreach programs, representation goals, or whenever proportional access is important.

**Business Rationale:** Ensures all groups have equal access to opportunities.

**Limitation:** May require lowering standards for some groups; ignores qualifications.

**Legal Context:** Often relevant for government programs and public accommodations.

### **2. Equal Opportunity (Equal Success Detection)**

**Formula:** `P(Ŷ=1 | Y=1, A=a) = P(Ŷ=1 | Y=1, A=b)`

**What It Means:** for everyone who truly deserves to succeed (Y=1), the probability they are selected should be the same for all groups.

**Example:** Among all qualified candidates in each group, the same proportion is selected—for instance, 80% of qualified men and 80% of qualified women are hired.

**When To Choose This:** Hiring, admissions, or merit-based decisions where it’s crucial not to miss qualified individuals, while ensuring fairness across groups.

**Business Rationale:** Ensures that all qualified candidates have an equal chance, maintaining quality standards and promoting equitable treatment across groups.

**Limitation:** Doesn’t control for errors among unqualified candidates (false positives); assumes that qualifications (labels) are correctly measured.

**Legal Context:** Supports employment and anti-discrimination laws by focusing on fair treatment of qualified applicants across protected groups.


### **3. Equalized Odds (Equal Treatment Overall)**

**Formula:** `P(Ŷ=1 | Y=y, A=a) = P(Ŷ=1 | Y=y, A=b)`

**What It Means:** All groups have equal rates of both correct positive and correct negative decisions.

**Example:** Loan approvals are equally accurate for all groups, both for good and bad credit risks.

**When To Choose This:** High-stakes decisions where both types of errors (false positives and false negatives) matter.

**Business Rationale:** Provides consistent accuracy and fairness across all groups.

**Limitation:** More complex to implement; may reduce overall performance.

**Legal Context:** Offers strong protection against discrimination claims.


### **4. Predictive Parity / Calibration (Equal Prediction Accuracy)**

**Formula:** `P(Y=1 | Ŷ=1, A=a) = P(Y=1 | Ŷ=1, A=b)`

**What It Means:** Predictions should have the same meaning across all groups; e.g., if AI says “80% chance,” it is correct 80% of the time for everyone.

**Example:** Criminal risk scores are equally predictive of reoffending across demographic groups.

**When To Choose This:** Risk assessment, probability-based decisions, or any context where consistency of prediction meaning is critical.

**Business Rationale:** Ensures all groups can trust the scores and decisions equally.

**Limitation:** Can conflict with equal error rates if base rates differ; may allow unequal treatment if calibrated correctly.

**Legal Context:** Important in regulated domains like finance, insurance, and public safety.

### **5. Individual Fairness (Similar Treatment for Similar People)**

**Formula:** Similar individuals → similar predictions

**What It Means:** Two people with comparable qualifications or profiles should get similar outcomes, regardless of group membership.

**Example:** Two job applicants with identical profiles receive the same score, even if they belong to different demographic groups.

**When To Choose This:** Personalized services, individual assessments, or when fairness at the person-level matters more than group-level metrics.

**Business Rationale:** Most intuitive and human-centered form of fairness.

**Limitation:** Requires defining a “similarity metric”; can be difficult in practice.

**Legal Context:** Aligns with civil rights principles of equal treatment.

## VI. Strategic Considerations for Fairness Selection

Now that you know the available fairness metrics and have explored the historical context, it’s time to answer key strategic questions to ensure your fairness choice aligns with business goals, legal requirements, and stakeholder priorities.

### Step 1: Identify Primary Business Goal
**Questions to Ask:**
- What outcome matters most for business success?
- Are we optimizing for quality, quantity, or representation?
- What are our key performance indicators?

### Step 2: Assess Legal Requirements
**Questions to Ask:**
- What regulations apply to our industry and use case?
- Are there specific fairness requirements we must meet?
- What are the consequences of non-compliance?

### Step 3: Consider Stakeholder Priorities
**Questions to Ask:**
- What do affected communities expect from our system?
- What do advocacy groups and regulators prioritize?
- How do internal stakeholders (engineering, product, legal) view trade-offs?

### Step 4: Evaluate Trade-offs
**Questions to Ask:**
- What business metrics are we willing to sacrifice for fairness?
- How much accuracy reduction is acceptable?
- What operational changes can we accommodate?

> ⚠️ **Tip:** Document your answers, they form the rationale for later decisions about which fairness metrics to use.

## VII. Decision Tree for Selecting Fairness Definition
Now that you have defined your strategic priorities, assessed historical context, and considered stakeholder needs, you can begin selecting fairness metrics. Remember, fairness is not one-size-fits-all: you can combine multiple metrics to address different objectives, rather than relying on a single definition. Use the decision tree below as a structured guide to help identify which metrics align best with your context and the types of errors or disparities you want to mitigate.

1. **Historical Context**

   * *Has this domain or product seen systematic exclusion or disadvantage for certain groups?*

     → **Yes:** include **Demographic Parity** in your fairness goals.

     *Example:* A hiring platform with past evidence of women being systematically underrepresented in technical roles.

     → **No:** go to Step 2.

2. **Error Sensitivity Analysis**

   * *Which mistake hurts more?*

     → **False Negatives (missing qualified people) → Equal Opportunity**

     *Example:* A scholarship eligibility model where missing qualified applicants is worse than awarding a few extra grants.

     → **False Positives (approving unqualified people) → Predictive Equality**

     *Example:* A security screening tool where wrongly flagging people creates major inconvenience and reputational harm.

     → **Both equally → Equalized Odds**

     *Example:* A loan approval model where both granting loans to bad applicants and denying loans to good applicants are costly.

3. **Score Visibility**

   * *Do end-users see the model’s scores or probabilities directly?*

     → **Yes:** include **Predictive Parity** or **Calibration**

     *Example:* A credit scoring app where applicants see their scores directly and expect them to have consistent meaning.

     → **No:** skip this step

    > **Key difference between Predictive Parity and Calibration:** Predictive parity only checks *one decision cutoff*, while calibration checks *all score levels*.


4. **Individual Impact**

   * *Is it critical to treat similar individuals consistently, regardless of group membership?*

     → **Yes:** consider **Individual Fairness** or **Counterfactual Fairness**

     *Example:* A medical triage system where two patients with nearly identical symptoms should get the same risk score, regardless of demographic differences.

*(Document your answers clearly, these become your rationale.)*

---

## VIII. Trade-Off Documentation Template

Use this section to clearly document your fairness decisions and the reasoning behind them. Summarize the metrics you selected, why they make sense given historical context and stakeholder priorities, what trade-offs you are accepting, and how you plan to monitor outcomes over time.

```markdown
### Chosen Fairness Definition(s)
[Write the metric(s) you selected — e.g., *Equal Opportunity*]

### Why This Makes Sense
- **Historical context:** What real-world history or bias are you addressing?
- **Stakeholder priorities:** Who benefits from this choice, and why?
- **Legal or policy constraints:** Are there external rules pushing you this way?
- **Type of harm mitigated:** What concrete harm are you trying to avoid (e.g., false negatives hurting disadvantaged applicants)?

### What We’re Giving Up (Trade-Offs)
- Where does this definition fall short?
- Which other fairness metrics might get worse as a result?
- Are you sacrificing a little model accuracy, speed, or simplicity to gain fairness?

### Monitoring and Future Check-Ins
- **Primary metric to track:** Which fairness definition is your main focus?
- **Secondary metrics to watch:** What will you monitor so things don’t drift badly?
- **Frequency:** How often will you check (monthly, quarterly)?
- **Trigger for action:** What would make you stop and re-evaluate this choice?
```

## IX. Validation Checklist

Before moving to **bias source identification**, confirm that you can clearly answer the following:

* [ ] **Did we record our fairness decisions?**

  * Are the chosen definitions or metrics clearly documented?
  * Did we explain why these were selected and how they relate to the business context?

* [ ] **Are decisions grounded in historical context?**

  * Did we identify past inequities or systemic biases that the model may replicate?
  * Did we consider intersectional impacts?

* [ ] **Did we document stakeholder priorities?**

  * Are the perspectives of affected communities, internal teams, and regulators captured?
  * Did we note any conflicts or trade-offs among stakeholder interests?

* [ ] **Did we explain and justify trade-offs?**

  * Did we specify which performance or operational metrics are being sacrificed?
  * Did we identify groups that might be negatively affected and explain why this is acceptable or how it will be mitigated?

* [ ] **Did we assess impact across different groups?**

  * Are we clear on which groups gain, lose, or remain the same under the chosen fairness approach?
  * Did we highlight intersectional impacts where relevant?

* [ ] **Do we have a concrete monitoring plan?**

  * Did we specify which fairness metrics will be tracked?
  * Did we define monitoring frequency and what triggers a re-evaluation of decisions?

**Pass rule:** At least 80% of these items are complete, with all **critical fairness questions answered** before proceeding.

---

### ⏭️ Next Action: Proceed to [**Bias Source Identification**](./3-bias-source-identification.md)
