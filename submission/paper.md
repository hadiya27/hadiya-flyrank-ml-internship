---
layout: default
title: Content Performance Prioritization Using Machine Learning
---
# Content Performance Prioritization Using Machine Learning

## 1. Title and Abstract

### Content Performance Prioritization Using Machine Learning

**Abstract**

This project investigates whether available content-performance signals can support a practical system for prioritizing content for review. The analysis uses content, search, and engagement-related features to create a decision-support workflow that ranks content according to observable performance signals.

The project combines exploratory analysis, data-quality checks, feature selection, a classification model, and a rule-based prioritization approach. The final workflow produces ranked recommendations together with reason codes and suggested actions. Results are treated as directional observations rather than proof of causal relationships or claims about how a search engine ranking system works.

The analysis suggests that a simple prioritization framework can identify content that may deserve additional review, while also showing important limitations related to data availability, potential leakage, and the observational nature of the data.

---

## 2. Introduction / Problem Statement

Large content datasets can contain many pages that require different levels of attention. Reviewing every page manually is inefficient, so a useful decision-support system should help identify which content may deserve attention first.

The objective of this project is to develop and evaluate a reproducible workflow for prioritizing content using available performance and content signals.

The project asks:

> **Can observable content, search, and engagement signals be used to create a useful ranked queue for content review?**

The goal is not to predict or explain Google's ranking algorithm. Instead, the goal is to create a practical prioritization framework based on the available dataset.

The analysis focuses on signals such as search volume, word count, click-through rate (CTR), average search position, engagement rate, content age, and update recency.

---

## 3. Data

The project uses structured content-performance data containing content-level and performance-related fields.

Relevant variables include:

* Search volume
* Competition-related measures
* Content type
* Search intent
* Word count
* Click-through rate (CTR)
* Average position
* Engagement rate
* Content age
* Days since last update
* Search and analytics data-availability indicators

The March data contained **9,841,378 rows**.

The data-quality checks showed:

* **3,611,061 rows** had GSC data available.
* **413,966 rows** had GA4 data available.
* GSC availability was approximately **36.7%**.
* GA4 availability was approximately **4.2%**.

These availability differences are important because not every record contains the same performance signals.

The analysis therefore treats missing or unavailable measurement data as an important limitation rather than assuming that the absence of a signal means the underlying behavior did not occur.

---

## 4. Methodology

The workflow followed several stages.

### 4.1 Data and task framing

The first stage defined the decision problem as content prioritization rather than attempting to explain search-engine ranking behavior.

The intended output was a ranked queue that could help a human reviewer decide which content should receive attention.

### 4.2 Feature selection

Features considered for decision support included:

* Search volume
* Word count
* CTR
* Average position
* Engagement rate

Additional content-age and update-recency variables were also examined during the modeling workflow.

Feature selection was based on their relevance to the decision problem and their availability in the dataset.

### 4.3 Exploratory analysis

The analysis examined relationships between content and performance variables.

One earlier correlation check between search volume and impressions over the 90-day period produced a correlation of approximately **0.001**.

This was treated as an observed relationship in this dataset and not as evidence that search volume has no relationship with traffic in general.

### 4.4 Modeling

A decision-tree classification approach was tested as part of the prioritization workflow.

The modeling process used a constrained tree rather than an unrestricted model so that the resulting decision process would remain relatively interpretable.

The model was evaluated against a simpler hand-defined prioritization rule using Precision@K.

### 4.5 Ranking and recommendation

The final workflow converts model and rule signals into a ranked review queue.

The resulting queue contains:

* A priority score
* A reason code
* A recommended action

Observed reason codes included:

* `LOW_CTR_POSITION`
* `REVIEW`
* `STALE_CONTENT`

Observed actions included:

* `MONITOR`
* `REVIEW`
* `PRIORITIZE_REVIEW`

This structure is intended to make the output useful for human decision-making rather than treating the model prediction as an automatic final decision.

---

## 5. Results

The evaluation compared the hand-defined prioritization rule with the decision-tree approach.

The observed Precision@20 results were:

| Approach      | Precision@20 |
| ------------- | -----------: |
| Hand rule     |        0.900 |
| Decision tree |        0.550 |

The observed Precision@50 results were:

| Approach      | Precision@50 |
| ------------- | -----------: |
| Hand rule     |        0.680 |
| Decision tree |        0.600 |

In this evaluation, the hand-defined rule performed better than the tested decision tree at both evaluated cutoffs.

This suggests that the more complex model did not automatically provide better top-ranked recommendations in this experiment.

The final ranked queue contained **119,007 rows**.

The observed reason-code distribution was:

| Reason code      |  Count |
| ---------------- | -----: |
| LOW_CTR_POSITION | 73,207 |
| REVIEW           | 45,753 |
| STALE_CONTENT    |     47 |

The observed action distribution was:

| Action            |  Count |
| ----------------- | -----: |
| MONITOR           | 74,861 |
| REVIEW            | 43,992 |
| PRIORITIZE_REVIEW |    154 |

These results show that the workflow can transform available signals into a structured prioritization queue.

However, the results should be interpreted as directional observations from this dataset and evaluation setup.

---

## 6. Limitations and Honest Framing

Several limitations affect interpretation.

### Observational data

The analysis uses observational data. Relationships between variables should not be interpreted as causal effects.

### Search-engine interpretation

The project does not prove how Google's search algorithm works and does not attempt to reverse-engineer Google's ranking system.

### Data availability

GSC and GA4 data were not available for all rows. In particular, GA4 availability was substantially lower than GSC availability.

### Potential leakage

Potential target leakage was considered during the modeling workflow. Features that could directly reveal or closely encode the outcome must be treated carefully because they can make model performance appear stronger than it would be in a real deployment setting.

### Model performance

The decision tree did not outperform the simpler hand rule at the tested Precision@20 and Precision@50 levels.

Therefore, model complexity should not be treated as automatically superior to a transparent rule-based approach.

### Generalization

The findings come from the available dataset and evaluation setup. They should not automatically be generalized to all websites, industries, clients, or future periods.

---

## 7. Ranked Recommendations

Based on the observed results, the following recommendations are prioritized.

### 1. Keep a transparent prioritization baseline

The hand-defined rule produced higher Precision@20 and Precision@50 than the tested decision tree. A simple rule should therefore remain an important baseline for comparison.

### 2. Prioritize content showing low CTR relative to position

`LOW_CTR_POSITION` was the most common reason code in the final queue. This makes it a useful candidate signal for human review.

The result should be interpreted as a prioritization signal, not proof that changing a page will cause improved rankings or traffic.

### 3. Use human review for high-priority recommendations

The ranked queue should support human decision-making rather than automatically changing content.

Reviewers can investigate the underlying page, search intent, content quality, freshness, and business relevance before taking action.

### 4. Improve measurement coverage

The large difference between GSC and GA4 availability suggests that improving data coverage could strengthen future analyses.

### 5. Continue testing against holdout data

Future versions should evaluate the prioritization workflow on data that was not used during development. This would provide stronger evidence about whether the observed ranking performance generalizes.

---

## 8. Reproducibility

The project was developed as a reproducible notebook-based workflow.

The analysis was performed using the provided internship repository and notebook environment.

The workflow includes:

1. Data inspection and validation
2. Data availability checks
3. Task and feature framing
4. Exploratory analysis
5. Model development
6. Evaluation
7. Ranking
8. Reason-code generation
9. Recommended actions
10. Limitations and honest interpretation

The notebook was checked by running the completed workflow from beginning to end.

The published project should not contain client names, private URLs, private search queries, or other confidential information.

The repository provides the underlying project structure and executed notebook required to reproduce the analysis within the permitted dataset and environment.

---

## 9. Acknowledgments and Data Credit

This work was completed as part of the FlyRank ML internship.

The analysis uses the data and project structure provided for the internship assignment.

The author acknowledges the internship materials, datasets, and instructions that supported the development of this project.

All findings in this paper are presented as observations from the analysis performed and should not be interpreted as claims about search-engine algorithms or causal effects.

---

## Conclusion

This project demonstrates a practical approach to using machine learning and rule-based signals for content prioritization.

The tested decision tree provided a structured modeling approach, but the simpler hand-defined rule achieved stronger Precision@20 and Precision@50 in the observed evaluation. This reinforces the value of transparent baselines and careful evaluation before introducing additional model complexity.

The resulting ranked queue can support human review by identifying content associated with observable performance signals and providing reason codes and suggested actions.

Future work should focus on stronger holdout validation, improved data coverage, continued leakage checks, and testing whether the prioritization approach remains useful across different time periods and datasets.
