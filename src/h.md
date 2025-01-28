---
layout: default
title: Classifying single cell or nuclei RNA-seq
nav_order: 3

---
# Input for classification
The ccAFv2 classifier requires a thoroughly quality-controlled Seurat
object, with an example QC pipeline used on the U5 human neural stem cell (hNSC) dataset is available
[here](https://github.com/plaisier-lab/FIXLINK).
SCTransform is preferred, standard normalization only applies to the
highly variable genes. This can exclude genes needed for the accurate
classification of the cell cycle. To address this, the PredictCellCycle
function re-runs SCTransform to retain all genes in the dataset

# Test Data

The U5 human neural stem cell (hNSC) dataset used to train the ccAFv2 is
available for testing purposes
[here](https://zenodo.org/records/10961633/files/U5_normalized_ensembl.rds?download=1).
This data has been QC'd and normalized using SCTransform following our
best practices described above.

```{r install, echo=T, results='hide'}
devtools::install_github("plaisier-lab/ccafv2_R/ccAFv2")
```
