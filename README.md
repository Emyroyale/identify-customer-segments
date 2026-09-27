# Identify Customer Segments: Who Should a Mail-Order Company Target?

**Unsupervised Machine Learning | Python, Scikit-learn, PCA, K-Means**

**In one sentence:** I looked at demographic data to figure out which *types* of people are most likely to become customers of a mail-order company, so its marketing budget can target more of the right people instead of mailing everyone equally.

**Full analysis notebook:** [Identify_Customer_Segments.ipynb](./Identify_Customer_Segments.ipynb)
**Rendered HTML export:** [Identify_Customer_Segments.html](./Identify_Customer_Segments.html)

---

## The Problem, in Plain English

A mail-order company sends catalogs and ads out to the general population, but not everyone is equally likely to buy. If you knew, for example, that "financially comfortable people in their 60s" convert into customers far more often than their share of the population would suggest, you'd want your next marketing dollar chasing more people like that — and fewer dollars chasing groups that almost never buy.

That's the whole question this project answers: **which kinds of people are over-represented among existing customers, and which are under-represented?**

## The Data

Two spreadsheets, each describing people using the same ~85 demographic characteristics (things like estimated income level, age generation, household/family type, and neighborhood characteristics):

1. **General population data** — a broad snapshot of the population at large.
2. **Customer data** — the same characteristics, but only for people who are already customers of the company.

> **Note on the data:** This data was licensed to Udacity by Bertelsmann Arvato Analytics for coursework use only. Per that license, the raw data files are **not included in this repository** — only the analysis notebook (with all outputs already computed) and the resulting charts.

## The Process, Step by Step

### Step 1 — Clean up the data

Real-world survey data has gaps: some questions get skipped, some records are incomplete. I standardized how "missing" was represented across all ~85 columns, then set aside the rows that were missing so much information they looked like a fundamentally different, less-documented group rather than just "noisy" data — keeping the main analysis focused on people we actually have good information about.

### Step 2 — Simplify ~148 features down to the handful that actually matter

After cleaning, categorical answers were expanded into around 148 individual yes/no and numeric features. That's too many to compare people on directly — many of them move together (e.g., income level and neighborhood density tend to correlate), so a lot of that information is redundant.

I used a technique called **PCA (Principal Component Analysis)** to find the small number of underlying *themes* that explain most of the real differences between people — similar to how "overall wealth" might quietly explain dozens of individual survey answers at once, without needing to look at each one separately.

<p align="center"><img src="./screenshots/pca_explained_variance.png" alt="PCA Cumulative Explained Variance" width="720"></p>

This compressed the data from ~148 features down to **80 themes**, while still keeping about **90% of the meaningful differences** between people. The two strongest themes turned out to be interpretable on their own:

- **Theme 1 — Wealth & living density:** affluent homeowners in lower-density areas vs. more urban, higher-mobility households.
- **Theme 2 — Generational values:** pleasure-seeking and financially indulgent vs. traditional, dutiful, and saving-oriented.

### Step 3 — Sort people into natural "types" (clusters)

With everyone now described by those 80 themes, I used **K-means clustering** to group people into a number of "types," where people within a type are similar to each other and different from people in other types — the same idea as sorting a pile of mixed items into bins by similarity, except here the computer finds the bins.

The open question was *how many* types make sense. I tried anywhere from 2 to 20, and measured how much better the grouping got each time:

<p align="center"><img src="./screenshots/kmeans_elbow_plot.png" alt="K-Means Score vs Number of Clusters" width="620"></p>

Adding more groups kept helping up through **10 groups**, after which each additional group barely improved things — a classic "diminishing returns" pattern. So **10 customer types** were kept as the final segmentation: enough to be meaningfully distinct, few enough to actually reason about.

### Step 4 — Compare customers against everyone else

Finally, I sorted the *existing customers* into those same 10 types (using the grouping rules learned only from the general population, to keep the comparison fair), then compared: what share of the general population falls into each type, versus what share of customers falls into each type.

<p align="center"><img src="./screenshots/cluster_proportions_comparison.png" alt="Cluster Proportions: General Population vs. Customers" width="720"></p>

If a type's customer share is much higher than its general-population share, that type of person converts into a customer far more often than average — exactly who marketing should chase more of.

## What We Found

- **Type 2 is the company's bullseye.** It makes up only **13.7%** of the general population but **37.4%** of existing customers — nearly 3x over-represented. This group's profile: an **older generation** (roughly people whose households formed in the 1960s), living in **established, moderately urban** neighborhoods, and financially comfortable.
- **Types 7 and 1** are also over-represented among customers, though less dramatically.
- **Types 6, 0, and 9** are the most **under-represented** — people who look like this almost never become customers.

## Why It Matters

For a real marketing budget, this is the direct, actionable output: **stop spending evenly across the whole population.** Concentrate acquisition spend on reaching more people who look like Type 2 (and to a lesser extent Types 7 and 1), and treat Types 6, 0, and 9 as low-priority — they're demographically unlikely to convert regardless of how much is spent reaching them.

## Tools & Libraries

Python, Pandas, NumPy, Scikit-learn (PCA, K-Means, StandardScaler, SimpleImputer, OneHotEncoder), Matplotlib, Seaborn
