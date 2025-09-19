# Historical Context Assessment Tool

## I. Introduction
> ⚠️ **Important:** Skipping this step is like skipping a security audit. Context work now prevents expensive failures later.

Building fair AI isn’t just about numbers — it’s about understanding the **social, political, and historical context** of the problem your system addresses. These factors shape not only fairness, but also **model accuracy, market adoption, and regulatory exposure**.

**Why this step matters:**

* **Reveal hidden risks** — surface harms that raw data alone can’t show.
* **Avoid legacy bias** — prevent the AI from repeating or amplifying past discriminatory practices.
* **Protect vulnerable groups** — identify who could be disproportionately harmed, especially at intersections of race, gender, class, or other attributes.
* **Anchor the audit** — provide a solid foundation so later steps (definitions, metrics, mitigation) are grounded in real-world impact.

**Expected outcome:** a short `Historical Context Report` (1–3 pages) with:

1. Domain summary + key historical harms.
2. Data provenance notes (where data came from and how it was created).
3. List of likely proxy features and data deserts.
4. Risk Matrix with prioritized items (scores + explanation).

### How to Navigate This Component

This guide is detailed, but you don’t have to read it linearly.

* **New to this process?** Start at **[Section II: Estimated time & effort](#ii-estimated-time--effort)** as you need some context.
* **Returning user?** Jump straight to **[Section V: Research](#vii-risk-classification-matrix)** or **[Section VIII: Report Writing](#viii-how-to-write-the-historical-context-report-deliverables)** if you’re updating or validating work.
* Earlier sections (I–IV) set the stage, while later ones (IX–XI) cover integration, validation, and practical tips.

### Quick Links
1. [Introduction](#i-introduction)
2. [Estimated time & effort](#ii-estimated-time--effort)
3. [Who should do this (roles & responsibilities)](#iii-who-should-do-this-roles--responsibilities)
4. [How does this work?](#iv-how-does-this-work)
5. [Research](#v-research)
6. [Questionnaire](#vi-questionnaire)
7. [Risk Classification Matrix](#vii-risk-classification-matrix)
8. [How to write the Historical Context Report](#viii-how-to-write-the-historical-context-report-deliverables)
9. [Validation checklist](#ix-validation-checklist)
10. [Quick practical tips](#x-quick-practical-tips)


## II. Estimated time & effort

(Adjust based on team size and project's complexity.)

* **Small/simple project:** 4–6 hours total (2 people)
* **Medium project:** 8–16 hours total (3–4 people across 1–2 days)
* **Large/regulated project:** 2–5 days (cross-functional + possible external expert input)

Breakdown (typical medium project):

* **Domain research:** 2–3 hours
* **Questionnaire completion:** 2–4 hours
* **Risk scoring & matrix:** 1 hour
* **Report writing & integration:** 2–3 hours

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

> 👥: **If you’re a small team**, don’t get discouraged. You can still complete this notebook by sharing the work among yourselves. The process is clearly laid out step-by-step, and you can always reach out to external advisors or peers if you hit a gap.

## IV. How does this work?

### **1. Research**

Before answering the questionnaire, gather background knowledge on your domain by reviewing its historical, social, and regulatory context, identifying past patterns of discrimination, collecting key references (research, laws, policies, community input), and noting any assumptions or gaps that may need further inquiry.

### **2. Questionnaire Completion**

Once research is complete, work through the questionnaire sections with your team:

1. **Domain & Application Context**
2. **Data & Representation Analysis**
3. **Technology Transition & Feedback Loops**

Document answers with specific examples and references from your research.

### **3. Risk Classification Matrix**

Use a **Risk Classification Matrix** to rate each identified historical pattern by:

* **Severity**
* **Likelihood**
* **Relevance**

Classify risks as 🔴 Critical, 🟠 High, 🟡 Medium, or 🟢 Low.

### **4. Reporting**

Summarize findings in a **Historical Context Report**, highlighting critical and high-priority risks. This report feeds directly into the next phases of the audit.

A checklist at the end confirms all steps are complete. If stuck, check the [**case study**](./5-case-study.md) and the [**appendix**](./6-appendix.md). 


## V. Research

**What to do**

* Gather historical information specific to your application domain.

* Review academic literature, legal cases, regulatory guidance, and domain-specific sources.

* Include perspectives from affected communities or advocacy groups where available.

* Document not just *facts*, but also *assumptions* and *gaps* in the historical record.

**Why**

* Establishes a foundation for all later analysis.
* Prevents the team from treating questions as abstract “bias checks” — they’re grounded in lived histories.
* Strengthens credibility with regulators, community stakeholders, and internal decision-makers.


## VI. Questionnaire

### Step 1 — Domain & Application Context

**What to do**

* Describe the domain (e.g., lending, hiring) and the specific product feature (e.g., installment approval).

* Identify your user groups, both direct users and anyone affected by the system’s outcomes, even if they don’t interact with it directly (e.g., job applicants using the platform, plus qualified candidates who never applied because of biased outreach).

* Document past policies or practices that harmed these groups (e.g., redlining, segregation policies, discriminatory hiring rules).

* Research and document past policies or practices that disproportionately harmed people at the intersections of these groups (e.g., Black women).

**Why**
* Past patterns of discrimination often repeat themselves when new technologies are introduced.
* This helps you avoid “reinventing” old injustices in a modern, automated form.

**Evidence to collect**
* Quick sources (news, reports, internal audits); Deep sources (studies, policy papers).



### Step 2 — Data & Representation Analysis

**What to do**

* Inventory all data sources — who collected them, when, how, and for what purpose (e.g., government survey vs. marketing database).

* Spot proxy variables — features that indirectly encode sensitive attributes (e.g., ZIP → race, job title → gender).

* Find “data deserts” — not just single groups (e.g., women), but intersections (e.g., older women in rural areas, Black women in tech roles) that are missing or sparse.

* Check encoding methods — how categories (like race or income brackets) are numerically represented; are they oversimplified or distorted?

**Why**
Your data isn’t just numbers — it encodes historical choices and blind spots. If entire intersections (e.g., disabled immigrants) are missing, your model will underperform where risks are highest. Filling these gaps improves accuracy, strengthens compliance, and protects reputation.

### Step 3 — Technology Transition & Feedback Loops

**What to do**

* Identify what system existed before — was it a manual decision process, a rules-based engine, or no system at all?

* Trace data inheritance — Note whether the training data comes from past systems that may have baked-in biases.

* Sketch feedback loops — could incorrect or biased predictions lead to self-reinforcing patterns (e.g., fewer approvals → less data → more biased training)?

**Why**
* Automation can lock in biases via repeated reinforcement.


## VII. Risk Classification Matrix

**Purpose:** prioritize where to act first. You will score each identified historical pattern across three dimensions:

1. **Severity (S)** — How harmful would this be if it happens?

   * 1 = Low (Creates differential experiences but with limited material impact)
   * 2 = Medium (Creates significant disparities in opportunities or resources)
   * 3 = High (Directly impacts fundamental rights or life outcomes.)

2. **Likelihood (L)** — How likely is this bias to occur given your system/data?

   * 1 = Low (Pattern rarely appears in similar systems)
   * 2 = Medium (Pattern occasionally appears in similar systems)
   * 3 = High (Pattern frequently appears in similar systems)

3. **Relevance (R)** — How tied is the harm to your product’s core functionality?

   * 1 = Low (Limited applicability but potential for manifestation)
   * 2 = Medium (Partial applicability to certain system components)
   * 3 = High (Direct applicability to system's domain/purpose)

**Score calculation:** `Score = S + L + R` (range 1-9).
**Priority bins (example for 1–3 scale):**

* Score 8–9 → 🔴 Critical — immediate action required
* Score 6–7 → 🟠 High — plan remediation before deployment
* Score 4–5 → 🟡 Medium — monitor and include in near-term roadmap
* Score 3 → ⚪ Low — document, monitor over time

**How to score in practice:**

* For each identified pattern (e.g., “ZIP code proxy for race”), the team discusses and assigns S, L, R.
* Document the rationale in 1–2 sentences.
* Use the matrix to pick the top items to feed into the Fairness Definition Selection AND the mitigation plan.

**Example entry:**

* Pattern: ZIP code as proxy for race

  * Severity = 3 (can lead to systemic exclusion)
  * Likelihood = 3 (ZIP present in dataset; historical redlining evidence)
  * Relevance = 3 (used directly in model features)
  * Score = 9 → 🔴 Critical
  * Rationale: “ZIP present in training set, known redlining history, used in decision logic.”

## VIII. How to write the Historical Context Report (deliverables)

Include these sections (short & focused):

1. Executive summary (1 paragraph)
2. Domain & application summary
3. Key historical harms (bullet list + links)
4. Data provenance & proxies (table)
5. Technology & feedback loop notes (diagram)
6. Risk Matrix (scores + the 3 highest priority items)
7. Appendix: raw questionnaire answers, assumptions and source links

**Deliver the report** to the fairness lead, product manager, and the VP or governance body.

## IX. Validation checklist

Use this checklist to confirm that your **Historical Context Assessment (HCA)** work is thorough and ready to hand off. Tick and add short notes as you go.

* [ ] **Research:** Research has been conducted and documented to establish the historical, social, and regulatory context of the domain.

  * Did we gather **background knowledge** on the domain, including historical, social, and regulatory context?
  * Did we collect **references** from academic literature, policy papers, legal cases, and community perspectives?
  * Did we document any **knowledge gaps** or **assumptions** that need follow-up?

* [ ] **Scope defined:** Domain, product function, and stakeholders are clearly listed.

  * Did we capture **who is directly and indirectly affected** by the system?
  * Did we review **different time periods** to understand how harms evolved?
  * Did we address **intersectional considerations** rather than treating protected attributes in isolation? 

* [ ] **Data inventory** recorded with origins, coverage, and any known gaps or biases.

  * Can new team members and auditors easily see what this data covers and what it misses?
  * Did we **document assumptions** about what the data represents (and what it doesn’t)?

* [ ] **Proxy list** created — variables that may indirectly encode sensitive attributes.

  * Did we **consider multiple kinds of bias**, including **intersectional ones** (e.g., race × gender × class)?
  * Are there variables that **behave differently across groups**?

* [ ] **Data deserts** identified — groups with little or no representation.

  * Did we calculate coverage metrics across groups and their intersections? (e.g., % representation compared to population benchmarks)
  * Did we verify with domain experts or community members that the dataset reflects their lived reality?

* [ ] **Feedback loops** mapped — how model outputs could affect future data.

  * Did we record **what system existed before** and how its biases may persist in training data?
  * Did we sketch out all points **where model outputs influence future data**?

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

* If **at least 80% of items are complete** and every 🔴 **Critical risk** has an owner (or mitigation plan if you’re working alone), you can move forward to the next step.

* If not, pause and fill the gaps before starting the next component.

## X. Quick practical tips

* If you must choose, prioritize **evidence-backed risks** (documented history + present proxy in data) over theoretical ones.
* Keep language simple in the report, VPs prefer short, actionable summaries.
* Save everything: justification for scores is vital for audits and for explaining your choices later.
---

### ⏭️ Next Action: Proceed to [**Fairness Definition Selection component**](./2-fairness-definition-selection.md)

