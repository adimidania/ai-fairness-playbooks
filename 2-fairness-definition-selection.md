# Fairness Definition Selection Tool

## I. Purpose

This step helps your team **choose, justify, and document what “fairness” should mean for your AI system**.
The choice is context-dependent, stakeholder-sensitive, and involves ethical trade-offs as there is no one-size-fits-all answer.

**Why we do it:**
Different definitions of fairness can conflict mathematically, and choosing one definition over another always involves ethical trade-offs, stakeholder priorities, and legal considerations.

**Outcome (what you should produce by the end):**

* A **Fairness Definition Document** that includes:

  * The fairness definition(s) you will enforce
  * Why they were chosen (context, risk, stakeholder priorities)
  * Trade-offs you accept
  * How you will measure and monitor fairness over time

---

## II. Estimated time & effort

*(Adjust based on team size and complexity.)*

* **Small/simple project:** 3–5 hours total (2 people)
* **Medium project:** 6–12 hours total (3–4 people across 1–2 days)
* **Large/regulated project:** 3–5 days (cross-functional + possible external expert input)

Breakdown (typical medium project)

* **Review Historical Context outputs:** 1–2 hours

  * Summarize relevant findings (past harms, affected groups, regulatory constraints).

* **Decision tree walkthrough:** 2–3 hours

  * Cross-functional discussion to narrow down fairness definitions.
  * Address stakeholder priorities and disagreements.

* **Trade-off documentation:** 1–2 hours

  * Fill in the structured template (selected definition, rationale, trade-offs).

* **Alignment & sign-off:** 2–3 hours

  * Review with legal/compliance or leadership.
  * Make final adjustments and integrate into project docs.


## III. **Who should be involved**

* **Fairness lead / Audit owner** — coordinates and records decisions.
* **Product manager / Domain lead** — ensures business goals are represented.
* **Data scientist / ML engineer** — confirms model behavior and metrics.
* **Legal / Compliance** — flags regulatory constraints.
* **Optional:** Social scientist, UX researcher, or domain expert — for high-stakes or nuanced contexts.


> **If you’re a small team**, don’t worry as you can still complete this by discussing openly and working through each cell. The process is clear and you can always reach out for help.

---

## IV. **Step 01: Stakeholder Mapping & Priorities**

Before you choose fairness metrics, you need to **identify who is affected by your AI system and what each group values most.** This step ensures your fairness decisions reflect real-world concerns rather than abstract assumptions.

### **Your task:**

1. **List all stakeholders** involved or impacted by the system. Think broadly, not just users, but also those indirectly affected.
2. **Describe what each stakeholder would prioritize** (in plain language, without metrics).
3. **Note tensions or conflicts** where priorities differ.

### **Examples to guide you:**

* **In a lending model:**

  * *Applicants* may prioritize **equal access** and **avoiding unfair denials**.
  * *Banks* may prioritize **low default rates** and **trust in model outputs**.
  * *Regulators* may prioritize **compliance with anti-discrimination laws**.
* **In a criminal justice risk tool:**

  * *Defendants* may care most about **avoiding false accusations**.
  * *Prosecutors* may care about **not missing high-risk cases**.
  * *Society* may focus on **long-term safety and fairness across groups**.

---

## V. **Step 2: Fairness Definition Catalog (Reference)**


| **Definition**              | **Formula**                                 | **Philosophical Intuition**                                                                                               | **When to Use**                                                                          | **Example Scenarios**                                                                                       | **Limitations**                                                               |
| --------------------------- | ------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------- |
| **Demographic Parity**      | `P(Ŷ=1 \| A=a) = P(Ŷ=1 \| A=b)`             | **Egalitarian:** Everyone should have equal access to opportunities regardless of group membership.                       | When equal outcomes across groups are important (e.g., addressing historical exclusion). | A university outreach program ensuring equal interview rates for rural vs. urban applicants.                | Ignores qualifications; can reduce accuracy; may violate individual fairness. |
| **Equal Opportunity**       | `P(Ŷ=1 \| Y=1, A=a) = P(Ŷ=1 \| Y=1, A=b)`   | **Rawlsian justice:** Focus on improving outcomes for those who deserve success but might be unfairly overlooked.         | When false negatives (missing qualified individuals) matter most.                        | AI hiring tool ensuring qualified candidates are equally likely to be selected across genders.              | Doesn’t address false positives; assumes reliable labels.                     |
| **Equalized Odds**          | `P(Ŷ=1 \| Y=y, A=a) = P(Ŷ=1 \| Y=y, A=b)`   | **Balance of harms:** Both types of errors should be equally distributed across groups.                                   | When both false positives and false negatives matter.                                    | A loan approval model where both denying good borrowers and approving bad ones harm the business.           | More complex to implement; can reduce performance.                            |
| **Predictive Parity**       | `P(Y=1 \| Ŷ=1, A=a) = P(Y=1 \| Ŷ=1, A=b)`   | **Utilitarian / trust-based:** Predictions should mean the same thing for everyone, maximizing consistent interpretation. | When predictions must carry the same meaning across groups (focus on trust in scores).   | Criminal risk scoring tools ensuring “high risk” predictions mean equal likelihood of reoffending by group. | Incompatible with equal error rates if base rates differ.                     |
| **Individual Fairness**     | `Similar individuals → similar predictions` | **Liberal / individualist:** Treat people as individuals, not as members of a group.                                      | When consistency for similar individuals matters more than group-level metrics.          | Credit model where two applicants with identical profiles should get the same decision regardless of race.  | Requires domain-specific similarity metric; hard to define.                   |
| **Counterfactual Fairness** | `Ŷ(a) = Ŷ(a′)`                              | **Causal justice:** A protected attribute should not directly cause a different prediction.                               | When the goal is to remove bias caused by protected attributes in causal pathways.       | Medical diagnosis model ensuring outcome would be the same if a patient’s race or gender were different.    | Requires causal modeling; complex to explain and implement.                   |

---

## VI. **Step 3: Decision Tree for Selecting Fairness Definition**

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

## VII. **Step 4: Trade-Off Documentation Template**

Use this cell to summarize your decision:

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

---

## VIII. **Step 5: Validation — final checklist (must be completed before moving to the next step)**

Here’s a **question-driven version of your checklist** — it forces the team to *think* rather than just tick boxes, and it matches your goal of explicitly checking compromises and impacts.

---

## **Fairness Decision Quality Check**

Before moving to **bias source identification**, confirm you can clearly answer these questions:

* [ ] **Have we recorded our fairness decision?**

  * *Which definitions did we choose, and why?*

* [ ] **Did we ground it in historical context?**

  * *What past inequities or patterns are we addressing?*

* [ ] **Are stakeholder priorities explicitly documented?**

  * *Do all key groups understand or agree with this choice?*

* [ ] **Have we explained and justified trade-offs?**

  * *What are we giving up to achieve this fairness goal?*
  * *Who might be worse off under this choice — and why is that acceptable (or not)?*

* [ ] **Do we show the impact on different groups?**

  * *Are we clear on which groups gain, lose, or stay the same?*

* [ ] **Do we have a concrete monitoring plan?**

  * *Which metrics will we track, how often, and what triggers a recheck?*

**Pass rule:** *At least 80% complete, with **no critical fairness question unanswered** or brushed aside.*
