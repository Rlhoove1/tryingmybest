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

```
devtools::install_github("plaisier-lab/ccafv2_R/ccAFv2")
library(ccAFv2)
library(Seurat)
library(reticulate)
reticulate::use_virtualenv('retic')
library(tensorflow)
```

```
seurat_obj = readRDS('U5_normalized_ensembl.rds')
seurat_obj = PredictCellCycle(seurat_obj)
```
Examining marker genes is crucial for accuracy. Predictions
significantly worsen if fewer than 689 marker genes (80%) are present.
Setting do_sctransform = TRUE maximizes marker gene overlap. Timing and
93/93 values may vary by dataset, which is normal.

```
PredictCellCycle(seurat_obj,
                 threshold=0.5,
                 include_g0 = FALSE,
                 do_sctransform=TRUE,
                 assay='SCT',
                 species='human',
                 gene_id='ensembl',
                 spatial = FALSE)

```
# Cell cycle classification results

The results of the cell cycle classification are stored in the Seurat
object metadata. The likelihoods for each cell cycle state can be found
with the labels of each cell cycle state ( 'Neural.G0', 'G1', 'Late.G1', 'S',
'S.G2', 'G2.M', and 'M.Early.G1') and the classification for each cell
can be found in 'ccAFv2'.

Note that when 'include_g0 = FALSE' there will still be a calculated likelihood for 'Neural.G0', however none of the predictions in the 'ccAFv2' column will be 'G0'. 
```
head(seurat_obj@meta.data)
```
# Plotting cell cycle states
Plot a UMAP with cell cycle states
```
DimPlot.ccAFv2(seurat_obj)
```
![My Custom Image](/images/Dimplot_1.jpeg)
# Plotting the impact of varying likelihood thresholds
Cell cycle state likelihoods must meet or exceed the threshold; otherwise, cells are classified as “Unknown.” The removal of less certain classifications improves the accuracy of the overall classifications. 
For this dataset a likelihood threshold of ≥ 0.5 because it signifies a minimum of 50% certainty in the classified cell cycle state and >90% of cells could be assigned a cell cycle state. Users can adjust the threshold to suit specific datasets.

```
ThresholdPlot(seurat_obj)
```
