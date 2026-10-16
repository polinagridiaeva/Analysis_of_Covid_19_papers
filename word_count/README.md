# Task 1 — Distributed Word Count

This notebook computes word frequencies over the full text of the CORD-19 corpus using a MapReduce algorithm implemented with Apache Spark RDDs, and benchmarks its performance across different cluster configurations.

**Notebook:** `1_word_count.ipynb`
**Input:** CORD-19 JSON parses, filtered by `directory.csv` (128 782 unique papers, output of Task 0)

## Method

1. **Text extraction.** For each paper, all paragraphs of `body_text` are joined into a single string.
2. **Cleaning.** Text is lower-cased and only alphabetic tokens of at least three letters are kept (`[a-z]{3,}`). Stopwords are removed using a combined list of:
   - NLTK English stopwords;
   - LaTeX commands that appear in papers parsed from LaTeX source;
   - a few frequent words without scientific meaning (*also*, *may*, *however*, *one*, *two*).
3. **MapReduce.**
   - **Map:** each token *w* is mapped to the pair (*w*, 1);
   - **Reduce:** `reduceByKey` sums the counts of each word across the whole corpus.

```python
paths_rdd = sc.parallelize(all_path, numSlices=48)
words_rdd = paths_rdd.map(extract_text).flatMap(clean_text)
word_counts = words_rdd.map(lambda w: (w, 1)).reduceByKey(lambda a, b: a + b)
```

## Results

The corpus contains **1 346 984 distinct words**. The 20 most frequent are:

| Word | Count | Word | Count |
|---|---|---|---|
| patients | 1 508 675 | cell | 666 293 |
| cells | 1 100 521 | sars | 630 369 |
| virus | 911 869 | health | 628 576 |
| infection | 863 801 | viral | 628 484 |
| covid | 858 418 | time | 626 668 |
| study | 835 667 | cov | 610 630 |
| data | 791 011 | protein | 603 308 |
| using | 751 497 | results | 548 251 |
| used | 738 396 | cases | 542 228 |
| disease | 736 190 | studies | 526 864 |

The most frequent terms reflect the clinical (*patients*, *disease*, *cases*), virological (*virus*, *infection*, *sars*, *cov*) and molecular (*cells*, *protein*) focus of the corpus.

## Benchmarking

Execution time was measured for different numbers of partitions (8, 12, 24, 48, 72, i.e. from *x*/3 to 3*x* with *x* = 24 cores) and three executor configurations with the same total of 24 cores: 3 × 8, 6 × 4 and 12 × 2. Each setting was run 5 times. The first run, which also includes reading and parsing the JSON files, was excluded as a cold-start outlier.

Mean execution time for the 3 executors × 8 cores configuration:

| Partitions | 8 | 12 | 24 | 48 | 72 |
|---|---|---|---|---|---|
| Time (s) | 32.8 | 23.5 | 14.6 | 14.5 | 15.2 |

With fewer partitions than cores, part of the cluster stays idle and the computation is about twice as slow. The best performance is reached when the number of partitions is equal to or twice the number of cores; beyond that, the scheduling overhead of additional tasks starts to outweigh the gain. The comparison across all three executor configurations is shown in the plot at the end of the notebook.
