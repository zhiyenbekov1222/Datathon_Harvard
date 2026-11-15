# DAZA: Early-Onset Preeclampsia vs Preterm Birth in Decidual Tissue, Adjusted for Fetal Sex and Maternal Age

OpenBio 2026 Datathon, Track 1: Maternal-Fetal Cell Communication in Preeclampsia

| Name | Role |
|---|---|
| David Drew | Researcher |
| Alexis Namulinda | Researcher |
| Annabelle McGregor | Researcher |
| Zhalgas Zhiyenbekov | Data Scientist |

## Research Question

In bulk RNA-seq from decidual tissue (GSE203507), which genes differ between early-onset preeclampsia (EOPE, n=8) and gestational-age-matched idiopathic preterm birth (iPTB, n=5) after accounting for fetal sex and maternal age?

## Data Used

- [x] GSE203507 bulk RNA-seq (required): decidual tissue samples (`_DB` files), 27 samples and 26,485 genes, from the Track 1 data package (`data/extension/decidua`). Citation [1].
- [ ] Zeisel/Admati gene browser, for at least three genes (required): TODO add the three genes and what the browser showed, then cite [2].
- [ ] Admati/Skarbianskis single-cell pseudobulk files (optional): not used.
- [x] Other public data: MSigDB Hallmark gene sets (release 2024.1.Hs, `h.all.v2024.1.Hs.symbols.gmt`), downloaded with `data/get_gene_sets.py` and used for the gene set analysis in both tissues (not redistributed, MSigDB license). Citation [4]. We also use the Track 1 data package from the organizers. Citation [3].

## How to Run

1. The data package is in [`track-01/data`](https://github.com/Harvard-Undergraduate-OpenBio/openbio-datathon-2026/tree/main/track-01/data) of the datathon repository. On Colab the first notebook cell downloads it.
2. Install packages by following [Track 1 Setup](https://github.com/Harvard-Undergraduate-OpenBio/openbio-datathon-2026/blob/main/track-01/1-setup.md). The notebook needs `pydeseq2`, `numpy`, `pandas`, `matplotlib`, `scikit-learn` and `scipy`.
3. Run `notebooks/track01_4_f_covariates_median_eda_db.ipynb` top to bottom (Runtime > Run all on Colab). On Colab the first cell clones the repository, installs `pydeseq2` and changes into `track-01/notebooks`. On your own computer, start Jupyter from `track-01/notebooks` so the relative `../data` path works.

4. Gene set analysis for both villous and decidual tissue: run `python data/get_gene_sets.py` from `track-01` to download the Hallmark gene sets (this needs internet and may ask you to sign in to MSigDB; if so, download `h.all.v2024.1.Hs.symbols.gmt` from MSigDB and save it as `data/track01_gene_sets.gmt`). Then run `python notebooks/track01_geneset_analysis.py` from `track-01`. Use `--de-from-vt` and `--de-from-db` to reuse existing differential expression tables instead of refitting, and `--data-dir` if the data folder is somewhere else.

Reproducibility details:

- Notebook or script files: `notebooks/track01_4_f_covariates_median_eda_db.ipynb`
- Where we ran it (Colab or our own computer): TODO
- Random seeds, if any: none that affect the statistics. PCA and DESeq2 are deterministic. Random jitter in some dot plots (one plot uses `np.random.default_rng(1)`) only changes how points are drawn.
- Gene set outputs: `results/geneset/` holds the DE tables (`de_vt_EOPE_vs_iPTB.csv`, `de_db_EOPE_vs_iPTB.csv`), the rank tests (`rank_test_*.csv`), the over-representation tests (`ora_*.csv`) and the figures `hallmark_rank_vt.png`, `hallmark_rank_db.png`, `hallmark_comparison_villous_vs_decidual.png` and `hallmark_ora.png`.
- Outputs: the notebook writes figures and tables to `results/` and `results/eda/` (for example `de_EOPE_vs_iPTB.csv`, `passing_genes_EOPE_vs_iPTB.csv` and the volcano plot).
- Data folder: the notebook points to `../data/extension/decidua` (`track01_counts_db.csv`, `track01_metadata_db.csv`, `qc/track01_htseq_summary.csv`). The original placental files (`_vt`) can be used by switching the commented lines in the setup and load cells.
- Sections 4 to 6 contain the core differential expression analysis. Section 7 (cell-type markers) does not run on the decidua data because `interpretation/track01_marker_matrix.csv` is missing from that folder, and Section 6f is slow and not needed for the final result.

## Files

| Path | Contents |
|---|---|
| `notebooks/` | The analysis notebook and `track01_geneset_analysis.py` (Hallmark gene set analysis for villous and decidual tissue) |
| `figures/` | Exported graphs, including `headline.png` |
| `docs/article.md` | Science communication article, following the [HUDP article guide](https://github.com/Harvard-Undergraduate-OpenBio/openbio-datathon-2026/blob/main/docs/article-guide.md) |
| `docs/analysis-plan.md` | The comparison, model and thresholds we chose |
| `docs/hypothesis-card.md` | Our hypothesis and how to test it |

## Key Findings

We compared EOPE (n=8, delivered at 26.0-33.0 weeks) with iPTB (n=5, 29.9-33.0 weeks) using PyDESeq2 with the design `~ fetal_sex + maternal_age_years_class + group` (13 samples, 4 parameters, 9 residual degrees of freedom). Of 19,337 genes tested, 79 passed padj < 0.05 and |log2 fold change| >= 1: 51 were higher and 28 lower in EOPE. The genes with the smallest padj were UCA1, NTRK2, ARSI, LEP and COASY. LEP (log2 fold change 6.6), PAPPA2 (1.7) and INHBA (2.0) were all higher in EOPE, and a row-wise z-score heatmap of the top 20 genes shows how they behave across all clinical groups. TODO: add what the Zeisel/Admati gene browser showed for at least three genes. TODO: add the Hallmark gene set results for villous and decidual tissue (which sets were shifted in EOPE, and whether the two tissues agree) once `track01_geneset_analysis.py` has been run.

## Limitations

- Very small groups (8 EOPE and 5 iPTB), so a non-significant result does not show that groups are equal and fold changes are uncertain.
- Gestational age is only roughly balanced (26.0-33.0 weeks in EOPE, 29.9-33.0 in iPTB). Three EOPE samples (S18, S19, S20) are outliers for birthweight and gestational age.
- Fetal sex is unbalanced (EOPE is 6 female and 2 male). We adjusted for it, but with so few samples the adjustment is imperfect.
- Maternal age was split into Low and High at the median within each group. We chose it as a covariate after looking at PCA plots, so the model choice is data-driven and exploratory. We dropped the other candidate covariates (gestational age, birthweight, blood pressure) to keep residual degrees of freedom.
- Blood pressure is a defining feature of preeclampsia, so its association with PC1 cannot be treated as an independent source of variation.
- Bulk tissue mixes many cell types, so a change in cell composition could look like a change in gene expression. The marker-based cell-type analysis could not be run on the decidua data.
- The decidua dataset has 3 fewer samples than the full placental dataset. This does not change the EOPE vs iPTB comparison but limits the comparisons to other groups, which we did not run.
- The Hallmark gene set tests use broad, general-purpose gene sets that are not specific to placenta. The rank-based test treats genes as independent, and the over-representation test has little power with a short list of passing genes, so we read both as exploratory.
- The gene pre-filter (at least 10 reads in total in either group) means padj values are not directly comparable with earlier runs that tested a different set of genes.

## Presentation

Video link: TODO add your unlisted YouTube link

## Citations

Datasets

1. Ackerman WE, Buhimschi IA, Buhimschi CS, et al. Molecular subphenotyping using placental transcriptomics in early-onset preeclampsia and fetal growth restriction. NCBI GEO GSE203507. https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE203507
2. Zeisel lab placenta PE gene browser. https://storage.googleapis.com/www_zeisellab/PE_web/pe_web_test_v1.html (add only if used)
3. Track 1 data package, OpenBio 2026 Datathon, Harvard Undergraduate OpenBio. https://github.com/Harvard-Undergraduate-OpenBio/openbio-datathon-2026/tree/main/track-01/data
4. Liberzon A, Birger C, Thorvaldsdottir H, Ghandi M, Mesirov JP, Tamayo P. The Molecular Signatures Database (MSigDB) Hallmark gene set collection. Cell Systems 1(6), 417-425 (2015). https://doi.org/10.1016/j.cels.2015.12.004. Hallmark collection, MSigDB release 2024.1.Hs. https://www.gsea-msigdb.org/gsea/msigdb/

Tools

5. Muzellec B, Telenczuk M, Cabeli V, Andreux M. PyDESeq2: a python package for bulk RNA-seq differential expression analysis. Bioinformatics 39(9), btad547 (2023). https://doi.org/10.1093/bioinformatics/btad547
6. Love MI, Huber W, Anders S. Moderated estimation of fold change and dispersion for RNA-seq data with DESeq2. Genome Biology 15, 550 (2014). https://doi.org/10.1186/s13059-014-0550-8
7. Anders S, Pyl PT, Huber W. HTSeq: a Python framework to work with high-throughput sequencing data. Bioinformatics 31(2), 166-169 (2015). https://doi.org/10.1093/bioinformatics/btu638 (read-assignment QC summary)
8. Pedregosa F, et al. Scikit-learn: Machine Learning in Python. Journal of Machine Learning Research 12, 2825-2830 (2011). (PCA)
9. Harris CR, et al. Array programming with NumPy. Nature 585, 357-362 (2020). https://doi.org/10.1038/s41586-020-2649-2
10. McKinney W. Data Structures for Statistical Computing in Python. Proceedings of the 9th Python in Science Conference, 56-61 (2010).
11. Virtanen P, et al. SciPy 1.0: fundamental algorithms for scientific computing in Python. Nature Methods 17, 261-272 (2020). https://doi.org/10.1038/s41592-019-0686-2 (Fisher's exact and Mann-Whitney tests)
12. Hunter JD. Matplotlib: A 2D graphics environment. Computing in Science & Engineering 9(3), 90-95 (2007). https://doi.org/10.1109/MCSE.2007.55
