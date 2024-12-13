# PertussisReBoost
Predicting the response to the pertussis booster vaccine as part of the Computational Models of Immunity (CMI-PB) 3rd challenge.<br>
https://www.cmi-pb.org/

We develop algorithms that use a patient's pre-vaccination data to predict their post-vaccination response.
* *Harmonized and Processed Data* contains the training data from 2020-2022 and the testing data from 2023.
* *Code* contains the Mathematica notebook used for this analysis and the resulting prediction file. 
<br><br>
---
Lessons learned:
* In all tasks, the goal was to find features that could *robustly* predict responses in other seasons. Robustness was assessed by testing not only the predictive power against the feature-of-interest (e.g. the IgG_PT antibody feature in Task 1), but against all features within this class (e.g. all antibody features in Task 1).
    * We only used Day 0 data (not Day -30 and Day -14, which were only measured in 2022 and 2023).
    * Predicted across every possible pair of studies (2020→2021 and 2021→2020 for training, 2020+2021→2022, 2021+2022→2020, 2022+2020→2021 for validation). We always compute Spearman's coefficient and average the two results to evaluate each model.
* Task 1.1: Using normalized data, searched through all features to add to the baseline model (using IgG_PT).
    * Final Random Forest model uses IgG_PT and YearsSinceVaccination.
    * 2021 made flat predictions, so that neither helps nor hurts the model. Thus, only 2020 and 2022 contributed to the final predictions. There were multiple ties, which were all broken with arbitrary order (which should likely minimally affect the result).
* Task 1.2: Raw data works better than normalized data.
    * Surprisingly, no feature worked consistently better than the baseline model, 1/(day 0), which was our final answer.
* Task 2.1: Similar to task 1.1, looked for features that robustly improved predictions across all cell type features.
    * The baseline model already does well, so it was easy to build upon it.
* Task 2.2: Note that a null model (using baseline only) does poorly, making this a much harder challenge.
    * Used batch-corrected data, with the baseline feature being "Proliferating B cells," since that had the best correlation with Monocyte fold-change.
    * It was difficult to decide which other features are good, since averaging across all cell type features does very poorly. Ended up picking out the best 7 features that were well-predicted by Proliferating B cells to get a measure of robustness.
    * The Monocytes feature did not make things worse or better. It was decided to add it back in.
* Task 3.1: Unclear how to do a robust search with so many genes, so went with the simple model of using the baseline gene expression for that gene.
* Task 3.2: Looked for which features had the best correlation with the gene-of-interest's fold-change. Top hits were "IgG2_FHA" and "IgG2_DT", although neither did well in 2020 (but both were good in 2021-2022). Ended up using "IgG2_FHA."
* Task 4.1: Baseline model worked well, but many other features robustly made all T cell polarization features better (not looking at their ratios, just predicting each one outright).
  * Chose the single best predictive parameter ("CD3-CD19-CD56-CD14-CD16-CD123-CD11c-HLA-DR+cells") and used it in a random forest model together with the baseline parameter (ratio of the two features of interest).