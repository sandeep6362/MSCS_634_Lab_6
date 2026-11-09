# MSCS 634 – Lab 6: Association Rule Mining with Apriori and FP-Growth

**Name:** Sandeep Reddy Banda
**Course:** MSCS 634 – Advanced Big Data and Data Mining

## Purpose of the Lab

The goal of this lab was to practice association rule mining on real transactional data. I used the **Online Retail dataset** from the UCI Machine Learning Repository, where every invoice is treated as a basket of products. I mined frequent itemsets with both the **Apriori** and **FP-Growth** algorithms (using `mlxtend`), generated association rules with support, confidence, and lift, and used **Seaborn** to visualize the results. At the end I compared the two algorithms on output and speed.

## Files in this Repository

- `MSCS_634_Lab_6.ipynb` – the Jupyter Notebook with code, outputs, visualizations, and written analysis
- `README.md` – this file

The notebook loads `Online Retail.xlsx` from the same folder if it is there. If not, it downloads it from the UCI repository. It needs `pandas`, `numpy`, `matplotlib`, `seaborn`, `mlxtend`, and `openpyxl`.

## What I Did

1. **Data preparation:** Loaded 541,909 rows and removed missing descriptions, cancelled invoices, zero or negative quantities and prices, and non-product rows such as postage and bank charges. I kept only United Kingdom invoices, which left 17,901 invoices and 3,988 items, and turned them into a one-hot encoded basket matrix. I made a barplot of the most frequent items, a basket size histogram, and a Seaborn heatmap of item co-occurrence.
2. **Apriori:** Ran Apriori with a minimum support of 0.02 and plotted the top itemsets.
3. **FP-Growth:** Ran FP-Growth with the same support threshold, confirmed it gives the same itemsets, and compared runtimes (including a sweep over several support values).
4. **Association rules:** Generated rules with a minimum confidence of 0.5 and looked at support, confidence, and lift, with a confidence vs. lift scatter plot, a support vs. confidence plot, a top-rules barplot, and a lift heatmap.
5. **Comparison:** Compared Apriori and FP-Growth on results and speed, and wrote up the challenges.

## Key Insights

- Both algorithms found the **same 399 frequent itemsets** (300 single items, 94 pairs, and 5 triples) and the **same 75 association rules** at a support of 0.02 and a confidence of 0.5.
- All 75 rules have a lift above 1, with a median lift of about 11.7. The strongest rule by lift is **Wooden Heart Christmas Scandinavian → Wooden Star Christmas Scandinavian** (support 0.021, confidence 0.72, lift 27.0).
- The biggest group of rules is about the **Regency teacup and saucer** range. For example, Pink + Roses teacup → Green teacup has a confidence of about 0.90 and a lift of about 17, so customers seem to buy the colors as a set.
- The jumbo bag rules have a high confidence (about 0.80) but a lower lift (about 7.4), because the Jumbo Bag Red Retrospot is very popular on its own. This showed me that lift is a better measure than confidence for judging how strong a rule is.
- **Apriori was faster than FP-Growth** on my data: about 1.3 seconds vs. 1.8 seconds at a support of 0.02, and Apriori was faster at every threshold in my sweep (0.05 to 0.02). The gap got smaller as the support got lower. After removing items below the support threshold only 300 items and short itemsets were left, so Apriori had few candidates to deal with and FP-Growth's tree building did not pay off. I would still expect FP-Growth to scale better on bigger data or lower thresholds.
- Possible business uses: "customers also bought" suggestions, product bundles (like the teacup colors), and keeping linked items in stock together.

## Challenges and Decisions

- **Messy data:** Cancellations, returns, missing descriptions, and non-product rows had to be removed so they would not show up as fake "frequent items".
- **Memory:** My first basket matrix used `pivot_table`, which crashed the kernel, so I filled a NumPy True/False array directly. Apriori also used a lot of memory on all 3,988 items, so I dropped items that appear in fewer invoices than the support threshold before mining (this does not change the results).
- **Support threshold:** A high threshold gave almost nothing, and 0.015 made Apriori run out of memory, so I used 0.02 and stopped the sweep there.
- **Library versions:** `association_rules` in `mlxtend` takes different arguments in newer versions, so I added a small wrapper that works with both.
- **Readability:** Itemsets are stored as frozensets, so I converted them to text labels for tables and plots.
- **Fair comparison:** I used the same basket matrix and the same support threshold for both algorithms and averaged the runtime over 5 runs.
