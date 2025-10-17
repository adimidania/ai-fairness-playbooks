# In-Processing Fairness Toolkit
## I. Introduction

This toolkit provides a structured approach for embedding fairness constraints **directly into the training process** of machine learning models. Unlike pre-processing, which modifies input data, or post-processing, which adjusts outputs after training, **in-processing intervenes in the optimization loop itself**.

### Why we do it

* Diagnose model compatibility with fairness methods.
* Select appropriate techniques aligned with fairness definitions (e.g., demographic parity, equal opportunity).
* Implement fairness constraints within training pipelines.
* Evaluate trade-offs between predictive accuracy and fairness.
* Deliver reproducible, auditable workflows for production deployment.

> _💡: If you want to learn more about casual fairness so that you take the most out of this component, go ahead and read the notes available on [**the appendix**](./7-appendix.md)_.

### Outcome (what you should produce)

* A short **In-Processing Report** (2–4 pages) containing:

  1. Variable map (protected attributes, proxies, mediators, outcomes).
  2. Preliminary causal diagram(s).
  3. Counterfactual query examples + results.
  4. Pathway classification (fair vs. unfair).
  5. Intervention recommendations (pre-, in-, post-processing).
  6. Documentation of assumptions + uncertainties.

### How to Navigate This Component

This guide is detailed, but you don’t have to read it linearly.

* **New to this process?** Start at **[Section II: Estimated time & effort](#ii-estimated-time--effort)** as you need some context.
* **Returning user?** Jump straight to **[Section IV: Model Architecture Analysis](#iv-model-architecture-analysis)** if you’re updating or validating work.
* Earlier sections (I–III) set the stage, while later ones (IV–XI) cover integration, validation, and practical tips.

### Quick Links
1. [Introduction](#i-introduction)
2. [Estimated time & effort](#ii-estimated-time--effort)
3. [Who Should Do This](#iii-who-should-do-this)
4. [Model Architecture Analysis](#iv-model-architecture-analysis)
5. [Core Techniques Catalog](#v-core-techniques-catalog)
6. [Technique Selection Decision Tree](#vi-technique-selection-decision-tree)
7. [Implementation Pattern Catalog](#vii-implementation-pattern-catalog)
8. [Integration & Verification Framework](#ix-integration--verification-framework)
9. [Deliverables](ix-deliverables)
10. [Validation checklist](#x-validation-checklist)

## II. Estimated Time & Effort

* **Small/simple project:** 4–6 hours (2 people).
* **Medium project:** 1–2 days (3–4 people).
* **Large/regulated project:** 3–5 days (cross-functional team).

Breakdown (medium project):

* Model architecture & goal review: 2–3 hours.
* Fairness method selection: 2–4 hours.
* Implementation & training: 4–6 hours.
* Validation & robustness testing: 2–3 hours.
* Reporting & documentation: 2 hours.

## III. Who Should Do This

* **ML Engineers / Data Scientists**: Implement fairness-aware training and optimize hyperparameters.
* **Fairness / Ethics Specialists**: Define fairness goals and interpret trade-offs.
* **Domain Experts / Business Stakeholders**: Prioritize fairness-performance balance.
* **Project Manager** (large projects): Coordinate roles and timelines.

## IV. Model Architecture Analysis
Before getting into the different techniques we have for model intervention, take some time to analyze the model you are working with. This step is necessary before moving into the intervention stage because **the type of model, its characteristics, and the technical constraints around it will directly determine which fairness methods are feasible, efficient, and legally acceptable**. Skipping this step often leads to wasted effort, unstable implementations, or methods that cannot be deployed in practice.

**Step 1 — Model Family**

* Linear models (e.g., logistic regression, linear SVM).
* Tree-based models (e.g., decision trees, random forests, gradient boosting).
* Neural networks (e.g., feedforward, CNN, RNN, transformers).
* Other (custom / ensemble models).

**Step 2 — Model Characteristics**

* Training approach (batch vs online).
* Loss function (cross-entropy, hinge, MSE, etc.).
* Current regularization (L1, L2, dropout).
* Hyperparameter tuning approach (grid search, Bayesian).

**Step 3 — Technical Constraints**

* Compute budget (GPU/CPU hours available).
* Acceptable training time increase (e.g., <50%).
* Explainability needs (linear models vs black-box).
* Deployment limitations (real-time inference, memory).

Perfect, habibti ✨ Here’s the same structure, but with a short, simple explainer paragraph at the top sssssss

## V. Core Techniques Catalog
### 1. Fairness Constraints Optimization

This method works by **directly embedding fairness goals into the model’s training process**. Instead of training only to minimize prediction error, the model is also asked to respect mathematical fairness rules (like equal approval rates or equal true positive rates across groups). Think of it as teaching the model to “care” about both accuracy and fairness at the same time. It’s most effective when the model is simple enough (like linear models) and when you need strict guarantees that fairness conditions are met.

**When to Use:**

* Linear or convex models (logistic regression, SVM)
* Need theoretical guarantees
* Regulatory compliance requirements

**Trade-offs:**

* ✅ Strong theoretical foundations
* ✅ Guaranteed fairness properties
* ❌ Limited to convex problems
* ❌ May require specialized solvers

### 2. Adversarial Debiasing

This method uses the idea of **pitting two models against each other**: the main model tries to make accurate predictions, while a second model (the adversary) tries to guess the protected attribute (like gender or race) from the hidden representations. By introducing a gradient reversal, the predictor is forced to learn representations that are **useful for the task but hide sensitive information**. In other words, it trains the model to be accurate while also making it hard to discriminate based on protected attributes. This makes it especially powerful for deep learning systems where hidden features can unintentionally carry bias.

**When to Use:**

* Neural networks and deep learning models
* Complex, non-linear relationships
* Need representational fairness

**Architecture Pattern:**

```
Main Predictor ──→ Task Output (e.g., loan approval)
       │
       ├──→ Gradient Reversal Layer
       │
       └──→ Adversary ──→ Protected Attribute Prediction
```

**Implementation Steps:**

1. **Predictor**: Base model for main task
2. **Adversary**: Predicts protected attributes from representations
3. **Gradient Reversal Layer**: Reverses gradients from adversary
4. **Joint Training**: Optimize both objectives simultaneously

**Loss Function:**

```
L_combined = L_primary - λ * L_adversarial
```

**Training Schedule:**

* Start with low λ (0.1)
* Gradually increase to 1.0
* Use progressive training for stability

**Trade-offs:**

* ✅ Powerful for complex models
* ✅ Learns fair representations
* ❌ Training can be unstable
* ❌ Requires careful hyperparameter tuning

### 3. Fairness Regularization
Fairness regularization is a technique where we add extra penalty terms to the model’s loss function to encourage fairness while still optimizing performance. Instead of enforcing fairness as a hard rule (like in constraints optimization), this approach makes fairness a soft objective that the model gradually learns to balance against accuracy. The key advantage is flexibility: you can decide how much fairness pressure to apply through hyperparameters (λ values). This makes it useful across many model types, from linear models to deep networks, and allows practitioners to experiment with different fairness-performance trade-offs.

**When to Use:**
- Need flexible fairness-performance trade-offs
- Working with various model types
- Want gradual fairness improvements

**Trade-offs:**
- ✅ Flexible and stable
- ✅ Works with many model types
- ✅ Allows fine-tuned trade-offs
- ❌ Requires hyperparameter tuning
- ❌ May not achieve perfect fairness

### 4. Multi-Objective Optimization
Multi-objective optimization is used when fairness cannot be captured by a single metric and we must balance accuracy, fairness, interpretability, and other goals simultaneously. Instead of forcing one objective (like only maximizing accuracy), this method recognizes that real-world systems involve trade-offs. The key idea is to search for solutions that are Pareto optimal—where no objective can be improved without harming another. By using weighting schemes or interactive methods with stakeholders, practitioners can explicitly decide how much importance to give to fairness versus performance, making this approach particularly valuable in regulated or high-impact domains.

**When to Use:**
- Multiple conflicting fairness goals
- Need explicit trade-off management
- Stakeholder involvement in decision-making

**Core Concepts:**

**Pareto Optimality**: No objective can be improved without degrading another

**Preference Articulation:**
- **A priori**: Set preferences before optimization
- **A posteriori**: Generate options, then choose
- **Interactive**: Iterative refinement with stakeholders

**Trade-offs:**
- ✅ Explicit trade-off management
- ✅ Stakeholder involvement
- ✅ Principled decision framework
- ❌ Computationally expensive
- ❌ Requires stakeholder engagement

As you have learned above, some methods are highly compatible with linear models, while others are better suited for complex architectures like neural networks. The table below provides a quick compatibility matrix, showing the relative strengths and limitations of each technique across model types.

| Model Type           | Constraints | Adversarial | Regularization | Multi-Objective |
| -------------------- | ----------- | ----------- | -------------- | --------------- |
| **Linear Models**    | ✅ High      | ❌ Low       | ✅ High         | ✅ Medium        |
| **Tree-based**       | ❌ Low       | ❌ Low       | ⚠️ Medium      | ⚠️ Medium       |
| **Neural Networks**  | ⚠️ Medium   | ✅ High      | ✅ High         | ✅ High          |
| **Ensemble Methods** | ❌ Low       | ❌ Low       | ✅ High         | ⚠️ Medium       |

**Where:**

* ✅ **High** suggests strong compatibility, method works well
* ⚠️ **Medium** suggests possible but with limitations
* ❌ **Low** suggests poor compatibility, not recommended

## VI. Technique Selection Decision Tree

This decision tree helps you choose the most appropriate **model intervention method** among the four main techniques: **Fairness Constraints Optimization, Adversarial Debiasing, Fairness Regularization, and Multi-Objective Optimization**.

**Step 1: Model Architecture Assessment**
What type of model are you using?

* **Linear model** → Go to Step 2A
* **Tree-based model** → Go to Step 2B
* **Neural network** → Go to Step 2C
* **Other** → Consider model-agnostic approaches

**Step 2A: Linear Model Approaches**
What is your primary fairness definition?

* **Demographic parity** → Constraint optimization with optimization-based preprocessing
* **Equal opportunity** → Constraint optimization with adjusted thresholds
* **Individual fairness** → Similarity-based regularization

**Step 2B: Tree-based Model Approaches**
What is your primary fairness definition?

* **Demographic parity** → Fair splitting criteria
* **Equal opportunity** → Fair splitting with weighted samples
* **Individual fairness** → Regularized tree induction

**Step 2C: Neural Network Approaches**
What is your primary fairness definition?

* **Demographic parity** → Adversarial debiasing
* **Equal opportunity** → Multi-task learning with fairness head
* **Individual fairness** → Gradient penalties or contrastive learning

## VII. Implementation Pattern Catalog
This catalog shows how to implement the **four main fairness intervention methods** mentioned above.

### **1. Fairness Constraints Optimization**

This method embeds fairness constraints directly into the optimization problem. The model minimizes prediction error *and* respects fairness rules (e.g., equal true positive rates).

**Code Template (using `cvxpy` for constrained optimization):**

```python
import cvxpy as cp

def train_with_constraints(X, y, protected, lambda_fair=1.0):
    """
    Logistic regression with fairness constraints using CVXPY
    """
    n, d = X.shape
    w = cp.Variable(d)
    b = cp.Variable()

    # Logistic regression loss
    logits = X @ w + b
    prediction_loss = cp.sum(cp.logistic(-cp.multiply(y, logits))) / n

    # Fairness constraint: demographic parity (example)
    mask_a0 = (protected == 0)
    mask_a1 = (protected == 1)
    dp_constraint = cp.abs(cp.sum(cp.multiply(mask_a0, logits)) - 
                           cp.sum(cp.multiply(mask_a1, logits))) / n

    # Objective = accuracy + fairness penalty
    objective = cp.Minimize(prediction_loss + lambda_fair * dp_constraint)

    problem = cp.Problem(objective)
    problem.solve()

    return w.value, b.value
```

**How to use:** Replace the `dp_constraint` with another fairness metric (equal opportunity, equalized odds) depending on requirements.

### **2. Adversarial Debiasing**

Two networks:

* **Predictor** → learns the main task
* **Adversary** → tries to guess protected attributes
  A gradient reversal layer ensures the predictor hides bias information.

**Code Template (PyTorch):**

```python
import torch
import torch.nn as nn
import torch.optim as optim

class Predictor(nn.Module):
    def __init__(self, input_dim, hidden_dim):
        super().__init__()
        self.fc = nn.Sequential(
            nn.Linear(input_dim, hidden_dim),
            nn.ReLU(),
            nn.Linear(hidden_dim, 1),
            nn.Sigmoid()
        )
    def forward(self, x):
        return self.fc(x)

class Adversary(nn.Module):
    def __init__(self, hidden_dim):
        super().__init__()
        self.fc = nn.Sequential(
            nn.Linear(hidden_dim, 1),
            nn.Sigmoid()
        )
    def forward(self, h):
        return self.fc(h)

def adversarial_training_step(predictor, adversary, X, y, protected, 
                              optimizer_pred, optimizer_adv, lambda_adv=0.5):
    """
    One training step for adversarial debiasing
    """
    # Forward pass
    pred = predictor(X)
    hidden = predictor.fc[0](X)   # use hidden representation
    adv_pred = adversary(hidden.detach())

    # Losses
    task_loss = nn.BCELoss()(pred, y)
    adv_loss = nn.BCELoss()(adv_pred, protected)

    # Combined loss for predictor
    total_loss = task_loss - lambda_adv * adv_loss

    # Update predictor
    optimizer_pred.zero_grad()
    total_loss.backward(retain_graph=True)
    optimizer_pred.step()

    # Update adversary
    optimizer_adv.zero_grad()
    adv_loss.backward()
    optimizer_adv.step()

    return task_loss.item(), adv_loss.item()
```

**How to use:** Train predictor and adversary together, increasing `lambda_adv` gradually for stability.

### **3. Fairness Regularization**

Adds fairness penalties to the loss function (soft enforcement). This gives flexibility by letting you control fairness via hyperparameters.

**Code Template:**

```python
def fairness_regularizer(predictions, protected, labels, lambda_dp=0.5, lambda_eo=0.5):
    """
    Compute fairness penalty to add to loss function
    """
    # Demographic Parity penalty
    mean_pred_a0 = predictions[protected == 0].mean()
    mean_pred_a1 = predictions[protected == 1].mean()
    dp_penalty = (mean_pred_a0 - mean_pred_a1).abs()

    # Equal Opportunity penalty
    tpr_a0 = predictions[(protected == 0) & (labels == 1)].mean()
    tpr_a1 = predictions[(protected == 1) & (labels == 1)].mean()
    eo_penalty = (tpr_a0 - tpr_a1).abs()

    # Weighted sum of penalties
    fairness_penalty = lambda_dp * dp_penalty + lambda_eo * eo_penalty
    return fairness_penalty

def training_step(model, X, y, protected, optimizer, lambda_dp=0.5, lambda_eo=0.5):
    predictions = model(X).squeeze()
    base_loss = nn.BCELoss()(predictions, y.float())
    fairness_penalty = fairness_regularizer(predictions, protected, y)
    total_loss = base_loss + fairness_penalty

    optimizer.zero_grad()
    total_loss.backward()
    optimizer.step()
    
    return total_loss.item()
```

**How to use:** Adjust `lambda_dp` and `lambda_eo` to emphasize fairness more or less, depending on priorities.

### **4. Multi-Objective Optimization**

Balances multiple objectives (accuracy, fairness, interpretability). Uses scalarization (weighted sum) to turn multi-objective into one loss.

**Code Template:**

```python
def multi_objective_loss(predictions, labels, protected, weights):
    """
    Combine accuracy, fairness, and complexity into one loss
    """
    # Accuracy loss
    accuracy_loss = nn.BCELoss()(predictions, labels.float())

    # Fairness loss (demographic parity)
    mean_pred_a0 = predictions[protected == 0].mean()
    mean_pred_a1 = predictions[protected == 1].mean()
    fairness_loss = (mean_pred_a0 - mean_pred_a1).abs()

    # Complexity loss (L2 regularization on weights as interpretability proxy)
    complexity_loss = sum(torch.norm(p) for p in model.parameters())

    # Weighted objective
    total_loss = (weights['accuracy'] * accuracy_loss +
                  weights['fairness'] * fairness_loss +
                  weights['interpretability'] * complexity_loss)
    return total_loss
```

**How to use:** Pass in different `weights` (e.g., `{accuracy: 0.7, fairness: 0.2, interpretability: 0.1}`) depending on stakeholder priorities.

## VIII. Integration & Verification Framework

After implementing and selecting fairness-enhancing methods (constraints, adversarial debiasing, regularization, or multi-objective optimization), the next crucial step is **verification**. A method is only valuable if it both improves fairness **and** maintains acceptable model performance in practice. This framework ensures that chosen interventions are systematically validated, compared, and stress-tested before deployment.

**Validation Protocol**

1. **Baseline:** Train the model without fairness interventions → record:

   * Accuracy, F1, AUC.
   * Fairness metrics: demographic parity difference, equal opportunity difference.

2. **Fairness Intervention:** Apply the chosen in-processing method → record the same metrics.

3. **Compare:** Quantify improvement in fairness vs any performance drop.

4. **Robustness Tests:**

   * Subgroup performance (e.g., male vs female).
   * Hyperparameter sensitivity (e.g., λ changes).
   * Data shifts (simulate distribution drift).

**Success Criteria**

* Fairness metric ↑ at least **10–15%**.
* Accuracy drop ≤ **5%**.
* Stable fairness gains across subgroups.
* Results reproducible across multiple runs.

⚠️ **Intersectional Consideration (Very Important!)**
Always test **intersections of protected groups** (e.g., *Black female*, *older immigrant*, *young disabled*) in addition to single-group fairness. A model may appear fair across each group individually but still hide severe biases at these intersections — which are often the most vulnerable populations.

## IX. Deliverables

After completing this framework, you are expected to provide the following outputs:

1. **Code Implementation**

   * At least one fairness-enhanced model implemented using one of the four methods (**Constraints Optimization, Adversarial Debiasing, Fairness Regularization, Multi-Objective Optimization**).
   * Well-commented and reproducible code.

2. **Baseline Metrics**

   * Accuracy, F1, AUC recorded for the **unmodified baseline model**.
   * Initial fairness metrics: demographic parity difference, equal opportunity difference.

3. **Fairness Intervention Results**

   * Updated metrics after applying chosen fairness method(s).
   * Comparison table: fairness improvement vs performance trade-offs.

4. **Robustness Evaluation**

   * Subgroup analysis (male/female, etc.).
   * ⚠️ **Intersectional Consideration**: Explicit tests for combinations (e.g., Black female, Latino male).
   * Hyperparameter sensitivity (e.g., λ variations).
   * Data shift tests (distribution drift).

5. **Verification Report**

   * Summary of whether success criteria were met:

     * Fairness metric ↑ at least 10–15%
     * Accuracy drop ≤ 5%
     * Stable fairness across subgroups/intersections
     * Reproducibility across runs

## X. Validation Checklist

[] Did we fully understand all four methods and their trade-offs (constraints, adversarial, regularization, multi-objective)?
[] Did we choose the method most compatible with our model type and fairness definition (using the decision tree)?
[] Did we implement the method correctly, with clear and reproducible code?
[] Did we establish a baseline (performance + fairness metrics) before intervention?
[] Did we record fairness and accuracy metrics **after intervention** for comparison?
[] Did we explicitly test subgroup performance (e.g., male vs female)?
[] Did we test **intersectional groups** (e.g., Black female, Latino male) for hidden bias?
[] Did we perform robustness checks (hyperparameter sensitivity, data shifts)?
[] Did fairness improve by at least 10–15% while accuracy drop stayed ≤ 5%?
[] Did we confirm fairness gains are **stable across runs** (reproducibility)?
[] Did we clearly document the fairness–performance trade-off for stakeholders?

**Pass / Fail rule:**

* If **at least 80% of items are complete**, you can move forward to the next step.

* If not, pause and fill the gaps before starting the next component.

---

### ⏭️ Next Action: Proceed to [**Post-processing Toolkit**](./4-post-processing-toolkit.md)

