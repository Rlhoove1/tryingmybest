---
layout: default
title: Classifying single cell or nuclei RNA-seq
nav_order: 7

---
# Input For Classification
The ccAFv2 classifier requires a thoroughly quality-controlled Seurat
object. SCTransform is preferred, standard normalization only applies to the
highly variable genes. This can exclude genes needed for the accurate
classification of the cell cycle. The PredictCellCycle
function re-runs SCTransform to retain all genes in the dataset. 

An example QC pipeline used on the U5 human neural stem cell (hNSC) dataset is available
[here](https://github.com/plaisier-lab/FIXLINK).

## Test Data

The U5 human neural stem cell (hNSC) dataset used to train the ccAFv2 is
available for testing purposes
[here](https://zenodo.org/records/10961633/files/U5_normalized_ensembl.rds?download=1).
This data has been QC'd and normalized using SCTransform following our
best practices described above.

```r
devtools::install_github("plaisier-lab/ccafv2_R/ccAFv2")
library(ccAFv2)
library(Seurat)
library(reticulate)
reticulate::use_virtualenv('retic')
library(tensorflow)
#Load in sample data
seurat_obj = readRDS('U5_normalized_ensembl.rds')
seurat_obj = PredictCellCycle(seurat_obj)
```
## Marker Genes
Examining marker genes is crucial for accuracy. Predictions
significantly worsen if fewer than 689 marker genes (80%) are present.
Setting do_sctransform = TRUE maximizes marker gene overlap. Timing and
93/93 values may vary by dataset, which is normal.

```r
PredictCellCycle(seurat_obj,
                 threshold=0.5,
                 include_g0 = FALSE,
                 do_sctransform=TRUE,
                 assay='SCT',
                 species='human',
                 gene_id='ensembl',
                 spatial = FALSE)

```
Standard output:  
```r
Running ccAFv2:
  Redoing SCTransform to ensure maximum overlap with classifier genes...
  Total possible marker genes for this classifier: 861
    Marker genes present in this dataset: 861
    Missing marker genes in this dataset: 0
  Predicting cell cycle state probabilities...
93/93 [==============================] - 1s 4ms/step
  Choosing cell cycle state...
  Adding probabilities and predictions to metadata
Done
```

# Cell Cycle Classification Results

The results of the cell cycle classification are stored in the Seurat
object metadata. The likelihoods for each cell cycle state can be found
with the labels of each cell cycle state ( 'Neural.G0', 'G1', 'Late.G1', 'S',
'S.G2', 'G2.M', and 'M.Early.G1') and the classification for each cell
can be found in 'ccAFv2'.

Note that when 'include_g0 = FALSE' there will still be a calculated likelihood for 'Neural.G0', however none of the predictions in the 'ccAFv2' column will be 'G0'. 
```r
#check to ensure 'ccAFv2' colum exists
colnames(seurat_obj@meta.data)
```

# Plotting Cell Cycle States
Plot a UMAP with cell cycle states
```r
DimPlot.ccAFv2(seurat_obj)
```
![Dimplot_1]({{ site.baseurl }}/images/DimPlot_1.jpeg)

# Impact of Varying Likelihood Thresholds
Cell cycle state likelihoods must meet or exceed the threshold; otherwise, cells are classified as “Unknown.” The removal of less certain classifications improves the accuracy of the overall classifications. 
For this dataset a likelihood threshold of ≥ 0.5 because it signifies a minimum of 50% certainty in the classified cell cycle state and >90% of cells could be assigned a cell cycle state. Users can adjust the threshold to suit specific datasets.

```r
ThresholdPlot(seurat_obj)
```
![ThresholdPlot_1]({{ site.baseurl }}/images/ThresholdPlot_1.jpeg)

# Cell cycle Regression
The cell cycle strongly influences gene expression, so it's common to
regress out cell cycle effects and use residual variance for further
analysis. We support this using ccAFv2 marker genes, starting by
calculating expression module scores for Late G1, S, S/G2, G2/M, and
M/Early G1 phases.

```r
#Collect expression module scores for the cell cycle states 
seurat_obj = PrepareForCellCycleRegression(seurat_obj)

#Regress these signatures out of the expression data
seurat_obj = SCTransform(seurat_obj, vars.to.regress = c("Late.G1_exprs1", "S_exprs2", "S.G2_exprs3", "G2.M_exprs4", "M.Early.G1_exprs5"))
```
Removing the cell cycle from the U5 hNSCs leads to a random distribution
because the cell cycle is the primary biological signal in this in
vitro-grown cell line.
```r
seurat_obj = RunPCA(seurat_obj)
seurat_obj = RunUMAP(seurat_obj, dims=1:10)
DimPlot.ccAFv2(seurat_obj)
```
![Dimplot_2]({{ site.baseurl }}/images/DimPlot_2.jpeg)
