---
layout: default
title: About ccAFv2
nav_order: 2

---
# About ccAFv2
Single-cell transcriptomics has revealed extensive cellular heterogeneity, with the cell cycle playing a key role. We developed a high-resolution cell cycle classifier, ccAFv2, using scRNA-seq data from human neural stem cells. ccAFv2 identifies six cell cycle states (G1, Late G1, S, S/G2, G2/M, and M/Early G1) and a quiescent-like G0 state (Neural G0), incorporating a tunable parameter to filter uncertain classifications. It outperforms or matches other methods, while classifying more states, including G0. ccAFv2 generalizes well across cell types for S, S/G2, G2/M, and M/Early G1 states but shows reduced accuracy for G0, G1, and Late G1 in non-neuroepithelial cells. Misclassifications are limited to these states. We applied ccAFv2 to classify cells, nuclei, and spatial transcriptomics data across species using various normalization methods and gene identifiers. The classifier also enables regression of cell cycle effects to uncover biological signals. 

The ccAFv2 classifier builds on the foundation established by its predecessor (ccAFv1, O’Connor et al. 2021) and other classifiers like Seurat (Hao et al. 2021), Tricycle (Zheng et al. 2022), and Cyclone (Scialdone et al. 2015). It addresses the limitations of earlier methods by employing a fully connected artificial neural network (ANN) to classify cells into a broader range of cell cycle states, including a quiescent-like G0 state. ccAFv2 also features a tunable threshold to exclude uncertain classifications, making it adaptable across experimental conditions.

The core algorithm of the ccAFv2 is a fully connected artificial neural network (ANN) implemented using the Keras API (v2.12.0) that employs TensorFlow (v2.12.0) to construct ANNs. It takes the expression levels of 861 highly variable genes as input and classifies cells into seven distinct cell cycle states: Neural G0, G1, Late G1, S, S/G2, G2/M, and M/Early G1. The model incorporates dropout layers to prevent overfitting and uses Stochastic Gradient Descent (SGD) for optimization. The result is a powerful, flexible tool for analyzing cell cycle dynamics.

#WHICH FIGURES TO INCLUDE ?
