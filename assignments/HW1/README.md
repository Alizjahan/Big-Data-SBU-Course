<p align="center">
  <img src="../../assets/header.jpg" alt="Big Data Analytics - HW1 Banner" width="100%">
</p>

# Assignment 1: MapReduce & Locality-Sensitive Hashing (LSH)

**Course:** Big Data Analytics (Fall 2026 / Semester 4051)  
**Institution:** Shahid Beheshti University (SBU), Faculty of Computer Science & Engineering  
**Student:** [Alireza Jahanbakhsh](https://github.com/Alizjahan) (Student ID: 404422206)  
**Instructor:** Dr. Mahboubeh Riahi  
**Teaching Assistant:** Tara Zare  

Language Navigation: **English** | [فارسی](README_FA.md)

---

## Directory Structure

```plaintext
assignments/HW1/
├── BigData_HW1_MapReduce.ipynb      # PySpark MapReduce distributed rating pipeline
├── Big_Data_HW1_LSH.ipynb           # Random-Hyperplane LSH on clinical sentence embeddings
├── README.md                        # English assignment documentation
├── README_FA.md                     # Persian assignment documentation
└── report/
    ├── HW1_BigData_AlirezaJahanbakhsh.pdf  # Comprehensive academic report (XeLaTeX, 11 pages)
    ├── HW1_BigData_AlirezaJahanbakhsh.tex  # XeLaTeX source code
    └── images/                             # University insignia and figures
```

---

## Part 1: Distributed Computing with MapReduce (PySpark)

Notebook: `BigData_HW1_MapReduce.ipynb`

### Overview
This section implements a distributed pipeline using Apache Spark RDD transformations to compute exact movie rating statistics from MovieLens records (`users.txt` and `movies.txt`).

### Pipeline Stages
1. **Map Phase:** Line parsing transforms raw rating lines into key-value pairs: `(movie_id, (rating, 1))`. Parsing supports standard tab separation as well as whitespace fallback, with rating validation in `[0, 5]`.
2. **Reduce Phase:** Aggregation via `reduceByKey` combines partial statistics using associative tuple addition: `(sum_left + sum_right, count_left + count_right)`.
3. **Average Computation:** RDD transformation via `mapValues` determines exact arithmetic means: `stat[0] / stat[1]`.
4. **Eligibility Filtering & Extremes:** Filters films with $\ge 5$ ratings ($1,349$ out of $1,682$ rated titles):
   - **Minimum Average ($1.0000$):** Tied across 3 movies (IDs: 314, 437, 439), each with exactly 5 ratings.
   - **Maximum Average ($4.6250$):** *Pather Panchali (1955)* (ID: 1449) with 8 ratings.

---

## Part 2: High-Dimensional Similarity Search with LSH

Notebook: `Big_Data_HW1_LSH.ipynb`

### Overview
Implementation of angular Locality-Sensitive Hashing using Random Hyperplanes for patient complaint sentence embeddings generated via `sentence-transformers/all-mpnet-base-v2` ($d = 768$, $N = 25,971$ documents).

### Technical Specifications
- **Hyperplanes Configuration:** $L = 25$ independent hash tables, each constructed with $n = 11$ random planes ($2^{11} = 2,048$ possible buckets per table).
- **Hash Function Formulation:** Computes binary signs of projections: $h(v) = \sum_{i=0}^{n-1} \mathbf{1}_{\{v \cdot r_i \ge 0\}} 2^i$.
- **Search Space Reduction:** Filters candidates matching query buckets across tables, reducing the search space from $25,971$ to $843$ candidate documents ($3.25\%$ candidate fraction).
- **Empirical Speedup:** Achieves over $10\times$ acceleration relative to brute-force exact search, retrieving top semantically relevant clinical diagnoses with verified $\text{Recall}@3 = 33.3\%$.

---

## Theoretical Report & Mathematical Derivations

Complete technical derivations and empirical analyses are documented in `report/HW1_BigData_AlirezaJahanbakhsh.pdf`:

1. **Two-Stage MapReduce Matrix Multiplication:** Detailed key-value schemas, grouping mechanics, and end-to-end evaluation for non-square matrices $A_{2 \times 3} \times B_{3 \times 2}$.
2. **Relational Algebra in MapReduce:** Specification of Set Union ($R_1 \cup R_2$) and Reduce-Side Natural Join ($U \bowtie R_3$).
3. **MinHash & Jaccard Similarity:** 2-shingle characteristic matrix, signature matrix computation across random permutations, and variance bounds $\mathcal{O}(1/\sqrt{K})$.
4. **LSH Parameter Optimization:** Operational analysis for an automated news monitor ($K=18$, $b \times r = 18$) under probabilistic constraints, establishing $(b=6, r=3)$ as the optimal configuration.
