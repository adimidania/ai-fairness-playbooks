# **Causal Fairness Toolkit**

## I. Introduction

The Causal Fairness Toolkit helps identify the mechanisms through which bias operates in your AI system. Rather than just detecting statistical disparities, it uncovers the causal pathways that create unfair outcomes, enabling targeted interventions.

### Why we do it

* **Statistical fairness metrics** indicate bias but don't explain why.
* To detect whether disparities stem from **direct, proxy, or mediator discrimination**.
* To evaluate fairness using **counterfactual “what if” reasoning**.
* To identify **intervention points** in the pipeline where changes will matter most.

> _💡: If you want to learn more about casual fairness so that you take the most out of this component, go ahead and read the notes available on [**the appendix**](./7-appendix.md)_.

### Outcome (what you should produce)

* A short **Causal Fairness Report** (2–4 pages) containing:

  1. Variable map (protected attributes, proxies, mediators, outcomes).
  2. Preliminary causal diagram(s).
  3. Counterfactual query examples + results.
  4. Pathway classification (fair vs. unfair).
  5. Intervention recommendations (pre-, in-, post-processing).
  6. Documentation of assumptions + uncertainties.

### How to Navigate This Component

This guide is detailed, but you don’t have to read it linearly.

* **New to this process?** Start at **[Section II: Estimated time & effort](#ii-estimated-time--effort)** as you need some context.
* **Returning user?** Jump straight to **[Section IV: Causal Modeling Template](#iv-causal-modeling-template)** if you’re updating or validating work.
* Earlier sections (I–III) set the stage, while later ones (IV–IX) cover integration, validation, and practical tips.

### Quick Links
1. [Introduction](#i-introduction)
2. [Estimated time & effort](#ii-estimated-time--effort)
3. [Who Should Do This](#iii-who-should-do-this)
4. [Causal Modeling Template](#iv-causal-modeling-template)
5. [Counterfactual Analysis Template](#v-counterfactual-analysis-template)
6. [Intervention Point Identification Methods](#vi-intervention-point-identification-methods)
7. [Intersectionality Consideration](#vii-intersectionality-consideration)
8. [Deliverables](#viii-deliverables)
9. [Validation checklist](#ix-validation-checklist)

## II. Estimated Time & Effort

* **Small/simple project:** 4–6 hours (2 people).
* **Medium project:** 1–2 days (3–4 people).
* **Large/regulated project:** 3–5 days (cross-functional team).

Breakdown (medium project):

* Variable identification & graph sketching: 2–3 hours.
* Counterfactual formulation & testing: 2–4 hours.
* Pathway classification workshop: 1–2 hours.
* Intervention mapping: 1–2 hours.
* Report drafting: 2 hours.

## III. Who Should Do This

**Minimum core team:**

* **Fairness lead / Audit owner** — coordinates, owns report.
* **Data scientist / ML engineer** — builds graphs, counterfactuals.
* **Domain expert** — validates what causal links make sense.
* **Legal / Compliance** — checks implications of intervention.

**Optional / helpful:**

* **Stakeholder rep / UX** — validates fairness judgments from a user perspective.
* **Social scientist** — advises on systemic mediators (e.g., wage gaps, education).

## IV. Causal Modeling Template
**Purpose:** Map variables and relationships so you can spot where unfairness could arise.

### **Step 1 — Variable Identification & Mapping**

**What to do:** 
List and classify all variables in your dataset or model. For each category, provide clear examples and a short justification for why the variable belongs there. 

* Variables can classify as:

  * **Protected attributes** — characteristics that legally/ethically must not be the basis for decisions (e.g., gender, race).

    * *Example: Gender — should not directly affect loan approval.*

  * **Mediators** — variables that are causally influenced by protected attributes and then affect outcomes.

    * *Example: Income level may be lower for women due to wage gaps which then impacts loan approval.*

  * **Proxies** — variables strongly correlated with protected attributes, even if not causally related.

    * *Example: ZIP code can act as a proxy for race due to historical housing segregation.*

  * **Outcomes** — the predictions or decisions made by the system.

    * *Example: Loan approval decision (Yes/No).*

  * **Legitimate predictors** — variables ethically allowed to affect outcomes because they directly measure relevant ability or risk.

    * *Example: Debt-to-income ratio, fairly reflects repayment ability.*


**A template you can use:**

```markdown
* **Protected attributes**
  *Your variables:* \[ ... ]
  *Justification:* Why are these protected?

* **Mediators**
  *Your variables:* \[ ... ]
  *Justification:* How are they influenced by protected attributes and affecting outcomes?

* **Proxies**
  *Your variables:* \[ ... ]
  *Justification:* Why do they act as stand-ins for protected attributes?

* **Outcomes**
  *Your variables:* \[ ... ]
  *Justification:* What is the system ultimately trying to predict/decide?

* **Legitimate predictors**
  *Your variables:* \[ ... ]
  *Justification:* Why are these fair/relevant predictors?

```

### **Step 2 — Identifying Discrimination Mechanisms**

**What to do:**
Identify and document the possible root causes of bias in your application by classifying whether unfairness arises from: **Direct discrimination**, **Indirect (mediator-based) discrimination** or **Proxy discrimination**.

#### 1. **Direct Discrimination**

  ***Definition:*** When a protected attribute itself is directly used in decision-making.

  ***Guiding Question:*** Does the protected attribute directly influence the model’s output?

  ***Example:*** A hiring model that explicitly assigns lower scores to female applicants.

#### 2. **Indirect Discrimination (via Mediators)**

  ***Definition:*** When a protected attribute influences another factor (a mediator), which then affects the outcome.

  ***Guiding Question:*** Does the protected attribute affect mediator variables that then feed into the outcome?

  ***Example:*** Gender influences career breaks for childcare → the model penalizes gaps in employment → women are disadvantaged.

#### 3. **Proxy Discrimination**

  ***Definition:*** When a variable that seems neutral is so strongly correlated with a protected attribute that it effectively substitutes for it.

  ***Guiding Question:*** Do decisions rely on variables that act as stand-ins for protected attributes?

  ***Example:*** ZIP code used in credit scoring → strongly correlated with race due to historical redlining.

Now for each identified pathway, document:  
- **Causal mechanism**: How the bias flows.  
- **Evidence strength**: Statistical support for the relationship (e.g., correlations, regression results, domain studies).  
- **Ethical assessment**: Whether the pathway is problematic from a legal/ethical perspective.  
- **Intervention priority**: Based on harm potential and feasibility of mitigation.  

**A template you can use:**
```markdown
### 1. Direct Discrimination

- **Causal mechanism:** [Describe how the protected attribute → directly affects outcome]  
- **Evidence strength:** [What data/statistics support this?]  
- **Ethical assessment:** [Why is this problematic? Any exceptions?]  
- **Intervention priority:** [High / Medium / Low + short reasoning]  

### 2. Indirect Discrimination (via Mediators)

- **Causal mechanism:** [Describe how protected attribute → mediator → outcome]  
- **Evidence strength:** [What evidence/statistics support this mediation?]  
- **Ethical assessment:** [Is the mediator fair or unfair to include?]  
- **Intervention priority:** [High / Medium / Low + short reasoning]  

### 3. Proxy Discrimination

- **Causal mechanism:** [Describe how proxy variable → mirrors protected attribute → outcome]  
- **Evidence strength:** [Statistical correlation strength or empirical studies]  
- **Ethical assessment:** [Why is this proxy unfair/problematic?]  
- **Intervention priority:** [High / Medium / Low + short reasoning]  
```


### **Step 3 — Causal Graph Construction**
A causal graph is like a roadmap showing how variables connect and influence each other.

**What to do:**

* Draw a preliminary graph linking the variables from Step 1.
* Use directed arrows (→) to represent causal paths.
* Use dashed arrows (↔) for correlations without proven causation.
* Use different node shapes (or colors) to distinguish protected attributes, mediators, proxies, outcomes, and legitimate predictors.
* Annotate assumptions (why you think a link exists) and mark uncertain links with question marks.

## V. Counterfactual Analysis Template
**Purpose:** he Counterfactual Analysis Template enables evaluation of whether an AI system exhibits causal discrimination by examining how predictions would change under counterfactual scenarios where protected attributes take different values.

### **Step 1 — Counterfactual Queries**

**What to do:**

* Write “what if” questions: *Would this prediction change if gender/race were different, holding all causally independent factors constant?*

* Document:
  * **Base case** (original features, outcome).
  * **Counterfactual** (changed protected attribute, unchanged independent vars).
  * **Expected fair outcome** vs **actual model behavior**.

**A template you can use:**
```markdown
- **Case ID:** [Unique identifier for the case]  
- **Base case:**  
  - Protected attribute(s): [e.g., Gender = Female]  
  - Outcome: [Model’s prediction for the base case]  

- **Counterfactual scenario:**  
  - Changed protected attribute(s): [e.g., Gender = Male]  
  - Held constant: [List variables that must remain unchanged, e.g., credit score, income]  

- **Comparison:**  
  - Expected fair outcome: [What should happen if the model is fair?]  
  - Actual model outcome: [What the model actually did]  

- **Notes:** [Any discrepancy, patterns, or ethical concerns observed]  
```

### **Step 2 — Pathway Classification**

**What to do:**

* Identify which causal paths are **fair vs unfair**.
* Use domain + stakeholder input to decide:

  * **Fair:** reflect legitimate differences (e.g., skills).
  * **Unfair:** reflect bias or discrimination (e.g., gender → wage gap → income).

* Document rationale.

**A template you can use:**
```markdown
### Pathway: 
[Describe the causal path, e.g., Gender → Income → Loan Approval]

- **Classification:** [Fair / Unfair]  
- **Rationale:** [Why is this pathway considered fair or unfair? Include domain knowledge + stakeholder perspectives]  
- **Notes:** [Any assumptions, uncertainties, or conditions where this might change]
```

**Example:**

| Pathway                                    | Type                 | Fair/Unfair | Evidence                              | Intervention                            |
| ------------------------------------------ | -------------------- | ----------- | ------------------------------------- | --------------------------------------- |
| Gender → Wage gap → Income → Loan approval | Mediator             | Unfair      | Historical wage inequality documented | Adjust threshold / fairness constraints |
| Debt-to-income ratio → Loan approval       | Legitimate predictor | Fair        | Direct repayment ability              | Keep                                    |

## VI. Intervention Point Identification Methods

**Purpose:** Translate causal analysis results into actionable fixes. This step ensures your team doesn’t just diagnose unfairness but also chooses the right kind of intervention in the ML pipeline.

### **Step 1 — Map Causal Patterns to Interventions**

Use the following **decision tree** to select an appropriate intervention (more about these techniques in the next components):

1. If direct discrimination is present (direct path from protected attribute to outcome): **Is the protected attribute explicitly used as a feature?**
      - Yes: Apply in-processing constraints or remove the attribute
      - No: Investigate implicit direct discrimination through model architecture

2. If proxy discrimination is present (path through correlated but not causally related variables): **Can the proxy variables be identified?**
      - Yes: Consider pre-processing approaches to transform these variables
      - No: Apply in-processing regularization to minimize proxy use

3. If mediator discrimination is present (path through variables causally influenced by protected attributes): **Are the mediator variables legitimate predictors for the task?**
      - Yes: Consider using multi-objective optimization to balance fairness with prediction
      - No: Apply pre-processing to remove the influence of protected attributes on these variables

4. If outcome discrimination is present (disparities in model outputs): **Are the disparities consistent across subgroups?**
      - Yes: Consider post-processing approaches like threshold optimization
      - No: Apply more targeted interventions based on causal structure

### **Step 2 — Limited Information Adaptation**

In most fairness applications, we don’t have perfect causal knowledge or the luxury of randomized experiments. Instead, we’re working with incomplete data, observational records, and lots of uncertainty. This step is about adapting our causal reasoning to these limitations while still making fairness analysis actionable.

This involves the following moves:

#### **1. Start with What You Know**

* **Inventory domain knowledge**: Gather what experts, stakeholders, or prior research already know about potential causal links in your business domain. For example, in a loan system, we may know education impacts income, and income impacts repayment likelihood.

* **Identify gaps**: Explicitly mark where knowledge is missing. If we don’t know whether “neighborhood” directly influences repayment or only acts as a proxy for income, note that uncertainty.

* **Document assumptions**: Write down causal assumptions and confidence levels so later decisions are transparent (e.g., *we assume education has no direct biasing effect beyond income*).

#### **2. Apply Observational Methods to Approximate Experiments**

Since we can’t manipulate protected attributes, we use data-driven techniques that mimic “what-if” scenarios:

**1. Matching (e.g., Propensity Score Matching)**
*Idea:* Compare individuals who are nearly identical on all observed characteristics except the protected attribute.

*Example:*
Two loan applicants with the same income, debt ratio, and credit history — but one is male, the other female. Matching lets us ask: *Would approval rates differ if gender were swapped?*

*Practical tip:* Stronger when you have rich data on applicants; weaker if your dataset misses important variables (e.g., family wealth).

**2. Instrumental Variables (IVs)**
*Idea:* Use an external factor (an “instrument”) that shifts the protected attribute but does not directly affect the outcome.

*Example:*
Suppose a historical policy expanded women’s access to higher education in certain regions. The policy (instrument) affects education attainment (linked to gender) but does not directly determine job hiring outcomes. By comparing cohorts, you can isolate how gender-linked differences in education feed into hiring.

*Practical tip:* IVs are rare and tricky. They only work if the instrument influences the outcome *only* through the protected attribute (not through other side channels).

**3. Regression Discontinuity (RD)**
*Idea:* Exploit cutoff-based decisions where people just above and below the threshold are similar, except for the treatment they received.

*Example:*
If a bank approves loans for credit scores ≥ 650, compare applicants with scores of 649 vs. 651. They’re virtually identical, except one group got approved, the other didn’t. If approval rates differ by race within this narrow band, that signals bias.

*Practical tip:* Only works if enough data exists near the cutoff.

**4. Difference-in-Differences (DiD)**
*Idea:* Compare changes over time between groups when a policy or rule affects one group but not the other.

*Example:*
If a company introduces blind résumé screening in 2023, you can compare:

* Women’s vs. men’s hiring rates before vs. after the policy.
* The *difference in differences* shows whether the intervention reduced gender bias.

*Practical tip:* Requires time-series or panel data and a clearly defined intervention moment.

#### **3. Quantify and Communicate Uncertainty**

Because limited data = limited certainty, we must build “safety margins” into our conclusions:

* **Sensitivity Analysis**: Stress-test your findings by asking: *What size of unmeasured confounder would overturn this conclusion?* (e.g., using **E-values**).

* **Report bounds, not points**: Instead of saying *“race causes 15% of the disparity,”* say *“race likely explains 10–20% of the disparity.”*

* **Visualize uncertainty**: Show how different assumptions change the result — this helps stakeholders understand limits without losing trust.

## VII. Intersectionality Consideration 

Bias doesn’t always act on a single attribute (e.g., race **or** gender). Often, disadvantage emerges at the **intersection** (e.g., *Black women* may face unique patterns of discrimination not visible when analyzing race or gender separately). You can address this by using:

1. **Compound Counterfactuals**

   * Instead of changing only one attribute (e.g., gender), also test *combinations* (e.g., “What if this applicant were male **and** white?”).
   * This reveals whether multiple protected attributes amplify unfairness together.

2. **Subgroup-Specific Analysis**

   * Don’t stop at broad groups. Assess outcomes for **intersectional subgroups** (e.g., Latina women vs. Latino men vs. White women).
   * Compare whether fairness metrics vary across these subgroups, not just overall categories.

3. **Hierarchical Analysis**

   * **Level 1:** Assess fairness at the overall group level (e.g., all women vs. all men).
   * **Level 2:** Drill down into **intersectional subgroups** (e.g., Asian women vs. Black women).
   * **Level 3:** Where possible, assess **individual-level counterfactual fairness** — asking if a person would have been treated differently if their intersecting attributes were different.


## VIII. Deliverables

Sections to include:

* Executive summary (1 paragraph).
* Variable identification table.
* Causal diagram(s).
* Counterfactual examples + results.
* Pathway classification table (fair/unfair + rationale).
* Intervention recommendations.
* Risk scoring + top 2–3 priority issues.
* Assumptions + limitations.

## IX. Validation Checklist

* [ ] Did we clearly identify all relevant protected attributes?
* [ ] Did we consider intersectional combinations (e.g., gender × race)?
* [ ] Did we capture potential proxy variables that might stand in for protected attributes?
* [ ] Did we check for mediators that could unfairly transmit bias?
* [ ] Did we sketch a causal graph that reflects our assumptions?
* [ ] Did we mark uncertain or debated links explicitly?
* [ ] Did we write “what if” queries that test fairness meaningfully?
* [ ] Did we compare base vs. counterfactual outcomes and document discrepancies?
* [ ] Did we classify each pathway as fair or unfair, and record our rationale?
* [ ] Did we include input from domain experts or stakeholders in those judgments?
* [ ] Did we map discrimination types to possible interventions (pre-, in-, post-processing)?
* [ ] Did we prioritize interventions based on harm and feasibility?
* [ ] Did we explicitly note uncertainties and untested assumptions?
* [ ] Did we perform sensitivity analysis (e.g., E-values) to see how robust findings are?
* [ ] Did we create a risk matrix highlighting the top issues?
* [ ] Did we draft a Causal Fairness Report that is concise, clear, and actionable?

**Pass / Fail rule:**

* If **at least 80% of items are complete**, you can move forward to the next step.

* If not, pause and fill the gaps before starting the next component.

---

### ⏭️ Next Action: Proceed to [**Pre-processing Toolkit**](./2-pre-processing-toolkit.md)