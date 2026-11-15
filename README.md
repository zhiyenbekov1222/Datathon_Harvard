# TODO Team Name: Early-Onset Preeclampsia vs Preterm Birth, Adjusted for Fetal Sex and Maternal Age

OpenBio 2026 Datathon, Track 1: Maternal-Fetal Cell Communication in Preeclampsia

## Team

| Name | Role |
|---|---|
| TODO name | TODO role |
| TODO name | TODO role |

## Research Question

In bulk RNA-seq from decidual tissue (GSE203507), which genes differ between early-onset preeclampsia (EOPE, n=8) and gestational-age-matched idiopathic preterm birth (iPTB, n=5) after accounting for fetal sex and maternal age?

## Data Used

- [x] GSE203507 bulk RNA-seq (required)
- [ ] Zeisel/Admati gene browser, for at least three genes (required)
- [ ] Admati/Skarbianskis single-cell pseudobulk files (optional)
- [ ] Other public data: none

The notebook analyses the decidua version of the data (`data/extension/decidua`: `track01_counts_db.csv`, `track01_metadata_db.csv`, `qc/track01_htseq_summary.csv`), which has 27 samples and 26,485 genes. The EOPE and iPTB samples used in the comparison are the same as in the full dataset. The original placental tissue files (`_vt`) can be used instead by switching the commented lines in the setup and load cells.

## How to Run

1. The data package is in [`track-01/data`](https://github.com/Harvard-Undergraduate-OpenBio/openbio-datathon-2026/tree/main/track-01/data) of the datathon repository. On Colab the first notebook cell downloads it.
2. Install packages by following [Track 1 Setup](https://github.com/Harvard-Undergraduate-OpenBio/openbio-datathon-2026/blob/main/track-01/1-setup.md). The notebook needs `pydeseq2`, `numpy`, `pandas`, `matplotlib`, `scikit-learn` and `scipy`.
3. Run `notebooks/track01_4_f_covariates_median_eda_db.ipynb` top to bottom (Runtime > Run all on Colab). On Colab the first cell clones the repository, installs `pydeseq2` and changes into `track-01/notebooks`. On your own computer, start Jupyter from `track-01/notebooks` so the relative `../data` path works.

Reproducibility details:

- Notebook or script files: `notebooks/track01_4_f_covariates_median_eda_db.ipynb`
- Where we ran it (Colab or our own computer): TODO
- Random seeds, if any: none that affect the statistics. PCA and DESeq2 are deterministic. Random jitter in some dot plots (one plot uses `np.random.default_rng(1)`) only changes how points are drawn.
- Outputs: the notebook writes figures and tables to `results/` and `results/eda/` (for example `de_EOPE_vs_iPTB.csv` and the volcano plot).
- Sections 4 to 6 contain the core differential expression analysis. In the decidua version, Section 7 (cell-type markers) cannot run because `interpretation/track01_marker_matrix.csv` is not in the data folder, and Section 6f is slow and is not needed for the final result.

## Files

| Path | Contents |
|---|---|
| `notebooks/` | The analysis notebook or script |
| `figures/` | Exported graphs, including `headline.png` |
| `docs/article.md` | Science communication article, following the [HUDP article guide](https://github.com/Harvard-Undergraduate-OpenBio/openbio-datathon-2026/blob/main/docs/article-guide.md) |
| `docs/analysis-plan.md` | The comparison, model and thresholds we chose |
| `docs/hypothesis-card.md` | Our hypothesis and how to test it |

## Key Findings

We compared EOPE (n=8, delivered at 26.0-33.0 weeks) with iPTB (n=5, 29.9-33.0 weeks) using PyDESeq2 with the design `~ fetal_sex + maternal_age_years_class + group` (13 samples, 4 parameters, 9 residual degrees of freedom). Of 19,337 genes tested, 79 passed padj < 0.05 and |log2 fold change| >= 1: 51 were higher and 28 lower in EOPE. A row-wise z-score heatmap of the 20 genes with the smallest padj shows how these genes behave across all clinical groups. TODO: add what the Zeisel/Admati gene browser showed for at least three genes.

## Limitations

- Very small groups (8 EOPE and 5 iPTB), so a non-significant result does not show that groups are equal and fold changes are uncertain.
- Gestational age is only roughly balanced (26.0-33.0 weeks in EOPE, 29.9-33.0 in iPTB). Three EOPE samples (S18, S19, S20) are outliers for birthweight and gestational age.
- Fetal sex is unbalanced (EOPE is 6 female and 2 male). We adjusted for it, but with so few samples the adjustment is imperfect.
- Maternal age was split into Low and High at the median within each group. We chose it as a covariate after looking at PCA plots, so the model choice is data-driven and exploratory. We dropped the other candidate covariates (gestational age, birthweight, blood pressure) to keep residual degrees of freedom.
- Blood pressure is a defining feature of preeclampsia, so its association with PC1 cannot be treated as an independent source of variation.
- Bulk tissue mixes many cell types, so a change in cell composition could look like a change in gene expression. The marker-based cell-type analysis could not be run on the decidua data.
- The decidua dataset has 3 fewer samples than the full placental dataset. This does not change the EOPE vs iPTB comparison but limits the comparisons to other groups, which we did not run.
- The gene pre-filter (at least 10 reads in total in either group) means padj values are not directly comparable with earlier runs that tested a different set of genes.

## Presentation

Video link: TODO add your unlisted YouTube link
