# Appendix
This section brings together the key **fairness concepts** to help your team build a clear understanding. For deeper details, definitions, and examples, you can consult the [**glossary**](#glossary), [**component notes**](#component-notes), and [**references**](#references).


## I. Glossary

| Term        | Explanation                                                                                                                                                                                                                                                                                                |
| ------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Data deserts**         | Areas or groups with little to no data. For example, if a loan approval model is trained mostly on urban credit records, rural borrowers will have almost no representation. The system might reject them or treat them as risky, not because they are risky, but because the data about them is missing. |
| **Proxy variable**       | Something that secretly stands in for something else. For example, ZIP code often correlates with race because of historical segregation. Even if you don’t collect race, your model could still learn racial patterns through ZIP code.                                                                   |
| **Redlining**            | A practice (common in the mid-20th century U.S.) where banks refused loans to people in neighborhoods marked on a map, usually Black, immigrant, or low-income areas, regardless of individual qualifications. Outlawed today, but its effects remain in financial data.                                 |
| **Feedback loop**        | When a model’s decisions influence the data it will later learn from. Example: if an AI denies credit to a group, they never build repayment histories, which makes future models think they’re “risky,” reinforcing the bias.                                                                             |
| **Structural injustice** | Long-standing social or economic inequalities built into systems, like education gaps, wealth gaps, or unequal job access, that can silently flow into AI training data.                                                                                                                                 |
| **Intersectionality**    | When someone belongs to multiple marginalized groups at once (e.g., Black women in low-income jobs). These overlapping identities can face unique disadvantages that single-category analyses may miss.                                                                                                    |
| **True Positive (TP)**  | The system **correctly predicts a positive outcome**. Example: An AI loan model approves a qualified applicant.                     |
| **False Positive (FP)** | The system **incorrectly predicts a positive outcome**. Example: The model approves an applicant who later defaults on the loan.    |
| **True Negative (TN)**  | The system **correctly predicts a negative outcome**. Example: The model rejects an applicant who indeed would have defaulted.      |
| **False Negative (FN)** | The system **incorrectly predicts a negative outcome**. Example: The model rejects an applicant who would have repaid successfully. |                                       |
| **Confidence Interval (CI)** | A range around a metric estimate showing where the “true value” likely falls. If your hiring model shows a 10% gender gap with a ±2% CI, the real gap is probably between 8–12%.                                                 |
| **Credible Interval**        | Like a confidence interval, but from Bayesian statistics. Especially useful when you have very little data (e.g., fewer than 100 examples per group).                                                                            |
| **Bootstrap Resampling**     | A statistical method that repeatedly samples your dataset (with replacement) to check how stable your metric is. Helps verify that an observed disparity isn’t just due to random chance.                                        |
| **Threshold Tuning**         | Adjusting decision cutoffs (like an interview score threshold) separately per group to equalize error rates. Must be documented carefully to avoid hidden quotas or legality issues.  

## II. Component Notes 

### I.I. Historical Context Assessment

* **History lives in data** → AI systems often repeat old patterns of discrimination (race, class, gender).
* **Bias doesn’t need explicit labels** → even neutral features (like ZIP code, education, or healthcare costs) can act as **proxies** for protected traits.
* **Intersectionality matters** → people experience overlapping harms (e.g., Black women face risks missed if you only check “race” or “gender” separately).
* **Classification is political** → categories like “male/female” or “Black/White” are not neutral; they come from cultural and historical power structures.
* **Missing data isn’t random** → marginalized groups are often left out, which makes them invisible in models.
* **Power shapes datasets** → who collects, labels, and defines data influences whose stories are told and whose are silenced.
* **Feedback loops amplify bias** → biased predictions today shape tomorrow’s data, locking in discrimination over time.
* **Fairness depends on ethical lens:**
  * **Consequentialism (outcomes):** A hiring AI is fair if it maximizes jobs for qualified people → unfair if it systematically reduces opportunities for women.
  * **Deontology (rights):** A credit scoring system is fair if everyone has equal right to apply → unfair if it denies loans based on protected attributes like race.
  * **Virtue ethics (values):** A healthcare triage tool is fair if it reflects values like compassion and justice → unfair if it prioritizes profit over patient well-being.
  * **Non-Western / cultural frameworks:** A land-use model is fair if it respects Indigenous stewardship traditions → unfair if it ignores community consent or cultural values.
* **Domain-specific knowledge is crucial** → fairness challenges look different in healthcare, hiring, finance, or policing.
* **Documentation is as important as modeling** → recording assumptions, trade-offs, and limitations builds accountability and trust.
* **Skipping historical context is costly** → just like ignoring security, neglecting bias history leads to failures, reputational risk, and regulatory trouble.


### I.II. Fairness Definition Selection

* **Fairness has no single definition** → Different people (users, developers, regulators, communities) may all define fairness differently.
* **Fairness is philosophical, not just technical** → Competing views come from traditions like:

  * **Egalitarianism**: fairness = equal outcomes.
  * **Libertarianism**: fairness = fair treatment of individuals.
  * **Rawlsian justice**: fairness = protect the most disadvantaged.
  * **Utilitarianism**: fairness = maximize total benefit, even with unequal outcomes.
* **Stakeholders have conflicting goals** → One group may want fewer false accusations, another may want fewer missed risks. You can’t satisfy everyone perfectly.
* **Fairness depends on context** → What’s fair in U.S. hiring law may not be fair in Indian education or EU healthcare. Ignoring context risks harm.
* **Abstraction trap** → A model that ignores history (like past discrimination in admissions) can unintentionally reinforce that discrimination.
* **Not all fairness goals can be achieved at once** → It is mathematically impossible to satisfy all fairness criteria simultaneously. Trade-offs are unavoidable.
* **Fairness spans the entire ML lifecycle** → From defining the problem, choosing data, training models, to deploying systems in the real world.
* **Intersectionality matters** → Systems can look fair on average but still fail for people at the intersection of identities (e.g., Black women).
* **Documentation is essential** → Assumptions, trade-offs, and reasoning must be recorded for accountability and trust.
* **Skipping historical analysis is risky** → Neglecting the history of bias is like skipping security checks: it can lead to failures, lawsuits, and reputational damage.
* **Structured process helps** → Analyze context, map stakeholders, explore fairness definitions, and document trade-offs.

### I.III. Types and Sources of Bias
* Bias often starts with data; small choices in collection, measurement, or encoding can bake in unfairness.
* Types of data bias:
  * **Historical Bias:** Past discrimination embedded in data. Example: underrepresentation of women or minorities in hiring data.
  * **Sampling Bias:** Some groups missing or underrepresented. Example: over-policed communities in criminal justice data.
  * **Measurement Bias:** Wrong or unfair proxies for what you want to measure. Example: healthcare cost used as a proxy for need.
  * **Encoding Bias:** Bias introduced in data transformation or encoding. Example: word embeddings associating “woman” with homemaker.
* Intersectionality amplifies bias; people with multiple marginalized identities may be affected more.
* Fair data alone doesn’t ensure fairness; algorithmic choices can create or worsen bias.
* Sources of algorithmic bias:
  * **Inductive Bias & Architecture:** Models assume certain patterns; majority patterns often favored.
  * **Loss Functions & Optimization:** Optimizing for average performance can ignore minority groups.
  * **Regularization & Hyperparameters:** May remove subtle but important minority features.
  * **Evaluation Protocol:** Aggregate metrics hide disparities; disaggregated group-level metrics are necessary.
* Feedback loops occur when model outputs affect future inputs; small biases can grow over time.
* Types of feedback:

  * **Direct Feedback:** Model outputs directly influence future inputs.
  * **Indirect Feedback:** Outputs influence inputs through external factors.
  * **User-Driven Feedback:** Users adapt to system rules or patterns.
  * **System-Driven Feedback:** Models self-update and can amplify bias.
* Intersectional analysis is critical; compounded effects often appear at identity overlaps.
* Fairness is not only algorithmic; it emerges during deployment through human, organizational, and environmental interactions.
* Key sources of deployment bias:

  * **Human-AI Interaction:** User behavior and trust can amplify bias.
  * **Interface & Accessibility:** Poor UI or non-inclusive design can exclude users.
  * **Infrastructure & Resources:** Access to devices, internet, or technical support affects outcomes.
  * **Organizational Context:** Workflows, metrics, and incentives influence fairness.
  * **Intersectionality:** Biases interact across attributes; combined effects can be severe.

### I.IV. Comprehensive Fairness Metrics
* Group fairness metrics compare outcomes across demographic groups.
* **Statistical Parity:** Outcome distribution equal across groups. Example: equal % of hires/admissions.
* **Equal Opportunity (EO):** True positive rates equal across groups. Example: qualified applicants treated fairly.
* **Equalized Odds (EOds):** TPR & FPR equal across groups. Example: errors balanced across groups.
* **Predictive Parity (PPV):** Prediction reliability equal across groups. Example: a positive prediction means the same thing for everyone.
* Trade-offs exist: EO vs Statistical Parity → fairness for qualified individuals vs equal outcome distribution.
* EOds vs PPV → balancing errors vs prediction reliability.
* **Impossibility Theorem:** All metrics cannot be satisfied simultaneously if base rates differ.
* Implement fairness metrics by selecting based on domain, using true labels (Y), predictions (Ŷ), and protected attributes (A).
* Visualize disparities with confidence intervals and combine with traditional metrics like accuracy.
* Individual fairness ensures similar individuals are treated similarly.
* **Similarity-Based Fairness:** Similar input → similar outcome.
* **Fairness Through Awareness (FTA):** Use protected attributes contextually to understand differences.
* **Counterfactual Fairness:** Would the outcome change if the protected attribute changed?
* Intersectional fairness addresses overlapping identities, e.g., race × gender × age.
* Multi-attribute metrics reveal hidden disparities:
  * **Multi-Attribute Error Rate Balance:** Compare FP/FN rates across intersections.
  * **Intersectional Demographic Parity:** Equal predictions across subgroups.
  * **Worst-Case Subgroup Analysis:** Identify the most disadvantaged subgroup.
* Small sample sizes in intersectional groups create high variance and unreliable metrics.
* Solutions include Bayesian estimation, hierarchical modeling, data augmentation, and visualizing uncertainty.
* Aggregated group metrics can hide severe subgroup disparities (Simpson’s Paradox).
* Statistical significance ensures observed fairness gaps are real and not random noise.
* Sources of uncertainty: small sample sizes, random variation in outcomes.
* Techniques: confidence intervals, hypothesis testing, p-values, multiple testing corrections (e.g., FDR), cross-validation, and robustness checks.
* Bayesian and hierarchical modeling help with rare subgroups.
* Report uncertainty alongside metrics, use tiered thresholds for action, and visualize findings for stakeholders.
* No single fairness metric fits all contexts; multiple definitions are needed.
* Individual fairness complements group fairness; both must be considered.
* Intersectional analysis is critical to uncover hidden bias.
* Fairness tools should include multi-dimensional metrics, FTA and counterfactual testing, intersectional analysis, and robustness checks.
* Visualizations and storytelling improve stakeholder understanding and help guide action.

## III. References
* Angwin, J., Larson, J., Mattu, S., & Kirchner, L. (2016). Machine bias. ProPublica. Retrieved from https://www.propublica.org/article/machine-bias-risk-assessments-in-criminal-sentencing
* Barocas, S., & Selbst, A. D. (2016). Big data's disparate impact. California Law Review, 104(3), 671–732. https://doi.org/10.15779/Z38BG31
* Bolukbasi, T., Chang, K.-W., Zou, J. Y., Saligrama, V., & Kalai, A. T. (2016). Man is to computer programmer as woman is to homemaker? Debiasing word embeddings. In Advances in Neural Information Processing Systems (pp. 4349–4357).
* Agarwal, A., Beygelzimer, A., Dudík, M., Langford, J., & Wallach, H. (2018). A reductions approach to fair classification. In Proceedings of the 35th International Conference on Machine Learning (pp. 60-69).
* Chouldechova, A. (2017). Fair prediction with disparate impact: A study of bias in recidivism prediction instruments. Big Data, 5(2), 153-163. https://doi.org/10.1089/big.2016.0047
* Corbett-Davies, S., Pierson, E., Feller, A., Goel, S., & Huq, A. (2017). Algorithmic decision making and the cost of fairness. In Proceedings of the 23rd ACM SIGKDD International Conference on Knowledge Discovery and Data Mining (pp. 797-806). https://doi.org/10.1145/3097983.3098095
* D'Amour, A., Srinivasan, H., Atwood, J., Baljekar, P., Sculley, D., & Halpern, Y. (2020). Fairness is not static: Deeper understanding of long term fairness via simulation studies. In Proceedings of the 2020 Conference on Fairness, Accountability, and Transparency (pp. 525-534). https://dl.acm.org/doi/10.1145/3351095.3372878
* Ekstrand, M. D., Tian, M., Azpiazu, I. M., Ekstrand, J. D., Anuyah, O., McNeill, D., & Pera, M. S. (2018). All the cool kids, how do they fit in?: Popularity and demographic biases in recommender evaluation and effectiveness. In Proceedings of the 1st Conference on Fairness, Accountability and Transparency (pp. 172-186). https://proceedings.mlr.press/v81/ekstrand18b.html
* Crenshaw, K. (1989). Demarginalizing the intersection of race and sex: A black feminist critique of antidiscrimination doctrine, feminist theory, and antiracist politics. University of Chicago Legal Forum, 1989(1), 139–167.
