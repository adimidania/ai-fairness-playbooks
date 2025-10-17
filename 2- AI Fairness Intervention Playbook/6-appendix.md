# Appendix

## I. Notes

### I.I. Casual Fairness
* Correlation only shows patterns, not causes → a model can treat groups differently even if numbers look the same.
* Fixing fairness with just statistics is like treating symptoms, not the disease → you need to know *why* bias happens.
* Two cases with the same numbers can mean very different things: sometimes the difference is legitimate (e.g., education affects credit), sometimes it’s pure discrimination (e.g., race directly changes decisions).
* Causal models act like blueprints → they reveal which connections are fair (skills → hiring) and which are unfair (gender → hiring).
* Counterfactuals are “what if” tests → would this person get the same outcome if only their race or gender were different?
* Not every effect of a protected trait is unfair → some pathways are legitimate, others transmit discrimination.
* Interventions depend on *where bias enters*:
  * At data collection (who is missing?)
  * At features (are they proxies for protected traits?)
  * At training (do we enforce the wrong constraints?)
* Intersectionality is key → people can face unique harms at the overlap of traits (e.g., Black women may face patterns missed if we look only at “gender” or “race” alone).
* Domain knowledge matters → fairness looks different in hiring, healthcare, finance, or policing.
* Documentation is part of fairness → recording assumptions and pathways builds trust.
* To fight bias, we need maps → causal models act like blueprints showing how different factors influence decisions.
* Without these maps, fairness fixes risk treating surface symptoms (like unequal outcomes) instead of the root causes of discrimination.
* Causal graphs (arrows between variables) make bias pathways visible:
  * **Direct discrimination**: protected trait directly influences outcome (e.g., gender → hiring).
  * **Indirect discrimination**: trait affects an intermediate factor (e.g., gender → career gaps → hiring).
  * **Proxy discrimination**: unrelated variables secretly “stand in” for traits (e.g., ZIP code as proxy for race).
* Choosing which variables to include is critical → leave out the wrong ones and you miss bias, add the wrong ones and you confuse the picture.
* Structural equation models (SEMs) add math to the graph → they tell us not just *what* connects, but *how strongly*.
* Validating models matters → just like engineers stress-test a bridge before opening it, we must test if the causal model matches reality.
* Interventions depend on these models → the “where” and “how” of fixing bias change depending on which pathways are unfair.
* Domain expertise is vital → fairness is not just data science; it needs input from people who understand the field (finance, healthcare, hiring, etc.).
* Intersectionality must be built in → overlapping identities (like being both Black and female) can create unique bias pathways that don’t appear when traits are checked one by one.
* Counterfactual fairness asks a simple “what if”: *Would this person get the same prediction if only their protected attribute (like race or gender) were different, while everything else stayed the same?*
* This goes beyond stats → instead of just checking numbers, it digs into causes to see if decisions are unfairly tied to identity.
* Classic fairness metrics (like demographic parity) can miss hidden bias → they fix the “symptom” without addressing the “disease.”
* Structural causal models (SCMs) are like simulators → they let us test “what if” scenarios by changing just one thing (e.g., flipping gender) while holding everything else consistent.
* Path-specific fairness = nuance → not every effect of a protected trait is unfair. Example: gender → subject preference → course performance may be fine, but gender → biased grading is unfair.
* Implementing counterfactual fairness can be done by:
  * Removing unfair signals while keeping useful ones (fair representations).
  * Using causal math to control for unfair pathways.
  * Training with explicit fairness constraints.
* Evaluating fairness means checking how much predictions *change* between factual and counterfactual worlds → big changes = bias, small/no changes = fairness.
* Challenge: we can’t “observe” counterfactuals directly → we rely on causal assumptions and sensitivity analysis.
* Analogy: it’s like a medical trial where only one factor changes (the drug), so you know if the outcome really depends on it.
* Intersectionality matters → we must test counterfactuals that flip *multiple* attributes (e.g., Black woman → White man) because discrimination often hides in combinations.
* Ignoring intersectionality risks “fixing” fairness for single traits but leaving compounded harms (e.g., Black women still face bias even if race and gender seem “fair” separately).
* Counterfactual fairness gives us a principled lens to spot, measure, and fix discrimination by simulating how outcomes should behave in a fair world.
* The big challenge in causal fairness: we can never see both realities at once → what happened *and* what would’ve happened if protected traits were different.
* This missing data problem makes fairness harder → we must *estimate* counterfactuals instead of directly observing them.
* Pearl’s “ladder of causation”:
  * Level 1: See correlations (who gets hired more).
  * Level 2: Imagine interventions (what if we changed one factor).
  * Level 3: Counterfactuals (what if this same person had a different gender).
* In practice, we rarely have experiments → we rely on observational methods to approximate fairness.
* Matching methods: compare “similar” people who differ only in protected traits to guess the causal effect.
* Instrumental variables: use outside factors (like a policy change) that affect outcomes only through the protected trait.
* Regression discontinuity: exploit thresholds (like a test score cutoff) to compare people just above and below.
* Difference-in-differences: compare before/after changes between affected and unaffected groups.
* Sensitivity analysis = stress test → asks: *how strong would hidden bias have to be to overturn our fairness conclusion?*
* Causal discovery = detective work → use data patterns to guess the hidden causal map when experts don’t fully know it.
* But discovery is tricky → algorithms may suggest plausible causal pathways, but assumptions always matter.
* Domain adaptation: fairness rules may not “travel well” → what’s fair in one country/system might not hold in another. We need ways to check if causal lessons still apply in new settings.
* Analogy: it’s like training a doctor in one hospital, then asking if their skills and guidelines still apply in another with different patients and resources.
* For fairness, this means we can’t just assume models are fair everywhere; we need to adapt and re-check.
* Intersectionality makes all this harder → data for specific intersections (e.g., older minority women) is often scarce.
* Bayesian / hierarchical methods help by “borrowing strength” → learning from related groups while still capturing unique intersectional effects.
* Causal fairness in practice means working with imperfect data, making careful estimates, testing robustness, and being transparent about uncertainty—especially at demographic intersections.

### I.II. Data-Level Interventions (Pre-Processing)
* Before fixing bias, you need to know where it lives → **data auditing** is the diagnostic step. Skipping it risks solving the wrong problem or creating new unfairness.
* **Representation checks**: Look at who is in your dataset (by gender, race, and their combinations). Bias often hides in intersections (e.g., women of color may be severely underrepresented even if women and people of color look balanced separately).
* **Correlation checks**: Even if protected attributes (like race) are removed, other variables (like ZIP code or prior arrests) may strongly correlate and act as stand-ins, letting bias sneak back in.
* **Label checks**: Outcomes may already be biased (e.g., résumés with “female” or “Black-sounding” names scored lower by annotators). If labels are unfair, the model will be unfair.
* **Baseline fairness metrics**: Measure disparities in raw data (e.g., approval rates, error rates) before any modeling. This gives you a benchmark for later interventions.
* **Intersectionality**: Always check combinations (e.g., gender × race). Averages can hide real harm happening at the intersections.
* **Practical workflow**:

  * Document how the data was collected and what biases might come from that.
  * Compare group distributions to population benchmarks (e.g., census).
  * Map correlations to uncover hidden proxies.
  * Audit labels for bias or inconsistencies.
  * Calculate fairness metrics and visualize gaps clearly.
* Think of this like a **medical checkup** for your dataset — you don’t just take the temperature, you run blood tests and scans to find hidden problems before prescribing treatment.
* Models usually learn most from the majority group → **minority patterns get ignored** unless we rebalance the data.
* **Reweighting** = don’t change the dataset, just change how much each example “counts” in training.

  * Example: give more weight to qualified minority applicants so their patterns shape the model as much as the majority group’s.
* **Resampling** = actually change the dataset before training.

  * Example: duplicate underrepresented group examples, or use synthetic techniques (like SMOTE) to create new ones.
* Both methods aim to **level the playing field**: majority and minority groups get fairer influence in shaping the model.
* Think of reweighting like **weighted voting** → smaller groups get more influence to balance decisions.
* Think of resampling like **curating a diverse reading list** → you add more voices from underrepresented authors so they aren’t drowned out.
* **Watch out for pitfalls**:

  * Too much resampling → overfitting small groups.
  * Extreme reweighting → unstable predictions.
  * Fix = use cross-validation, regularization, and keep track of calibration.
* **Intersectionality matters**: if you only rebalance by race or gender separately, you might still leave out groups like women of color. Always check combinations, not just single categories.
* These methods are **simple, practical, and plug into most ML pipelines** → they don’t require rewriting the model, just adjusting the training data or weights.
* You can transform features so they no longer reveal protected attributes but still keep predictive power, using these main approaches:

* **Disparate Impact Removal**
  * Make feature distributions identical across groups.
  * Preserves rank-ordering within groups.
  * Example: transform “distance to financial center” so it no longer predicts race.

* **Optimal Transport**
  * Optimize how distributions are shifted to fairness.
  * Minimizes distortion/information loss.
  * Example: adjust “communication style” in resumes to remove gender correlation but keep job relevance.

* **Learning Fair Representations (LFR)**
  * Build a new feature space hiding protected attributes.
  * Uses representation learning (e.g., adversarial methods).
  * Example: convert resumes into fair features where gender is obscured, but skills remain visible.
* Disparate impact removal → like **color correction** (balances tones fairly).
* Optimal transport → like **logistics optimization** (minimal cost movement).
* Fair representations → like **gender-neutral translation** (keeps meaning, removes bias).
* When it comes to intersectionality, single-attribute fixes may fail for overlaps (e.g., Black women).
* Need transformations that address **multiple protected attributes simultaneously**.
* Use distribution transformation when bias is baked into features. It removes proxy discrimination and keeps fairness + predictive accuracy balanced.
Perfect — here’s **Unit 4: Fairness-Aware Data Generation** boiled down into clean **bulleted points**, just like we did for the earlier ones:
* When reweighting or distribution fixes aren’t enough, generate new synthetic data to balance representation, reduce bias, and preserve predictive signals.
* Synthetic data lets you **fill gaps** where minority groups are underrepresented.
* Unlike reweighting or transformation, generation can create **entirely new examples** that don’t exist in the original dataset.
* Helps break unfair correlations (e.g., income ↔ race) while keeping useful relationships.
* There are some key approaches for data augmentation including:

* **Synthetic Data Generation**

  * Create new examples to balance the dataset and reduce bias.
  * Example: Generate loan applicants with diverse income + education combinations not tied to race.

* **Conditional Generation**

  * Control generation based on attributes (e.g., gender, race).
  * Guarantees balanced group representation.
  * Example: Generate equal numbers of male and female applicants with similar qualifications.

* **Counterfactual Data Augmentation**

  * Generate “what-if” versions of individuals by flipping protected attributes while holding qualifications constant.
  * Example: Same resume, but change gender → ensures model learns to ignore gender in hiring.
* Synthetic generation → like **cooking a new dish from scratch** with balanced ingredients.
* Conditional generation → like **custom manufacturing** to meet exact group specifications.
* Counterfactual augmentation → like **parallel universes**, where the same person only differs in race or gender.
* Fairness-aware generation is most useful when data is severely biased or incomplete — it creates the missing pieces needed for equitable training.

### I.III. Model-Level Interventions (In-Processing)
* Fairness in machine learning should be built into the model itself, not just fixed afterward.
* Instead of only chasing accuracy, models can be trained to also respect fairness rules.
* These fairness rules are turned into **mathematical instructions** (constraints or objectives) that the training process must follow.
* Different fairness goals (like equal treatment across groups or equal error rates) translate into different types of mathematical rules.
* Adding fairness rules makes training harder because the model has to balance both accuracy and fairness.
* Some methods, like **Lagrangian techniques**, turn strict fairness rules into softer “penalties” that are easier for algorithms to handle.
* Fairness constraints can reduce performance on traditional metrics (like accuracy), so there’s always a **trade-off** between fairness and prediction quality.
* Sometimes fairness rules can be too strict or conflict with each other, making it impossible to satisfy them all at once.
* Choosing fairness rules is like setting boundaries in a GPS: you might reach your goal slower, but you avoid unfair “routes.”
* Fairness should be considered at multiple levels of a machine learning pipeline: what the model optimizes, the rules it must follow, how it’s trained, and how it’s evaluated.
* Fairness isn’t always simple—if you only check single attributes (like gender or race separately), you can miss unfairness at **intersections** (e.g., women of a certain race).
* To handle intersectional fairness, models may need more complex rules that look at combinations of attributes without exploding in complexity.
* Overall, the challenge is to find the **best balance**: enough fairness to prevent discrimination, while still keeping the model useful and accurate.
* Adversarial debiasing trains models to be accurate **while hiding protected attributes** (like race or gender) from influencing predictions.
* It works by setting up a **competition**:

  * The predictor tries to do the main task well.
  * The adversary tries to detect if the predictor is leaking protected attribute info.
  * The predictor learns to succeed **without giving away protected info**, reaching a fair balance.
* This approach is especially useful for complex models (like deep neural networks) where writing fairness constraints is difficult.
* Instead of only limiting outputs, adversarial debiasing filters out protected information from **internal representations** of the model.
* Training uses special tricks for stability:

  * Adjusting the power of adversary vs. predictor.
  * Using gradient reversal layers to push the predictor away from leaking sensitive info.
  * Gradually strengthening the adversary during training.
  * Adding regularization to keep learning balanced.
* If training isn’t carefully managed, models can become unstable, unfair, or perform poorly.
* Different design choices (whether the adversary looks at hidden layers or final outputs) change the type of fairness the model achieves.
* This method is flexible—it can work with many model types, not just one class of algorithms.
* Intersectional fairness needs special care:

  * Standard adversaries may miss biases against **overlapping groups** (e.g., women of a certain ethnicity).
  * Advanced versions use multi-task or hierarchical adversaries that catch unfairness at intersections.
* Conceptually, adversarial debiasing is like:

  * A **filter** removing sensitive info from documents.
  * A **basketball game** where a shooter adapts against a defender who tries to exploit patterns.
  * A **training exercise** where two students compete, and the teacher must balance difficulty so both improve.
* Overall, adversarial debiasing provides a powerful, flexible way to reduce bias by “unlearning” sensitive information, while still allowing models to remain accurate.
* Regularization adds fairness penalties into the model’s training loss instead of enforcing strict rules.
* This makes fairness a softer, more flexible goal that balances with accuracy.
* A penalty term measures group disparities and is combined with the usual loss.
* A regularization parameter (λ) controls the balance:

  * Small λ → model favors accuracy.
  * Large λ → model favors fairness.
* Tuning λ helps practitioners find the best trade-off for their context.
* Regularization can prevent “fairness gerrymandering,” where a model looks fair overall but hides unfairness in subgroups.
* Group-specific regularization applies stronger penalties to disadvantaged or intersecting subgroups.
* Trade-off curves (fairness vs. accuracy plots) help visualize the impact of different λ values.
* Regularization is less rigid than constraints, making it easier to apply in practice and scale to complex models.
* Fairness often conflicts with accuracy, precision, or recall, so trade-offs are unavoidable.
* Adding fairness constraints can reduce accuracy, since predictive features may correlate with protected attributes.
* Multi-objective optimization treats fairness and performance as equally important goals instead of making fairness a side constraint.
* Pareto optimality defines solutions where you cannot improve fairness without hurting performance, or vice versa.
* The Pareto frontier shows the full set of best compromise models, letting stakeholders choose rather than rely on arbitrary tuning.
* Scalarization methods combine objectives into one (e.g., weighted sums, ε-constraints) to generate different points along the frontier.
* Regularization can be seen as scalarization with fixed weights, but multi-objective methods explore the full trade-off space.
* Preference articulation methods (a priori, a posteriori, interactive) help stakeholders decide which solution best matches their values.
* These decisions are value-driven, not purely technical, so stakeholder engagement is critical.
* Intersectionality requires adding fairness objectives for overlapping groups (e.g., gender × race) to avoid hidden unfairness.
* Visualization of trade-offs helps communicate fairness-performance balances clearly to decision-makers.
* Multi-objective frameworks make fairness optimization principled, transparent, and aligned with societal priorities.

### I.VI. Prediction-Level Interventions (Post-processing)
* Threshold optimization is a fairness method applied **after training**, so you don’t need to retrain or change the model.

* It works even for black-box models where you can’t see inside or modify the algorithm.

* Standard ML decisions often use one threshold (e.g., probability > 0.5 = positive).

* Using one threshold for everyone often **creates unfair error rates** across groups because probability distributions differ.

* The solution is to **set different thresholds for different groups** to reduce disparities in false positives/negatives.

* Hardt, Price, and Srebro (2016): showed that group-specific thresholds can satisfy fairness definitions like equal opportunity or equalized odds.

* This makes threshold optimization powerful in real-world systems where speed matters more than retraining.

* **Why one threshold is unfair**:

  * Different groups often have different score distributions due to history, data, or bias.
  * Same threshold across groups → unfair error rates.
  * Example: loan approvals → disadvantaged groups may be denied more often even if qualified.

* **Fairness definitions applied to thresholds**:

  * Equal opportunity = thresholds that equalize true positive rates across groups.
  * Equalized odds = thresholds that equalize both true positives and false positives across groups.
  * Demographic parity = thresholds that equalize selection rates across groups.

* These can be written as mathematical optimization problems: maximize accuracy while satisfying fairness constraints. But they usually involve trade-offs with performance.

* **ROC curves help** visualize how thresholds affect fairness vs. performance.

* Each group has its own ROC curve → different possible thresholds.

* Moving thresholds changes true positive and false positive rates.

* Kleinberg, Mullainathan, and Raghavan (2016): it’s mathematically impossible to satisfy all fairness metrics at once without losing accuracy.

* Friedler et al. (2019): threshold optimization makes these trade-offs transparent so decisions are explicit.

* **Implementation depends on whether protected attributes are available at decision time**:

  * If available → directly apply group-specific thresholds.
  * If not available → alternative methods:

    * Derived features: use legally acceptable features that correlate with group membership (Dwork et al., 2018).
    * Multiple thresholds for everyone: thresholds apply broadly but indirectly improve group fairness (Lipton et al., 2018).

* These methods balance fairness needs with legal/ethical restrictions.

* **Intersectionality challenges**:

  * Looking at gender or race alone can miss combined unfairness (e.g., young Black women).
  * Buolamwini & Gebru (2018, *Gender Shades*): showed large disparities at intersections.
  * Intersectional fairness in threshold optimization requires:

    * Considering multi-attribute thresholds.
    * Handling small sample sizes in subgroup data.
    * Prioritizing which intersectional disparities to address first.
    * Monitoring effects on all subgroup combinations.

* Calibration means predicted probabilities should match actual outcomes consistently across groups (e.g., if a model says 70% risk, about 70% should truly be positive cases for everyone).
* Without calibration, the same score can mean different things for different demographic groups, leading to subtle unfairness that accuracy alone won’t detect.
* Example: a “70% risk” may actually mean 85% for one group and 70% for another, which is fundamentally unfair.
* This matters in high-stakes domains like lending, healthcare, hiring, and criminal justice, where probability scores directly inform decisions.
* Pleiss et al. (2017) showed that models with identical accuracy can have large calibration disparities, proving accuracy isn’t enough to ensure fairness.
* Calibration is distinct from threshold optimization: thresholds handle binary decisions, calibration ensures the probabilities themselves are reliable.
* Kleinberg, Mullainathan, and Raghavan (2016) proved an impossibility result: you cannot simultaneously have perfect calibration and equal false positive/negative rates when base rates differ.
* This means trade-offs are unavoidable, and practitioners must decide when to prioritize calibration vs. other fairness metrics depending on context.
* Common calibration methods include:

  * Platt Scaling: logistic regression applied per group.
  * Isotonic Regression: non-parametric adjustment that preserves ranking.
  * Beta Calibration: uses beta distribution, good for bounded probabilities.
  * Temperature Scaling: divides logits by a temperature parameter, often used for neural networks.
* Group-specific calibration applies these techniques separately per demographic group to address disparities.
* Calibration quality is measured with specialized metrics:

  * Expected Calibration Error (ECE): average mismatch between predicted and actual outcomes across bins.
  * Maximum Calibration Error (MCE): worst-case mismatch.
  * Reliability Diagrams: visual plots of predicted vs. actual probabilities (perfect = diagonal line).
  * Group-specific metrics: run calibration checks separately for each demographic group to detect disparities.
* Trade-offs in calibration fairness include:

  * Decision context prioritization: calibration may matter more in risk scoring, while error rate parity may matter more in hiring.
  * Partial satisfaction: minimize disparities across multiple metrics instead of expecting perfection.
  * Stakeholder communication: need to explain why all fairness metrics can’t be satisfied simultaneously.
* Corbett-Davies and Goel (2018) argued calibration is often the best fairness goal in risk-assessment contexts, since forcing error-rate parity can harm disadvantaged groups.
* In practice, calibration is implemented as a system layer: a calibration module adjusts outputs, group-specific functions apply transformations, a calibration dataset provides data for fitting, an evaluation module tracks metrics, and a governance layer manages fairness trade-offs.
* Miscalibration is like inconsistent grading: a “B” might mean 85% mastery with one teacher and 75% with another.
* Calibration methods are like grading curves: they adjust raw scores so a “B” always means the same.
* The fairness trade-off is like legal systems: consistency of punishment (calibration) can conflict with equal error rates (fairness across groups), and choices depend on context.

* Prediction transformation methods allow us to modify model outputs to meet fairness goals without retraining the model or changing its internals.
* This makes them especially useful when working with third-party systems, pre-trained models, or legacy deployments.
* Unlike threshold optimization or calibration, prediction transformations handle more complex fairness criteria while preserving prediction information.
* Dwork et al. (2018) described this as “decoupling the predictor from the fairness criteria,” meaning fairness can be imposed independently of how the model was built.
* Learned transformation functions discover the best way to map original predictions into fair outputs based on validation data.
* These are more flexible than fixed thresholds because they can create non-linear mappings that vary across groups and prediction values.
* Canetti et al. (2019) showed that learned transformations can produce better fairness–utility trade-offs by tailoring adjustments to real data patterns.
* Approaches for learned transformations include optimization-based learning (minimize prediction distortion while enforcing fairness constraints), transfer learning (using smaller fair models to correct larger biased ones), and adversarial methods (learning transformations that hide protected attributes from predictions).
* Distribution alignment techniques transform output distributions so different demographic groups have similar score distributions.
* Unlike calibration, which matches predicted probabilities to actual outcomes, distribution alignment ensures prediction patterns are consistent across groups even if outcome labels are missing.
* Feldman et al. (2015) introduced quantile-based methods to align features across groups, and the same idea applies to aligning predictions.
* Key alignment approaches include quantile mapping (forcing group distributions to match at the same percentiles), optimal transport (finding the minimal-cost mapping to align distributions), and distribution matching (reducing statistical distance between groups’ outputs).
* These methods are valuable in semi-supervised or unsupervised scenarios where full outcome data isn’t available.
* Fair score transformations modify scores directly to enforce fairness while keeping the relative ranking of predictions within each group intact.
* This is critical for ranking, recommendation, and risk scoring tasks where order matters more than raw values.
* Wei et al. (2020) showed that fair score transformations can improve fairness metrics significantly while preserving ranking quality.
* Common approaches include monotonic transformations (adjusting scores but preserving order), constrained re-ranking (changing positions to meet fairness rules), and score normalization (rescaling scores across groups so they are comparable).
* Prediction transformation methods extend threshold and calibration approaches by giving practitioners more expressive and flexible tools for fairness when only outputs are available.
* Purely algorithmic systems can struggle with fairness in borderline cases where predictions are uncertain.
* Rejection option classification solves this by allowing the model to defer uncertain or high-risk cases to human judgment instead of forcing automated decisions.
* This approach is especially important in high-stakes contexts like loans, healthcare, hiring, or criminal justice where both fairness and accuracy matter.
* Madras et al. (2018) showed that deferring uncertain cases to humans reduces disparate impact while keeping strong overall performance.
* Unlike threshold adjustments or calibration, which still force a decision for every case, rejection creates a third option: strategic deferral.
* The key idea is that fairness errors often cluster in boundary regions where confidence is low, so routing these cases to humans reduces unfairness.
* Confidence-based rejection thresholds set minimum confidence levels for automated decisions, and group-specific thresholds can balance fairness and accuracy better.
* Example: in loan approvals, clear approvals and rejections are automated, while mid-range confidence applications are reviewed by humans.
* Selective classification theory formalizes this approach as a trade-off between decision coverage (how many cases the model decides) and fairness.
* Geifman and El-Yaniv (2017) showed selective classification gives guarantees on error rates in the automated region.
* Gupta et al. (2022) extended this to fairness, showing that lowering coverage (automating fewer cases) can improve fairness across groups.
* Example: predictive policing could automate 70% of high-confidence cases and defer 30% borderline ones to human officers, reducing unfair outcomes.
* Human-AI collaboration models determine how deferred cases are handled by people.
* Effective systems don’t just flag cases but also provide reviewers with information about why the case was flagged and which fairness issues to consider.
* Lai and Tan (2019) found that giving humans algorithmic predictions as suggestions (not defaults) leads to fairer decisions.
* Green and Chen (2019) showed that decision aids highlighting fairness concerns help humans catch algorithmic biases.
* Example: in child welfare, a flagged case could show reviewers why the model is uncertain and prompt them to consider fairness-sensitive factors.
* Cost-sensitive rejection accounts for the fact that some mistakes are more harmful than others.
* It prioritizes human review for cases where automated errors would carry the highest fairness impact.
* De-Arteaga et al. (2020) showed that algorithmic triage can allocate human effort where it matters most for fairness.
* Example: in hiring, false negatives for underrepresented groups may be more costly due to historical discrimination, so borderline cases for these groups should be sent to humans more often.
* Cost-sensitive frameworks combine statistical uncertainty with fairness considerations, ensuring limited human oversight is deployed where it yields the most equity.
* Overall, rejection option classification shifts fairness interventions from “how to make every automated decision fair” to “which decisions should we automate at all,” creating hybrid systems that combine algorithmic efficiency with human judgment for better fairness outcomes.

## Glossary

[To be completed]