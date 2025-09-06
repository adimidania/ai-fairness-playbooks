# Historical Context Assessment Tool

## I. Purpose

This step helps your team understand the social, political, and historical forces that shape the problem your AI tries to solve. These factors directly influence model performance, market reach, and regulatory risk.

**Why we do it:**

* To surface *hidden* harms that are not visible in the dataset alone.
* To avoid repeating past injustices by blindly optimizing for historical outcomes.
* To identify which groups may be harmed and how (including intersectional harms that are often ignored).
* To give the rest of the audit (definitions, metrics, mitigation) a meaningful anchor.

> ⚠️: **This step is really important, it's like security audits; skipping context work now leads to expensive failures later.**

**Outcome (what you should produce by the end):**

* A short `Historical Context Report` (1–3 pages) containing:

  1. Domain summary + key historical harms.
  2. Data provenance notes (where data came from and how it was created).
  3. List of likely proxy features and data deserts.
  4. Risk Matrix with prioritized items (scores + explanation).
  5. Recommended next actions (feeding into Fairness Definition Selection Component).

---

## II. Estimated time & effort

(Adjust based on team size and project's complexity.)

* **Small/simple project:** 4–6 hours total (2 people)
* **Medium project:** 8–16 hours total (3–4 people across 1–2 days)
* **Large/regulated project:** 2–5 days (cross-functional + possible external expert input)

Breakdown (typical medium project):

* Domain research: 2–3 hours
* Questionnaire completion: 2–4 hours (cross-functional workshop)
* Risk scoring & matrix: 1 hour
* Report writing & integration: 2–3 hours

---

## III. Who should do this (roles & responsibilities)

Minimum core team:

* **Fairness lead / Audit owner** — coordinates the exercise, owns the report.
* **Data scientist / ML engineer** — explains data origins, features, and technical implications.
* **Product manager / Domain lead** — explains the product function and user groups.
* **Domain expert** (internal or external) — clarifies real-world domain practices and harms.
* **Legal / Compliance** — flags regulatory or policy constraints.

Optional / as-needed:
* **Social scientist / historian** — for deep historical context (use when risk & relevance are high).
* **UX / Researcher** — for lived-experience input and to help identify affected user journeys.
* **QA / Ops** — to note operational implications and feedback loop risks.

> **If you’re a small team**, don’t get discouraged. You can still complete this notebook by sharing the work among yourselves. The process is clearly laid out step-by-step, and you can always reach out to external advisors or peers if you hit a gap.

---

## IV. How does this work?

### **1. Research**

You will first complete a questionnaire of the sections:

1. **Domain & Application Context**.
2. **Step 2 — Data & Representation Analysis**.
3. **Technology Transition & Feedback Loops**.

### **2. Risk Classification Matrix**

Use a **Risk Classification Matrix** to rate each historical pattern by **severity, likelihood, and relevance**, classifying them as 🔴 Critical, 🟠 High, 🟡 Medium, or 🟢 Low.

### **3. Reporting**

Summarize findings in a **Historical Context Report**. This feeds directly into the next steps of the audit.
A checklist at the end confirms all steps are done. If stuck, check the **case study** and **glossary**.

---

## V. Questionnaire

### Step 1 — Domain & Application Context

**What to do**

* Describe the domain (e.g., lending, hiring) and the specific product feature (e.g., installment approval).

* Identify your user groups, both direct users and anyone affected by the system’s outcomes, even if they don’t interact with it directly (e.g., job applicants using the platform, plus qualified candidates who never applied because of biased outreach).

* Document past policies or practices that harmed these groups (e.g., redlining, segregation policies, discriminatory hiring rules).

**Why**
* Knowing domain-specific harms tells you which fairness definitions and metrics will matter.

**Evidence to collect**
* Links to studies, news articles, government reports, internal audit notes, policy papers.

---

### Step 2 — Data & Representation Analysis

**What to do**

* Inventory all data sources — who collected them, when, how, and for what purpose (e.g., government survey vs. marketing database).
* Spot proxy variables — features that indirectly encode sensitive attributes (e.g., ZIP → race, job title → gender).
* Find “data deserts” — not just single groups (e.g., women), but intersections (e.g., older women in rural areas, Black women in tech roles) that are missing or sparse.
* Check encoding methods — how categories (like race or income brackets) are numerically represented; are they oversimplified or distorted?

**Why**
* Data reflects historical and social choices, not just “facts.”
* If your dataset overlooks entire intersections of identity (e.g., disabled immigrants), your model may fail hardest where the harm is most severe.
* Identifying gaps helps you improve accuracy and avoid regulatory or reputational risk by not systematically excluding groups.

---

### Step 3 — Technology Transition & Feedback Loops

**What to do**

* Identify what system existed before — was it a manual decision process, a rules-based engine, or no system at all?
* Sketch feedback loops — could incorrect or biased predictions lead to self-reinforcing patterns (e.g., fewer approvals → less data → more biased training)?

**Why**
* Automation can lock in biases via repeated reinforcement. Understanding feedback loops helps design safeguards.

---

## VI. Risk Classification Matrix — how to use it (step-by-step)

**Purpose:** prioritize where to act first. You will score each identified historical pattern across three dimensions:

1. **Severity (S)** — How harmful would this be if it happens?

   * 1 = Low (annoyance; doesn’t cause measurable harm)
   * 2 = Medium (individual harm, reputational risk)
   * 3 = High (systemic or serious individual harm, legal/regulatory risk)

2. **Likelihood (L)** — How likely is this bias to occur given your system/data?

   * 1 = Low (limited evidence, rare)
   * 2 = Medium (some evidence or plausible given features)
   * 3 = High (strong historical signal or clear proxy)

3. **Relevance (R)** — How tied is the harm to your product’s core functionality?

   * 1 = Low (peripheral feature)
   * 2 = Medium (affects some users or flows)
   * 3 = High (affects core decisions or many users)

**Score calculation:** `Score = S + L + R` (range 1-9).
**Priority bins (example for 1–3 scale):**

* Score 8–9 → 🔴 Critical — immediate action required
* Score 6–7 → 🟠 High — plan remediation before deployment
* Score 4–5 → 🟡 Medium — monitor and include in near-term roadmap
* Score 3 → ⚪ Low — document, monitor over time

**How to score in practice:**

* For each identified pattern (e.g., “ZIP code proxy for race”), the team discusses and assigns S, L, R.
* Document the rationale in 1–2 sentences (important for audit trail).
* Use the matrix to pick the top 2–3 items to feed into the Fairness Definition Selection AND the mitigation plan.

**Example entry:**

* Pattern: ZIP code as proxy for race

  * Severity = 3 (can lead to systemic exclusion)
  * Likelihood = 3 (ZIP present in dataset; historical redlining evidence)
  * Relevance = 3 (used directly in model features)
  * Score = 9 → 🔴 Critical
  * Rationale: “ZIP present in training set, known redlining history, used in decision logic.”

---

## VII. How to write the Historical Context Report (deliverables)

Include these sections (short & focused):

1. Executive summary (1 paragraph)
2. Domain & application summary
3. Key historical harms (bullet list + links)
4. Data provenance & proxies (table)
5. Technology & feedback loop notes (diagram)
6. Risk Matrix (scores + the 3 highest priority items)
7. Recommended next steps (concrete, who owns them, deadlines)
8. Appendix: raw questionnaire answers, assumptions and source links

**Deliver the report** to the fairness lead, product manager, and the VP or governance body.

---

## VIII. Integration: what happens next

* Top-priority items feed into **Fairness Definition Selection**.
* Documented proxies inform **Bias Source Identification** (which features to test).
* Data deserts guide targeted data collection or model uncertainty quantification.

---

## IX. Validation — final checklist (must be completed before moving to the next step)

Use this checklist to confirm that your **Historical Context Assessment (HCA)** work is thorough and ready to hand off. Tick and add short notes as you go.

* [ ] **Scope defined:** Domain, product function, and stakeholders are clearly listed.

  * Did we capture **who is directly and indirectly affected** by the system?
  * Did we review **different time periods** to understand how harms evolved?

* [ ] **Data inventory** recorded with origins, coverage, and any known gaps or biases.

  * Can someone **new to the project** understand the dataset’s social and historical background from this documentation?
  * Did we **document assumptions** about what the data represents (and what it doesn’t)?

* [ ] **Proxy list** created — variables that may indirectly encode sensitive attributes.

  * Did we **consider multiple kinds of bias**, including **intersectional ones** (e.g., race × gender × class)?
  * Are there variables that **behave differently across groups**?

* [ ] **Data deserts** identified — groups with little or no representation.

  * Did we **check with real community members or domain experts**: does this data reflect their reality?

* [ ] **Feedback loops** mapped — how model outputs could affect future data.

  * Did we think through **who benefits and who is burdened** as the system evolves over time?

* [ ] **Risk Matrix** completed — severity, likelihood, relevance scored with rationales.

  * Are we clear on **why each score was assigned** (1–2 sentences documented)?
  * Did we **prioritize risks that have strong historical evidence** over speculative ones?

* [ ] **Top priority risks** identified — clear owners (or action plan if solo) and timelines.

  * Does every **critical (🔴) risk** have an **action owner** or at least a mitigation plan?

* [ ] **Historical Context Report** drafted — summarizes findings and reasoning.

  * Does the report explain **which social or historical forces matter most** to this system?
  * Could **a VP or auditor read it in 5 minutes and understand the risks?**

* [ ] **Handoff prepared:** ready to inform Fairness Definition Selection and Bias Source Identification steps.

  * Is it **explicit how this work connects to the next audit steps** (fairness definitions, bias tests)?

**Pass / Fail rule:**

* If **at least 80% of items are complete** and every 🔴 **Critical risk** has an owner (or mitigation plan if you’re working alone), you can move forward.
* If not, pause and fill the gaps before starting the next component.

---

## X. Quick practical tips

* If you must choose, prioritize **evidence-backed risks** (documented history + present proxy in data) over theoretical ones.
* Keep language simple in the report, VPs prefer short, actionable summaries.
* Save everything: justification for scores is vital for audits and for explaining your choices later.
