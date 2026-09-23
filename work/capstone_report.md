# Capstone Report — <your lane>

- **Author:** Rajavi Mhatre
- **Lane:** Refresh/Content Opportunity Scoring
- **Repo:** flyrank-ml
- **Date:** 21/09/2026

> Copy this file to `work/capstone_report.md` and fill it in as you build. The eight
> sections mirror the Pass / Needs-Work rubric axes, so nothing here is optional.

## 1. Problem framing

What decision does this support? Name the unit of analysis (page, client, day…), the output
(score, rank, cluster, report), the action a human takes from it, and the cost of a wrong
call. Why does data/ML help here at all?

Ans. 
Research Question- Can search and content-performance signals help identify pages that should be prioritized for content-refresh review?
Unit of analysis: Individual page record
Decision: Should X page be flagged for review?
Output: Ranked review list
Human Action: A FlyRank editor can review the highest-ranked pages and decide whether a content refresh is required.
Cost of inaccurate flagging: Time spent reviewing pages that do not require a refresh, as well as the possibility of overlooking pages that would benefit from review.

Why does Data Science/Machine Learning help here? Data Science and Machine Learning can combine multiple search and content-related signals to provide consistently ranked pages. Machine Learning can help go beyond a simple rule based system.

## 2. Data safety

Which data you used and which columns you deliberately excluded (and why). Leakage risks you
considered — especially label-derived fields (`trend_direction`, `trend_pct`) and pseudonymous
IDs (grouping only, never features). Confirm nothing client-identifying appears anywhere in
`work/`.

Ans. Data used: The dataset contains page-level search, traffic, engagement, freshness, and performance signals. The original columns were:

`content_id`, `client_id`, `search_volume`, `competition`, `competition_level`, `cpc`, `content_type`, `main_intent`, `word_count`, `char_count`, `provider_used`, `model_used`, `impressions_90d`, `clicks_90d`, `pageviews_90d`, `sessions_90d`, `users_90d`, `engaged_sessions_90d`, `ai_sessions_90d`, `scroll_events_90d`, `days_with_impressions`, `days_with_sessions`, `impressions_last_30d`, `clicks_last_30d`, `sessions_last_30d`, `impressions_prev_30d`, `clicks_prev_30d`, `sessions_prev_30d`, `content_age_days`, `age_tier`, `age_tier_order`, `days_since_last_update`, `freshness_tier`, `word_count_tier`, `char_count_tier`, `ctr`, `avg_position`, `engagement_rate`, `scroll_rate`, `ai_traffic_pct`, `impression_tier`, `position_tier`, `trend_direction`, `trend_pct`.

Excluded columns: `content_id` and `client_id` were excluded from modelling. `client_id` was retained only for grouped validation. Categorical, metadata, and derived tier columns were also excluded from the final model, including `competition_level`, `cpc`, `content_type`, `main_intent`, `word_count`, `char_count`, `provider_used`, `model_used`, `age_tier`, `age_tier_order`, `freshness_tier`, `word_count_tier`, `char_count_tier`, `impression_tier`, and `position_tier`.

The final leakage-reduced feature set was:

`search_volume`, `competition`, `impressions_90d`, `clicks_90d`, `pageviews_90d`, `sessions_90d`, `users_90d`, `engaged_sessions_90d`, `ai_sessions_90d`, `scroll_events_90d`, `days_with_impressions`, `days_with_sessions`, `clicks_last_30d`, `sessions_last_30d`, `clicks_prev_30d`, `sessions_prev_30d`, `content_age_days`, `days_since_last_update`, `ctr`, `avg_position`, `engagement_rate`, `scroll_rate`, `ai_traffic_pct`.

`trend_direction` was used to construct the target and was not used as a feature. `trend_pct` was also excluded because it is directly related to the target definition. During the leakage audit, `impressions_last_30d` and `impressions_prev_30d` were removed because they may contain information closely related to the construction of `trend_direction`.

Target: A binary label where `declining = 1` when `trend_direction = "down"` and `declining = 0` otherwise.

No client-identifying information, private queries, credentials, domains, or URLs are included in the project outputs.


## 3. Baseline

The transparent rule or score you built first. Why it's a fair comparison, and its numbers on
the same data and metric as your model.

Ans. The Week 4 baseline model ranks pages for review using 3 signals if they are to be refreshed depending on i) days since last update- if the 90 day impressions are above the median, ii) click through rate being below the median, and iii) whether the content was updated more than 90 days ago. The rule is transparent and provides a simple comparison for the ML model. However, as low CTR can result from poor ranking or search intent, it is important to note that some high-scoring pages may be weak refresh candidates, thereby making the baseline a prioritisation tool instead of a diagnostic one.

## 4. Model / analysis

Your method and why it fits the lane. The exact feature list (and what you left out on
purpose). The target or proxy definition, in one sentence.

Ans. Logistic Regression was used as a simple and interpretable binary classification model. The model classifies pages into a declining class, which can be used to rank pages for content refresh review.
The target is binary- declining=1 when trend_direction=="down" and declining=0 otherwise.
The final model uses the leakage-induced feature set describes in Section 2. trend_direction and trend_pct were not used as features as they relate to the target class. Similarly, impressions_last_30d and impressions_prev_30d were also removed as part of leakage audit.

The model output is a predicted probability of a decline for each page. Pages can then be ranked accordingly to create a review queue.

## 5. Evaluation

Your split (grouped by client? time-aware?) and why. Metrics, model vs baseline **on the same
split**. What the errors look like — a short error analysis beats a big metric table.

Ans. Week 5 used a random 80/20 train-test split with random_state=42. Week 6 additionally evaluated the model using a client-grouped split. This prevents pages from the same client showing up on both the training and testing sets and provides a better generalization on unseen data.
A third experiment was done removing impressions_last_30d and impressions_prev_30d to test leakage. 

Results: 
Random-split:
Accuracy: 0.9974433893352812
Precision: 0.9996746909564086
Recall: 0.9957874270900843
F1 score: 0.9977272727272727

Client-based split:
Accuracy: 0.9969512195121951
Precision: 0.998675057966214
Recall: 0.9953780125453945
F1 score: 0.9970238095238095

Leakage test split:
Accuracy: 0.5740176151761518
Precision: 0.5754994124559342
Recall: 0.646748101683724
F1 score: 0.6090471008860563

The near-perfect results from the first two experiments should not be treated as evidence of strong predictive performance. Removing the two potentially leakage-related features reduced performance substantially, indicating that they contained information closely related to the target. The leakage-reduced, client-grouped result is therefore used as the main basis for interpreting the final model.

The model should be evaluated against the Week 4 rule-based baseline using the same client-grouped test set. Because the baseline produces a ranked review list rather than a binary prediction, ranking-based measures such as Precision@20 and Precision@50 are more appropriate than comparing classification accuracy.

The overall proportion of test-set pages labelled as declining should also be reported alongside Precision@K. This provides the base rate needed to interpret whether the model is identifying declining pages at a useful rate rather than simply reflecting their frequency in the dataset.

Error Analysis- The final evaluation should examine false positives and false negatives. False positives represent pages ranked or classified as declining when they are not labelled as declining, while false negatives represent declining pages that the model fails to identify. These errors are important because the model is intended to support a human review queue rather than make an automatic refresh decision.


## 6. Interpretation

What the model/clusters actually found. Feature importances or cluster profiles in plain
words. Surprises and negative results — a well-understood "no effect" is a valid result.

Ans. The final model is intended to identify pages associated with the declining class and rank them for human review. The model should therefore be interpreted as a prioritisation tool rather than as evidence that a page definitely requires a content refresh.

The Logistic Regression coefficients provide an indication of which features are associated with higher or lower predicted probability of the declining class.

The main negative result was the substantial reduction in performance after impressions_last_30d and impressions_prev_30d were removed during the leakage audit. The initial near-perfect results were therefore not treated as reliable evidence of predictive performance. This demonstrates the importance of validating features that are closely related to how the target was constructed.

The final leakage-reduced model provides a more conservative basis for interpreting whether search and content-performance signals can support review prioritisation.

## 7. Recommendation

The ranked actions or decisions your output supports, and how a FlyRank editor would use them
tomorrow. State your confidence and the limits explicitly.

Ans. The model should be used to create a ranked content-refresh review queue rather than to automatically recommend that a page be refreshed.

A FlyRank editor can begin with the highest-ranked pages and review the model output alongside contextual signals such as search position, CTR, impressions, freshness, and search intent. This allows the editor to determine whether the observed performance indicates a potential content issue or whether another factor, such as poor search ranking or search intent mismatch, may explain the result.

## 8. Reproducibility

Ans. The project code and notebooks are maintained in the flyrank-ml repository. The capstone analysis is contained in the capstone notebook under work/notebooks/, with this report stored as work/capstone_report.md.

The final analysis uses Logistic Regression with a client-grouped train-test split and random_state=42. The leakage audit was performed by removing impressions_last_30d and impressions_prev_30d before the final evaluation.

The project outputs contain no client names, domains, private search queries, credentials, or other client-identifying information.

The final public paper will provide the repository and reproducibility information needed to reproduce the reported analysis.

The exact commands to re-run everything from a fresh clone, your random seeds, and your
environment (`pip freeze` highlights or `requirements.txt` deltas).

---

> **Claims checklist before submitting:** observed / measured / directional / decision-support
> **Metrics vs. base rate:** report your task's base rate (majority-class %) next to any
> precision@K or accuracy — a high score can just be a high base rate. AUC / lift over
> baseline are the honest discrimination numbers.
> language everywhere · no causal claims without an experiment or causal design · no
> "predicted Google's algorithm" · no client-identifying details · numbers in this report
> match a fresh re-run.
