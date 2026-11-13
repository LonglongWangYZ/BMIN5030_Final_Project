# BMIN 5030 Final Project — Gene Expression in Coronary Artery Disease
Yizhou Wang, Nov 2026

This exploratory project compares whole-blood gene expression in people with coronary artery disease (CAD) and healthy controls using public Agilent microarray data. The analysis will examine expression patterns, test for differential expression, and assess how well a simple classifier distinguishes cases from controls within this cohort.

## Research questions

1. Do overall expression patterns differ between CAD cases and healthy controls?
2. Which microarray probes show differential expression after controlling the false discovery rate, and which genes do they map to?
3. How well does an expression-based classifier distinguish CAD cases from controls on held-out samples from this cohort?

## Data source

GSE42148 contains human whole-blood expression profiles measured on the Agilent SurePrint G3 Human GE 8×60K microarray (GPL13607). The planned analysis starts with the submitter-processed Series Matrix and uses GEO sample annotations to verify case and control labels.

- [NCBI GEO series GSE42148 and sample records](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE42148)
- [Processed Series Matrix download](https://ftp.ncbi.nlm.nih.gov/geo/series/GSE42nnn/GSE42148/matrix/GSE42148_series_matrix.txt.gz)
- [GPL13607 platform and probe annotation](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GPL13607)
- [Supplementary files, including raw Agilent data](https://ftp.ncbi.nlm.nih.gov/geo/series/GSE42nnn/GSE42148/suppl/)

## Workflow

1. **Data preparation.** Import the Series Matrix in R with `GEOquery`. Match expression columns to metadata by GEO sample accession. Verify CAD and control labels, record inclusion criteria and retained sample counts, and check the documented normalization and expression scale before applying any transformations. Map probe identifiers to genes using GPL13607 and document how ambiguous mappings and multiple probes per gene are handled.
2. **Quality control and exploratory analysis.** Check for missing values, duplicate sample IDs, unusual expression distributions, and outliers. Review available sample covariates and batch annotations that may affect interpretation.
3. **Principal component analysis (PCA).** Use PCA to visualize major sources of variation across samples. Annotate plots by CAD status and, where available, relevant technical or biological variables.
4. **Differential expression.** Use `limma` to fit probe-level linear models for CAD versus healthy controls and obtain empirical Bayes moderated statistics. Report log2 fold changes and Benjamini–Hochberg FDR-adjusted p-values.
5. **Heatmap and clustering.** Apply hierarchical clustering to a heatmap of variable probes selected without using case/control labels. Annotate samples by group to inspect whether expression patterns align with CAD status.
6. **Exploratory classification.** Compare penalized logistic regression with a majority-class baseline using stratified cross-validation. Fit all data-dependent preprocessing and feature selection within each training fold, including any expression-based probe ranking. If tuning is used, perform it in inner folds and score predictions on the held-out outer folds. Summarize balanced accuracy and ROC AUC with their variability across repeated splits where feasible.

## Repository files

- [`README.md`](README.md) — Project scope, data links, and analysis plan.
- [`final_project.qmd`](final_project.qmd) — Quarto report template with initial project notes and sections for methods, results, and conclusions.
- [`.gitignore`](.gitignore) — Excludes local R and RStudio files.

## Reproducibility

1. Install R and Quarto, then open the repository root as your working directory. Install the planned analysis and rendering packages in R.

   ```r
   install.packages(c("BiocManager", "rmarkdown"))
   BiocManager::install(c("GEOquery", "limma"))
   ```

2. Retrieve GSE42148 with `GEOquery` or download the Series Matrix from GEO. Record the accession, retrieval date, sample IDs and group assignments, filtering choices, and preprocessing decisions in the report.
3. Add the analysis code to the Methods and Results sections of `final_project_template.qmd`. Record any additional package versions and the random seed for cross-validation, and include `sessionInfo()` in the report.
4. Render the HTML report from the repository root after adding the analysis code.

   ```bash
   quarto render final_project_template.qmd
   ```

The Quarto file currently contains the report template and project notes. The analysis steps above are planned.

## Limitations

The small sample size limits power after multiple-testing correction and makes cross-validation estimates sensitive to the samples in each fold. Whole-blood cell composition, clinical differences, and technical variation may influence the observed expression patterns. Results are exploratory and would require independent validation before drawing conclusions about performance in other populations or clinical settings.

## Attribution

This project uses data deposited by Prathima Arvind and Jayashree Shanker in the NCBI Gene Expression Omnibus.  [GEO study record](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE42148)
