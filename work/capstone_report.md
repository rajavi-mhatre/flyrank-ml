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

Ans. Can search and content-performance signals help identify pages that should be prioritized for content-refresh review?
Unit of analysis: Individual page record
Decision: Should X page be flagged for review?
Output: Ranked review list
Human Action: Review flagged pages
Cost of inaccurate flagging: Time spent on pages that didn't really require any flagging. 

Why does Data Science/Machine Learning help here? Data Science and Machine Learning can combine multiple signals to provide a consistently ranked pages.

## 2. Data safety

Which data you used and which columns you deliberately excluded (and why). Leakage risks you
considered — especially label-derived fields (`trend_direction`, `trend_pct`) and pseudonymous
IDs (grouping only, never features). Confirm nothing client-identifying appears anywhere in
`work/`.

Ans. Original rows in the dataset: 'content_id', 'client_id', 'search_volume', 'competition',
       'competition_level', 'cpc', 'content_type', 'main_intent', 'word_count',
       'char_count', 'provider_used', 'model_used', 'impressions_90d',
       'clicks_90d', 'pageviews_90d', 'sessions_90d', 'users_90d',
       'engaged_sessions_90d', 'ai_sessions_90d', 'scroll_events_90d',
       'days_with_impressions', 'days_with_sessions', 'impressions_last_30d',
       'clicks_last_30d', 'sessions_last_30d', 'impressions_prev_30d',
       'clicks_prev_30d', 'sessions_prev_30d', 'content_age_days', 'age_tier',
       'age_tier_order', 'days_since_last_update', 'freshness_tier',
       'word_count_tier', 'char_count_tier', 'ctr', 'avg_position',
       'engagement_rate', 'scroll_rate', 'ai_traffic_pct', 'impression_tier',
       'position_tier', 'trend_direction', 'trend_pct'

Excluded rows:  'content_id', 'client_id', 
       'competition_level', 'cpc', 'content_type', 'main_intent', 'word_count',
       'char_count', 'provider_used', 'model_used', 'age_tier',
       'age_tier_order',  'freshness_tier',
       'word_count_tier', 'char_count_tier', 'impression_tier',
       'position_tier', 'trend_direction', 'trend_pct'

Finalized feature set: 'search_volume', 'competition' , 'impressions_90d',
   'clicks_90d', 'pageviews_90d', 'sessions_90d',
   'users_90d', 'engaged_sessions_90d', 'ai_sessions_90d',
   'scroll_events_90d', 'days_with_impressions', 'days_with_sessions',
   'impressions_last_30d', 'clicks_last_30d', 'sessions_last_30d',
   'impressions_prev_30d', 'clicks_prev_30d', 'sessions_prev_30d',
   'content_age_days', 'days_since_last_update',
   'ctr', 'avg_position', 'engagement_rate', 'scroll_rate',
   'ai_traffic_pct'

Features = 'search_volume', 'competition' , 'impressions_90d',
   'clicks_90d', 'pageviews_90d', 'sessions_90d',
   'users_90d', 'engaged_sessions_90d', 'ai_sessions_90d',
   'scroll_events_90d', 'days_with_impressions', 'days_with_sessions',
   'impressions_last_30d', 'clicks_last_30d', 'sessions_last_30d',
   'impressions_prev_30d', 'clicks_prev_30d', 'sessions_prev_30d',
   'content_age_days', 'days_since_last_update',
   'ctr', 'avg_position', 'engagement_rate', 'scroll_rate',
   'ai_traffic_pct'

Target = A binary label where 'declining'=1 when 'trend_direction'=='down' and 0 otherwise. 

## 3. Baseline

The transparent rule or score you built first. Why it's a fair comparison, and its numbers on
the same data and metric as your model.

## 4. Model / analysis

Your method and why it fits the lane. The exact feature list (and what you left out on
purpose). The target or proxy definition, in one sentence.

## 5. Evaluation

Your split (grouped by client? time-aware?) and why. Metrics, model vs baseline **on the same
split**. What the errors look like — a short error analysis beats a big metric table.

## 6. Interpretation

What the model/clusters actually found. Feature importances or cluster profiles in plain
words. Surprises and negative results — a well-understood "no effect" is a valid result.

## 7. Recommendation

The ranked actions or decisions your output supports, and how a FlyRank editor would use them
tomorrow. State your confidence and the limits explicitly.

## 8. Reproducibility

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
