# **README — Twitch Streamer-User Interaction Recommender System**
By: Ansh Bhatnagar, Pranav Singh, Jeronimo Adames-Baena

---

### Data Citation
Recommendation on Live-Streaming Platforms: Dynamic Availability and Repeat Consumption
Jérémie Rappaz, Julian McAuley and Karl Aberer
RecSys, 2021

1. [Data Folder](https://mcauleylab.ucsd.edu/public_datasets/gdrive/twitch/): full_a.csv.gz contains the full dataset while 100k.csv is a subset of 100k users for benchmark purposes
2. [Code](https://www.google.com/url?q=https://github.com/JRappaz/liverec&source=gmail-html&ust=1629428377164000&usg=AFQjCNFEGBZtyMwP3RJu-w8IWbubR8MAjw) for reference.

---

# **Part 1: Sequential Next-Streamer Prediction**

The goal of this task is to **predict which streamer a user will watch next given their historical viewing sequence**. 
Twitch consumption is inherently sequential, with users navigating between streamers over time depending on interest, availability, and social dynamics (trends). Understanding next-watch behavior is central to real-time recommendation systems such as Twitch’s “Recommended Channels” and TikTok/Reels video reccomendation algorithms.

Our methodology includes:
1. **Data preprocessing and sessionization:** 
   * Construction of chronological user sequences
   * Removal of extremely short sessions
   * Grouping of interactions into longer viewing periods
2. **Baseline models:**
   * Global streamer popularity
   * User’s historically most-watched streamer
   * Random predictor
3. **Collaborative filtering approaches:**
   * Item-item similarity models computed via co-viewership
   * Jaccard similarity between watcher sets
   * KNN recommendations based on last-watched streamer
4. **Sequence modeling:**
   * First-order Markov chains estimating transition probabilities ( P(s' \mid s) )
5. **Hybrid model:**
   Weighted blend of CF-based similarity scores and sequential transition scores, with mixture weight tuned using validation data.
6. **Evaluation:**
   Hit Rate @ K, MRR, and Accuracy@1 on held-out next-step predictions.


# **Part 2: Streamer Revisit Prediction (Implicit Return Prediction)**

This focuses on **predicting whether a user will return to a given streamer within a fixed future time window**. This experiment models longer-term engagement and user affinity. This framing mirrors industrial recommender tasks such as retention prediction, personalized ranking, and implicit “like/dislike” estimation.

We treat this as a **binary classification task** derived from implicit data. The approach includes:

1. **Label construction:**
   For each (user, streamer) pair with at least one interaction, define a return label based on whether a subsequent interaction occurs within a chosen prediction window.
2. **Feature engineering:**
   * Frequency of past interactions
   * Total and average session duration
   * Recency of last interaction
   * Streamer popularity and activity level
   * Overlap between streamer and user viewing profiles
   * Item–item similarity between the streamer and the user’s other viewed streamers
3. **Baselines:**
   * Streamer-level popularity predictor
   * User’s top historically consumed streamers
   * Simple collaborative filtering heuristics (e.g., average of user and streamer biases)
4. **Models:**
   * Logistic Regression using content and collaborative features
   * Item-based collaborative filtering via implicit co-viewing patterns
   * Hybrid ensemble blending logistic and CF-based scores
5. **Evaluation:**
   ROC-AUC, Precision@K, Recall@K, and accuracy on a stratified test set.

# **Part 3: Streamer Similarity Modeling and Community Discovery**

Streamer similarity is a foundational component of item-based recommendation systems. The objective of this task is to **identify groups of streamers that share similar audiences and to build a recommender that suggests streamers a user is likely to enjoy based on these similarity relationships**. 

This task is structured as a **similarity estimation and clustering problem** using implicit signals. The methodology includes:
1. **Co-viewership matrix construction:**
   Construction of a streamer–streamer matrix where entry ( M_{ij} ) reflects the number of users who watched both streamers ( i ) and ( j ).
2. **Similarity computation:**
   * Jaccard similarity between viewer sets
   * Cosine similarity based on co-view counts
   * Optional SVD-based latent embeddings or item2vec for compact representations
3. **Clustering:**
   * k-means or spectral clustering on embedded representations
   * Community identification using graph-based methods
4. **Recommendation procedure:**
   For a user’s top streamers, recommend new streamers from the same similarity cluster or based on nearest-neighbor similarity.
5. **Evaluation:**
   * Qualitative inspection of clusters
   * Quantitative metrics such as intra-/inter-cluster similarity
   * Downstream performance on a small recommendation task (e.g., recommending held-out streamers a user eventually watched)

This demonstrates how structural patterns in implicit viewing data can reveal latent content communities and supports item-centric recommendation strategies.
