# **Pre-Processing Fairness Toolkit**

## I. Introduction

This step helps your team tackle **bias at its source: the data itself**. Instead of patching fairness during or after model training, pre-processing interventions reshape the dataset so that downstream models start from a fairer foundation.

### Why we do it

* To correct **representation gaps** before they distort learning.
* To mitigate **proxy discrimination** by transforming biased features.
* To counter **label bias** introduced by historical or annotator prejudice.
* To preserve **information balance** — fairness gains without losing predictive power.
* To provide **transparent, auditable steps** for regulators and stakeholders.
> _💡: If you want to learn more about pre-processing techniques for fairness so that you take the most out of this component, go ahead and read the notes available on [**the appendix**](./7-appendix.md)_.

### Outcome (what you should produce)

* A **Pre-Processing Fairness Report** (3–5 pages) containing:

  1. Data profiling answers (with rationale).
  2. Technique catalog tailored to project.
  3. Selection decision tree (bias pattern → technique).
  4. Configuration guidelines (parameter choices, tuning).
  5. Evaluation framework (fairness, information, cost).
  6. Documentation of assumptions + *why choices were made*.

### How to Navigate This Component

This guide is detailed, but you don’t have to read it linearly.

* **New to this process?** Start at **[Section II: Estimated time & effort](#ii-estimated-time--effort)** as you need some context.
* **Returning user?** Jump straight to **[Section IV: Data Profiling](#iv-data-profiling)** if you’re updating or validating work.
* Earlier sections (I–III) set the stage, while later ones (IV–XI) cover integration, validation, and practical tips.

### Quick Links
1. [Introduction](#i-introduction)
2. [Estimated time & effort](#ii-estimated-time--effort)
3. [Who Should Do This](#iii-who-should-do-this)
4. [Data Profiling](#iv-data-profiling)
5. [Core Techniques Catalog](#v-core-techniques-catalog)
6. [Technique Selection Decision Tree](#vi-technique-selection-decision-tree)
7. [Implementation Pattern Catalog](#vii-implementation-pattern-catalog)
8. [Configuration Guidelines](#viii-configuration-guidelines)
9. [Integration & Verification Framework](#ix-integration--verification-framework)
10. [Deliverables](x-deliverables)
11. [Validation checklist](#xi-validation-checklist)

## II. Estimated Time & Effort

* **Small/simple project:** 1–2 days (2 people).
* **Medium project:** 3–4 days (3–5 people).
* **Large/regulated project:** 1–2 weeks (cross-functional team).

Breakdown (medium project):

* Data audit review: 3–4 hours.
* Technique catalog adaptation: 4–5 hours.
* Decision tree drafting: 3 hours.
* Configuration + tuning experiments: 6–8 hours.
* Evaluation runs + metrics: 6 hours.
* Report drafting: 3–4 hours.

## III. Who Should Do This

**Minimum core team:**

* **Fairness lead / Audit owner** — coordinates, documents decisions.
* **Data scientist / ML engineer** — implements weighting, transformation, or generation.
* **Domain expert** — validates which interventions are realistic.
* **Compliance/legal** — ensures interventions meet policy requirements.

**Optional / helpful:**

* **UX / stakeholder rep** — ensures transformed data still reflects lived realities.
* **Ethics or social scientist** — highlights systemic mediators (e.g., wage disparities).

## IV. Data Profiling

Before selecting interventions, teams must **profile the dataset** and record not only findings but also *why they matter*. You can use Python libraries like `ydata_profiling` to get more insights quickly.

### **1. Multidimensional Representation Analysis**

* Are groups represented in realistic proportions compared to the reference population?

  * _Example: Dataset has 70% Group A, 30% Group B, while the real population is 50/50_.

* Do intersectional groups appear in sufficient proportions?

  * _Example: Women from racial minorities make up 3% of data but should be \~10%_.

* Are outcomes distributed fairly across groups?

  * _Example: Approval rates are 80% for men vs. 55% for equally qualified women_.

Check the code below to help you quickly audit your dataset for group representation, intersectional balance, and fairness in outcomes:
```python
import pandas as pd

def fairness_checks(data, protected_attrs, outcome_col, reference_dist):
    
    # 1. Group representation vs reference
    print("\n--- Group Representation vs Reference ---")
    for attr in protected_attrs:
        actual_dist = data[attr].value_counts(normalize=True).to_dict()
        print(f"\nAttribute: {attr}")
        for group, ref_prop in reference_dist.get(attr, {}).items():
            actual_prop = actual_dist.get(group, 0)
            print(f"  {group}: dataset={actual_prop:.2f}, reference={ref_prop:.2f}")

    # 2. Intersectional group representation
    print("\n--- Intersectional Group Representation ---")
    inter_counts = data.groupby(protected_attrs).size() / len(data)
    print(inter_counts)

    # 3. Outcome fairness across groups
    print("\n--- Outcome Fairness Across Groups ---")
    for attr in protected_attrs:
        rates = data.groupby(attr)[outcome_col].mean()
        print(f"\nApproval rates by {attr}:")
        print(rates)
```

### **2. Correlation Pattern Detection**

* Does a protected attribute directly correlate with the outcome?

  * _Example: Gender has correlation of 0.3 with hiring decision_.

* Are there features in the dataset that act as proxies for protected attributes?

  * _Example: ZIP code strongly predicts race due to housing segregation_.

Check the code below to help you quickly detect correlations:
```python
def fairness_correlation_checks(data, protected, outcome, features):
    results = {}

    # 1. Does a protected attribute directly correlate with the outcome?
    corr = data[protected].astype("category").cat.codes.corr(data[outcome])
    results["protected_vs_outcome_correlation"] = corr

    # 2. Do any features act as proxies for the protected attribute?
    proxy_corrs = {}
    for feat in features:
        proxy_corrs[feat] = data[protected].astype("category").cat.codes.corr(data[feat])
    results["proxy_feature_correlations"] = proxy_corrs

    return results
```

### **3. Label Quality Evaluation**

* Do historical policies or systemic discrimination affect the labels?

  * _Example: “Promoted” labels come from a period when women were systematically overlooked_.

* Could annotator bias have influenced labels?

  * _Example: Resume screening annotations rate identical resumes lower when they have female names_.

👉 For every finding, **document WHY you consider it problematic** (e.g., “Low representation of women of color risks blind spots in model learning”).

## V. Core Techniques Catalog
This section highlights the different techniques you can use for fairness-aware data balancing. If you’re already familiar with these methods, you can skip ahead to **[Technique Selection  Selection Decision Tree](#vi-technique-selection-decision-tree)** to see how to choose between them.

### **1. Reweighting & Resampling**

* **Goal:** Make training data *count* differently (reweighting) or *look* different (resampling) so the model learns fairer patterns.

* **When to prefer which:** Use **reweighting** when your training framework accepts `sample_weight` (no data duplication, more precise). Use **resampling** (oversample/SMOTE/undersample) when your framework lacks weight support or you need an explicitly balanced dataset. 

* **What we’ll do:** we’ll make the model “pay more attention” to underrepresented people (reweighting) or create a balanced training set (resampling) so it learns fairer decisions.

* **Why it’s safe:** we’ll start conservatively (small weights, limited synthetic examples), validate on realistic business metrics (accuracy, calibration) and fairness metrics, and document every choice and why we made it.

* **What to expect:** improved fairness measures (e.g., selection rate gaps shrink), small drops in raw accuracy are possible but often small; if we see large drops we’ll roll back and try gentler settings.

Here’s a list of the different techniques available:

1. **Group-based reweighting**

   * **What it does:** Gives more weight to smaller groups.
   * **Example:** If women = 20% and men = 80%, women’s samples get higher weight.
   * **Pros:** Keeps all data, simple to apply.
   * **Cons:** Can cause instability if weights get too large.

2. **Outcome-aware reweighting**

   * **What it does:** Focuses on rare outcomes within groups.
   * **Example:** Minority applicants who get hired = rare → upweight those cases.
   * **Pros:** Ensures rare successes aren’t ignored.
   * **Cons:** Noisy if positives are extremely few.

3. **Conditional reweighting**

   * **What it does:** Adjusts weights inside subgroups (e.g., income brackets).
   * **Example:** High-income women vs. high-income men.
   * **Pros:** Very precise targeting.
   * **Cons:** Can fail if subgroups are too small.

4. **Undersampling**

   * **What it does:** Drops some samples from the majority group.
   * **Example:** Randomly remove some men’s resumes if men dominate the dataset.
   * **Pros:** Easy, reduces training size.
   * **Cons:** Throws away data, can reduce accuracy.

5. **Oversampling (naïve)**

   * **What it does:** Simply duplicates minority samples.
   * **Example:** Copy women’s resumes multiple times until numbers match men.
   * **Pros:** Very simple, no special tools needed.
   * **Cons:** Overfitting risk (same data repeated).

6. **SMOTE (Synthetic Minority Oversampling Technique)**

   * **What it does:** Creates new synthetic minority examples by interpolation.
   * **Example:** Generate “in-between” resumes for women applicants.
   * **Pros:** More variety than duplication, less overfitting.
   * **Cons:** May create unrealistic samples if features are tricky.

7. **Preferential sampling**

   * **What it does:** Selects and duplicates samples based on usefulness.
   * **Example:** Keep resumes that are on the “decision boundary” (uncertain).
   * **Pros:** Smarter than random oversampling.
   * **Cons:** Needs a way to measure which samples are “useful.”

8. **Progressive weighting**

   * **What it does:** Gradually increases sample weights instead of all at once.
   * **Example:** Slowly give more weight to women’s resumes over training runs.
   * **Pros:** Smooth, less risk of instability.
   * **Cons:** Requires multiple training rounds.

### **2. Label Correction**

**Why we do it**
Sometimes the *labels themselves*, the “ground truth” our model learns from, are **already biased**. If past hiring decisions, loan approvals, or promotions were discriminatory, the model will faithfully reproduce those unfair patterns. Label correction steps in to “clean up the answers” before training, so the model doesn’t inherit past injustices.

**Example**
In hiring data, equally qualified women were often labeled “reject” while men were labeled “hire.” If trained on this directly, the model learns *“reject women.”* Label correction changes some of those unfair rejections to “hire,” reflecting what should have happened in an unbiased process.

**Main Approaches**

1. **Label Massaging**

   * *What it does*: Flips a small number of labels to rebalance outcomes (e.g., some “rejects” for women become “accepts”).
   * *When to use*: If bias clearly caused certain groups to be unfairly rejected.
   * *Pros*: Simple, direct.
   * *Cons*: If overdone, it can distort reality. Needs careful tuning.

2. **Prejudice Removal (Kamiran & Calders, 2012)**

   * *What it does*: Adjusts labels so they don’t strongly correlate with protected attributes.
   * *When to use*: If your data shows a clear correlation like “being female → lower chance of hire.”
   * *Pros*: Specifically targets label bias.
   * *Cons*: Can hurt accuracy if pushed too far. Needs alignment with fairness metrics.

3. **Expert Relabeling**

   * *What it does*: Domain experts review questionable cases and correct labels manually.
   * *When to use*: When fairness decisions require deep context (e.g., medicine, law).
   * *Pros*: Credible, grounded in expertise.
   * *Cons*: Expensive, subjective, doesn’t scale easily.

4. **Label Smoothing / Probabilistic Correction**

   * *What it does*: Instead of flipping labels, assign probabilities (e.g., “70% hire, 30% reject”).
   * *When to use*: If labels are noisy and bias isn’t clear-cut.
   * *Pros*: Captures uncertainty, less distortion.
   * *Cons*: Hard to explain to stakeholders, needs models that handle soft labels.

5. **Counterfactual Labeling**

   * *What it does*: Creates “what if” labels by imagining protected attributes were different (e.g., would this candidate be hired if male?).
   * *When to use*: If you can model counterfactuals based on causal analysis.
   * *Pros*: Powerful for causal fairness.
   * *Cons*: Technically complex, counterfactuals may be uncertain. 


### **3. Feature Transformation**

**Why we do it**
Sometimes features in the dataset act as **proxies** for protected attributes like gender, race, or age. Even if we drop the sensitive attribute itself, the model can still “reconstruct” it indirectly from correlated features (e.g., ZIP code → race, prior salary → gender pay gap).

Imagine we’re hiring and we *don’t* give the model “gender” — but we *do* give it “years without a career break” and “ZIP code.” The model can still guess gender and race through those signals. Feature transformation is like **“translating” the features** into a fairer form: we keep useful information (skills, experience, effort) while removing unfair clues that let the model discriminate.

**Main Approaches**

1. **Disparate Impact Removal** (Feldman, 2015)

   * *What it does*: Aligns feature distributions across groups while preserving the relative ordering within each group.
   * *When to use*: If a **single feature** is strongly correlated with a protected attribute (e.g., income, distance to branch).
   * *Pros*: Simple, quick to apply; keeps within-group ranking intact.
   * *Cons*: Can reduce accuracy if the feature also carries real predictive signal.

2. **Optimal Transport** (Jiang, 2020)

   * *What it does*: “Shifts” features optimally so group distributions are closer, minimizing distortion.
   * *When to use*: For **complex or multi-dimensional features** (e.g., many correlated variables).
   * *Pros*: Flexible, fine-grained control of fairness–utility trade-off.
   * *Cons*: Computationally heavy; harder to scale on large datasets.

3. **Fair Representations** (Zemel, 2013; Madras, 2018)

   * *What it does*: Learns new latent features (embeddings) where protected attributes are hidden but task-related info remains.
   * *When to use*: With **nonlinear, high-dimensional data** (resumes, text, images).
   * *Pros*: Handles multiple bias types at once; great for unstructured data.
   * *Cons*: Less interpretable; needs tuning, often adversarial training.

4. **Intersectional Fairness Transformations** (Foulds, 2020)

   * *What it does*: Applies fairness corrections across intersections of attributes (e.g., Black women, Latino men).
   * *When to use*: If disparities appear **at intersections** rather than single groups.
   * *Pros*: Protects overlapping subgroups; reduces hidden unfairness.
   * *Cons*: More complex; risk of over-correction with small datasets.

### **4. Data Generation**
Sometimes the dataset is too biased or too small. In those cases, we can **create new data** — either by generating synthetic samples or by augmenting the existing ones — so that the model sees more balanced and fair examples.

**Example**
If women in leadership roles are rare in the dataset, the model may never learn they can exist. Data generation can create additional “women-in-leadership” examples so the model doesn’t assume only men belong there.

**Main Approaches**

1. **Fair Synthetic Data (Causal / Counterfactual)**

   * *What it does*: Builds a whole new dataset that keeps useful patterns but enforces fairness constraints.
   * *When to use*: If real data is heavily biased, sparse, or private.
   * *Pros*: Very powerful, can remove multiple bias sources; also supports privacy.
   * *Cons*: Needs compute + expertise; risk of unrealistic or “fake-looking” data.
   * *Examples*: DECAF, FairCauseSyn, FairTabGen.

2. **Conditional Generation**

   * *What it does*: Generate extra data **for specific groups** (e.g., more women with high incomes).
   * *When to use*: If some groups are very underrepresented.
   * *Pros*: Strong control over representation.
   * *Cons*: Can distort overall distribution if overused.

3. **Counterfactual Augmentation**

   * *What it does*: For each record, create a “what if” version where only the protected attribute changes.
   * *When to use*: If you want **counterfactual fairness** — e.g., “same person, different gender.”
   * *Pros*: Encourages fair, individual-level reasoning.
   * *Cons*: Risk of generating unrealistic counterfactuals; requires causal knowledge.

4. **Heuristic / Perturbation-Based**

   * *What it does*: Make small tweaks to existing data (e.g., change a name, edit text, swap a category).
   * *When to use*: For small or structured datasets, or when you can define clear rules.
   * *Pros*: Simple, cheap, and quick.
   * *Cons*: Limited coverage; risk of unrealistic edits or hidden bias.

5. **Mixing Synthetic + Real Data**

   * *What it does*: Blend generated data with the original dataset.
   * *When to use*: To balance realism (from real data) and fairness (from synthetic).
   * *Pros*: Improves generalization, avoids over-reliance on synthetic.
   * *Cons*: Needs careful ratio — too much synthetic can bias results.

6. **Prompt-Based / Guided Generation**

   * *What it does*: Use prompts with LLMs or generators to produce fair, balanced synthetic examples.
   * *When to use*: Low-data domains or when you want fine-grained control with prompts.
   * *Pros*: Flexible; easy to adapt with recent LLM advances.
   * *Cons*: Quality depends on prompt design; bias in prompts can leak through.

**What it means to generate data fairly**
We’re not making data out of thin air. We’re trying to approximate **what reality would look like if bias hadn’t skewed the records** — filling in the missing pieces for groups and outcomes that were historically ignored.

**Risks to watch out for**

* Unrealistic or implausible examples.
* Synthetic overwhelming real data.
* Harder to justify to regulators (“where did the data come from?”).

## VI. Technique Selection Decision Tree
This decision tree helps you choose the most appropriate **pre-processing intervention method** among the four main techniques discussed above.

**Step 1 — Start with Data Profiling Findings**

* Representation gaps? → Go to **Reweighting/Resampling**.
* Outcome disparities tied to group membership? → Go to **Reweighting/Resampling**.
* Labels seem biased (historical or annotator)? → Go to **Label Correction**.
* Features strongly correlated with protected attributes (proxies)? → Go to **Feature Transformation**.
* Severe underrepresentation, sparse subgroups, or privacy restrictions? → Go to **Data Generation**.

**Step 2 — Within Each Technique**

### **A. Reweighting & Resampling**

* **Does your ML framework support `sample_weight`?**

  * Yes → **Reweighting**.
  * No → **Resampling**.

* **If Reweighting:**

  * Simple group imbalance → **Group-based weighting**.
  * Rare positive examples missing (e.g., women approved for loans) → **Outcome-aware weighting**.
  * Bias only inside certain strata (e.g., high-income women) → **Conditional weighting**.
  * Concern about instability? → Start with **Progressive weighting**.

* **If Resampling:**

  * Too many majority examples → **Undersampling**.
  * Need more minority examples → **Oversampling (naïve)**.
  * Minority has few but continuous features → **SMOTE**.
  * You can rank examples by usefulness → **Preferential sampling**.

### **B. Label Correction**

* **Do labels reflect historical bias?** (e.g., women consistently rejected despite equal skills)

  * Yes → **Label Massaging** (simple) or **Prejudice Removal** (metric-driven).
* **Do you have domain experts to validate labels?**

  * Yes → **Expert Relabeling**.
* **Are labels noisy/uncertain?**

  * Yes → **Label Smoothing / Probabilistic correction**.
* **Do you have a causal model for counterfactuals?**

  * Yes → **Counterfactual Labeling**.

### **C. Feature Transformation**

* **Is bias concentrated in one or two features?**

  * Yes → **Disparate Impact Removal**.
* **Is bias spread across many features or high-dimensional data?**

  * Yes → **Optimal Transport** (tabular) or **Fair Representations** (unstructured/complex).
* **Do intersectional groups show specific bias?**

  * Yes → **Intersectional fairness transformations**.

### **D. Data Generation**

* **Is group representation very low (<5%) or data sparse?**

  * Yes → **Conditional Generation**.

* **Do you want the model to learn “what if” fairness at individual level?**

  * Yes → **Counterfactual Data Augmentation**.

* **Do you want entirely new but fair data, respecting causal links?**

  * Yes → **Causal/Synthetic Data Generation frameworks (GAN/LLM-based)**.

* **Do you need a quick, lightweight boost?**

  * Yes → **Heuristic/Perturbation Augmentation**.

* **Worried about synthetic artifacts?**

  * Mix **Synthetic + Real data** carefully.

> 👉 **Practitioner Reminder**: At every branching step, document not just *what* you chose but also *why*.



## VII. Implementation Pattern Catalog
his catalog provides **code templates and pseudocode** for implementing common fairness data pre-processing techniques.
Each snippet is intentionally lightweight, easily adaptable, and includes comments describing **where to customize** for your use case (loan approval, hiring, etc.).

### **1. Reweighting & Resampling**

```python
import numpy as np
import pandas as pd
from sklearn.utils import resample
from imblearn.over_sampling import SMOTE

# --- Group-based reweighting ---
def compute_group_weights(data, protected_attr):
    """Compute inverse frequency weights per group"""
    group_counts = data[protected_attr].value_counts()
    total = len(data)
    weights = {g: total / (len(group_counts) * count) for g, count in group_counts.items()}
    return data[protected_attr].map(weights)

# Example usage:
# data['sample_weight'] = compute_group_weights(data, 'gender')


# --- Resampling (undersampling / oversampling) ---
def balance_dataset(data, target_col, protected_attr, method="oversample"):
    """Balance dataset across groups via over/under-sampling"""
    groups = []
    min_size = data[target_col].value_counts().min()

    for g, df_g in data.groupby(protected_attr):
        if method == "undersample":
            groups.append(resample(df_g, replace=False, n_samples=min_size))
        elif method == "oversample":
            groups.append(resample(df_g, replace=True, n_samples=min_size))
    return pd.concat(groups)

# Example usage:
# balanced = balance_dataset(data, target_col='approved', protected_attr='gender')


# --- SMOTE example ---
def apply_smote(X, y):
    smote = SMOTE(sampling_strategy='auto', random_state=42)
    X_res, y_res = smote.fit_resample(X, y)
    return X_res, y_res

# Example usage:
# X_bal, y_bal = apply_smote(X_train, y_train)
```

### **2. Label Correction**

```python
import numpy as np

# --- Label Massaging ---
def label_massaging(data, label_col, protected_attr, flip_fraction=0.05):
    """
    Flip a small percentage of negative labels for disadvantaged group
    """
    group_mask = (data[protected_attr] == "female") & (data[label_col] == 0)
    n_flip = int(flip_fraction * group_mask.sum())
    flip_indices = np.random.choice(data[group_mask].index, n_flip, replace=False)
    data.loc[flip_indices, label_col] = 1
    return data

# Example usage:
# data = label_massaging(data, 'approved', 'gender')


# --- Label Smoothing / Probabilistic Correction ---
def soft_labels(y, smoothing=0.1):
    """Apply probabilistic correction to reduce bias from hard labels"""
    n_classes = len(np.unique(y))
    y_smoothed = (1 - smoothing) * np.eye(n_classes)[y] + (smoothing / n_classes)
    return y_smoothed

# Example usage:
# y_train_soft = soft_labels(y_train, smoothing=0.1)
```

### **3. Feature Transformation**

```python
import pandas as pd
import numpy as np
from sklearn.preprocessing import QuantileTransformer

# --- Disparate Impact Removal ---
def disparate_impact_removal(df, feature, protected_attr):
    """
    Equalize feature distribution across groups
    """
    fair_df = df.copy()
    qt = QuantileTransformer(output_distribution="uniform")

    for group in df[protected_attr].unique():
        mask = df[protected_attr] == group
        fair_df.loc[mask, feature] = qt.fit_transform(df.loc[mask, [feature]])
    return fair_df

# Example usage:
# data = disparate_impact_removal(data, 'income', 'gender')


# --- Fair Representations (simplified placeholder) ---
def learn_fair_representation(X, protected_attr):
    """
    Pseudocode for training a fair representation encoder
    """
    # 1. Train encoder E to minimize reconstruction loss
    # 2. Train adversary D to predict protected_attr from E(X)
    # 3. Optimize E to fool D (minimize adversary accuracy)
    pass

# (Actual implementation would use PyTorch or TensorFlow)
```

### **4. Data Generation**

```python
import numpy as np
import pandas as pd

# --- Counterfactual Augmentation ---
def generate_counterfactuals(data, protected_attr):
    """
    Create counterfactual samples by flipping protected attributes
    """
    cf_data = data.copy()
    cf_data[protected_attr] = cf_data[protected_attr].apply(lambda x: 'female' if x == 'male' else 'male')
    cf_data['is_counterfactual'] = True
    return pd.concat([data, cf_data])

# Example usage:
# data_cf = generate_counterfactuals(data, 'gender')


# --- Conditional Generation (pseudocode) ---
def conditional_data_generation(generator_model, condition):
    """
    Generate synthetic data conditioned on group or outcome
    """
    # z = random noise vector
    # synthetic_samples = generator_model.generate(z, condition)
    # return synthetic_samples
    pass


# --- Mix Real + Synthetic ---
def mix_datasets(real_df, synthetic_df, ratio=0.8):
    n_real = int(len(real_df) * ratio)
    mixed = pd.concat([real_df.sample(n_real), synthetic_df.sample(len(real_df) - n_real)])
    return mixed

# Example usage:
# mixed_df = mix_datasets(real_data, synthetic_data, ratio=0.9)
```

## VIII. Configuration Guidelines

* **Reweighting**

  * Begin with group-based weights.
  * For equal opportunity → conditional weights by outcome.
  * Cap extreme weights (≤2.5×).

* **Resampling**

  * Oversample moderately (≤2×).
  * Prefer SMOTE when features are continuous and rich.
  * Always validate calibration after resampling.

* **Disparate Impact Removal**

  * Start with repair level = 0.5.
  * Increase until fairness improves without heavy accuracy loss.
  * Keep rank within groups for sensitive features.

* **Fair Representations**

  * Train embeddings adversarially against protected attributes.
  * Tune latent dimension to preserve utility.

* **Synthetic Generation**

  * Use conditional generators (e.g., VAE, GAN) to control balance.
  * Validate samples with domain experts for realism.

**Fairness Assessment**

* Compute chosen metrics (e.g., equal opportunity, demographic parity).
* Test intersectional fairness (gender × age).
* Use significance tests to confirm improvements.

**Information Preservation**

* Compare accuracy, AUC, F1 before/after.
* Check if calibration holds across groups.
* Track feature importance stability.

**Computational Cost**

* Record additional runtime/memory.
* Test scaling to larger data.
* Evaluate impact on training + inference speed.


## IX. Integration & Verification Framework

Preprocessing techniques aim to make the **training data itself fairer** before modeling begins. To ensure these interventions are reliable and beneficial, we must systematically integrate and verify their impact — both on **data distribution** and **model outcomes**. This framework ensures changes made at the data level genuinely enhance fairness without distorting learning or utility.

### **Verification Protocol**

1. **Baseline Assessment**

   * Train model on **raw data** (no preprocessing).
   * Record baseline metrics:

     * Accuracy, Precision/Recall, AUC.
     * Fairness metrics (e.g., Demographic Parity Difference, Equal Opportunity Difference).
     * Distribution balance (group counts, positive outcome rates).

2. **Apply Preprocessing Method**

   * Implement selected technique (e.g., reweighting, label correction, feature transformation, or data generation).
   * Retrain model using the modified dataset.

3. **Post-Processing Evaluation**

   * Record metrics again and **compare to baseline**:

     * Δ Fairness metrics (improvement).
     * Δ Performance metrics (accuracy change).
     * Stability of distribution (e.g., no over-sampling artifacts).

4. **Robustness Checks**

   * Test model on **unseen hold-out sets**.
   * Perform **sensitivity analysis** (e.g., small perturbations in weights or sample ratios).
   * Assess **generalization** on slightly shifted data (simulated drift).

### **Success Criteria**

✅ Fairness metric improves by **≥10%** (smaller gaps in selection or opportunity rates).
✅ Accuracy decline **≤5%** (if larger, re-tune preprocessing intensity).
✅ Distribution remains realistic (no synthetic domination or instability).
✅ Results consistent across multiple seeds/runs.

### ⚠️ **Intersectional Validation Reminder**

Always validate fairness not only across **single protected groups** but also **intersections** (e.g., *low-income women*, *older immigrants*). Preprocessing can unintentionally fix bias for one group while worsening it for another intersection — this must be explicitly monitored.

## X. Deliverables
1. Executive summary.
2. Data auditing results.
3. Technique catalog (tailored to project).
4. Decision tree with rationale.
5. Configuration notes (parameters, settings).
6. Evaluation results.
7. Limitations + open risks.

## XI. Validation Checklist

* [ ] Did we answer all profiling questions clearly?
* [ ] Did we check representation at both **group** and **intersectional** levels?
* [ ] Did we identify correlations and potential proxies?
* [ ] Did we confirm whether representation, proxy, or label bias exists?
* [ ] Did we test whether bias appears stronger at **intersections** (e.g., race × gender)?
* [ ] Did we document why each bias matters for the project context?
* [ ] Did we use the decision tree to justify our chosen interventions?
* [ ] Did we consider whether interventions protect **intersectional groups**, not just single attributes?
* [ ] Did we note alternatives we considered but did not pick?
* [ ] Did we tune parameters carefully (weights, resampling ratios, repair levels)?
* [ ] Did we cap extremes to avoid instability?
* [ ] Did we record why we set parameters as we did?
* [ ] Did we compute fairness metrics at both **group** and **intersectional** levels?
* [ ] Did we validate accuracy, calibration, and utility after interventions?
* [ ] Did we measure computational cost and runtime trade-offs?
* [ ] Did we write down assumptions and limitations?
* [ ] Did we highlight any **intersectional risks** explicitly in the report?
* [ ] Did we complete the **Pre-Processing Fairness Report**?

**Pass / Fail rule:**

* If **at least 80% of items are complete**, you can move forward to the next step.

* If not, pause and fill the gaps before starting the next component.

---

### ⏭️ Next Action: Proceed to [**In-processing Toolkit**](./3-in-processing-toolkit.md)