# Emotion Analysis and Online Engagement

## Overview

This project investigates the relationship between emotions expressed in online comments and user engagement on FriendFeed.

A transformer-based emotion classification model is used to identify emotional patterns in comments. The detected emotion probabilities are aggregated at the entry level and analyzed against engagement indicators such as likes and comment activity.

The project combines Natural Language Processing (NLP), statistical analysis, and unsupervised machine learning to explore whether different emotional patterns are associated with different levels of online engagement.

---

## Project Objectives

The main objectives of this project are to:

- Detect emotions from online comments using a pretrained transformer model.
- Analyze the distribution of emotions across comments.
- Aggregate comment-level emotion probabilities into entry-level emotional profiles.
- Investigate relationships between emotions and engagement.
- Measure the statistical significance and effect size of emotion-engagement relationships.
- Identify natural emotional profiles using K-Means clustering.
- Compare engagement rates across different emotional clusters.

---

## Dataset

The project uses FriendFeed data containing:

- Comments
- Entries
- Likes

After preprocessing and matching comments with entries, the analysis produced:

- **110,336 emotion-classified comments**
- **91,071 unique entries**

The final analysis is performed primarily at the **entry level** to avoid counting the same entry's engagement multiple times.

### Engagement Characteristics

The engagement data is highly sparse:

- **93.03%** of entries received no likes.
- **6.97%** received at least one like.
- **95.76%** of entries contained exactly one analyzed comment.
- **4.24%** contained multiple comments.

Because of this imbalance, binary engagement indicators were also created:

- `has_like`: whether an entry received at least one like.
- `has_multiple_comments`: whether an entry contained more than one comment.

---

## Data Preprocessing

The preprocessing pipeline includes:

- Reading large comment files in chunks.
- Handling missing values.
- Removing unnecessary columns.
- Cleaning comment text.
- Removing empty comments.
- Removing duplicate comments.
- Detecting and retaining English comments.
- Matching comments with entry IDs.
- Connecting entry-level information with likes and comment counts.

Chunk-based processing was used to handle the large raw dataset efficiently.

---

## Emotion Detection

Emotion classification was performed using the pretrained transformer model:

`j-hartmann/emotion-english-distilroberta-base`

The model predicts probabilities for seven emotion categories:

1. Anger
2. Disgust
3. Fear
4. Joy
5. Neutral
6. Sadness
7. Surprise

Instead of using only the highest-probability emotion, the full emotion probability distribution was retained for later analysis.

Example:

| Emotion | Probability |
|---|---:|
| Anger | 0.02 |
| Disgust | 0.01 |
| Fear | 0.01 |
| Joy | 0.65 |
| Neutral | 0.19 |
| Sadness | 0.03 |
| Surprise | 0.09 |

This allows the analysis to capture mixed emotional signals rather than representing every comment with only one label.

---

## Entry-Level Emotion Profiles

Since engagement belongs to an entry rather than an individual comment, comment-level emotion probabilities were aggregated by `entry_id`.

For each entry, the mean probability of each emotion was calculated.

This produced one emotional profile for every unique entry:

`Entry → Average Emotion Probabilities → Engagement`

The final entry-level dataset contains **91,071 unique entries with no duplicate entry IDs**.

---

## Emotion Distribution

The dominant emotion distribution among classified comments was:

| Emotion | Percentage |
|---|---:|
| Neutral | 64.88% |
| Joy | 11.40% |
| Surprise | 8.02% |
| Sadness | 5.96% |
| Anger | 3.66% |
| Fear | 3.47% |
| Disgust | 2.63% |

Neutral comments represent the majority of the analyzed comments.

---

## Emotion and Engagement Correlation

Because the engagement variables are highly skewed, **Spearman rank correlation** was used.

| Emotion | Like Count | Comment Count |
|---|---:|---:|
| Anger | 0.115 | 0.138 |
| Disgust | **0.155** | **0.180** |
| Fear | 0.063 | 0.088 |
| Joy | 0.037 | 0.072 |
| Neutral | -0.054 | -0.075 |
| Sadness | 0.090 | 0.104 |
| Surprise | 0.015 | 0.063 |

Among individual emotion probabilities, **disgust showed the strongest positive association** with both likes and comment activity.

Neutral emotion showed a weak negative association with engagement.

Like count and comment count themselves showed a stronger positive Spearman correlation of approximately **0.531**.

These results describe associations and should not be interpreted as causal relationships.

---

## Statistical Analysis

The Mann-Whitney U test was used to compare emotion distributions between:

- Entries receiving no likes vs. entries receiving likes.
- Entries with one comment vs. entries with multiple comments.

Due to the large dataset, statistical significance alone can identify very small differences. Therefore, **rank-biserial correlation** was also calculated to measure effect size.

### Effect Size: Receiving Likes

| Emotion | Effect Size |
|---|---:|
| Disgust | **0.3492** |
| Anger | 0.2606 |
| Sadness | 0.2030 |
| Fear | 0.1426 |
| Neutral | -0.1250 |
| Joy | 0.0835 |
| Surprise | 0.0359 |

Disgust showed the largest effect for distinguishing entries that received likes from those that did not.

### Effect Size: Multiple Comments

| Emotion | Effect Size |
|---|---:|
| Disgust | **0.5129** |
| Anger | 0.3911 |
| Sadness | 0.2943 |
| Fear | 0.2487 |
| Neutral | -0.2146 |
| Joy | 0.2055 |
| Surprise | 0.1793 |

The strongest effect was again observed for disgust, particularly for entries containing multiple comments.

---

## K-Means Clustering

K-Means clustering was used to identify natural emotional profiles among entries.

Only the seven emotion probabilities were used as clustering features. Engagement variables were intentionally excluded so that the clusters represented emotional patterns independently of likes or comment activity.

### Selecting the Number of Clusters

Several values of `K` from 2 to 8 were evaluated using:

- Elbow Method
- Silhouette Score

The highest tested silhouette score was observed around:

**K = 6, Silhouette Score ≈ 0.574**

Therefore, six clusters were selected.

---

## Emotional Cluster Profiles

Clusters were labeled according to the emotion with the highest mean probability within each cluster.

| Cluster | Dominant Emotional Pattern |
|---|---|
| 0 | Neutral-dominant |
| 1 | Sadness-dominant |
| 2 | Anger-dominant |
| 3 | Surprise-dominant |
| 4 | Fear-dominant |
| 5 | Joy-dominant |

The Anger-dominant cluster also contained a relatively strong disgust component, showing that clusters can represent mixed emotional profiles.

---

## Engagement by Emotional Cluster

The engagement analysis revealed clear differences between emotional profiles.

| Emotional Cluster | Entries | Like Rate | Multiple Comment Rate |
|---|---:|---:|---:|
| **Surprise-dominant** | 10,365 | **11.57%** | **8.90%** |
| **Anger-dominant** | 6,126 | **10.38%** | **7.30%** |
| Joy-dominant | 10,221 | 8.85% | 4.80% |
| Neutral-dominant | 55,870 | 5.71% | 3.16% |
| Sadness-dominant | 5,152 | 5.05% | 3.12% |
| Fear-dominant | 3,337 | 4.70% | 2.25% |

### Key Observation

The **Surprise-dominant cluster showed the highest overall engagement**, followed by the Anger-dominant cluster.

The Fear-dominant cluster showed the lowest like rate and multiple-comment rate among the six clusters.

This clustering result complements the individual emotion analysis: statistical tests examine individual emotion probabilities, while clustering captures combinations of emotions forming broader emotional profiles.

---

## Key Findings

1. Neutral was the most common dominant emotion, representing approximately **64.88%** of classified comments.

2. Engagement was highly sparse, with approximately **93% of entries receiving no likes**.

3. Disgust showed the strongest positive Spearman association with both like count and comment count among the individual emotion probabilities.

4. Disgust also produced the largest rank-biserial effect sizes for receiving likes and having multiple comments.

5. Neutral emotion showed weak negative relationships with engagement.

6. K-Means identified six interpretable emotional profiles.

7. Surprise-dominant entries had the highest cluster-level engagement:
   - **11.57% like rate**
   - **8.90% multiple-comment rate**

8. Anger-dominant entries showed the second-highest engagement.

9. Emotion-engagement relationships observed in this project represent **associations rather than causal effects**.

---

## Technologies Used

- Python
- Pandas
- NumPy
- Matplotlib
- Scikit-learn
- SciPy
- PyTorch
- Hugging Face Transformers
- Jupyter Notebook

### Machine Learning / Statistical Methods

- Transformer-based Emotion Classification
- Spearman Rank Correlation
- Mann-Whitney U Test
- Rank-Biserial Correlation
- K-Means Clustering
- Elbow Method
- Silhouette Analysis

---

