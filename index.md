---
title: About
nav_order: 1
layout: home
---
This repository is for the R package for the cell cycle classifier ccAFv2. The input for the ccAFv2 classifier is single cell, nuclei, or spatial RNA-seq data. The features of this classifier are that it classifies six cell cycle states (G1, Late G1, S, S/G2, G2/M, and M/Early G1) and a quiescent-like G0 state, and it incorporates a tunable parameter to filter out less certain classifications. This package is implemented in R for use in Seurat analysis workflows. We provide examples of installing and running ccAFv2 on Seurat objects (both sc/snRNA-seq and ST-RNA-seq), plot and use results, and regress out cell cycle effects if desired.


----
