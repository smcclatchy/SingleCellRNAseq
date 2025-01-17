---
# Please do not edit this file directly; it is auto generated.
# Instead, please edit 07-Analyzing-Your-Data.md in _episodes_rmd/
source: Rmd
title: "Analyzing Your Data"
teaching: 10
exercises: 2
questions:
- "Can I analyze my own single cell RNA-Seq experiment?"
objectives:
- "Identify a workflow for analyzing your data."
- "Be aware of key decision points where your choices influence analysis outcomes."
- "Know how to seek help with your work."
keypoints:
- "There are excellent tools for helping you analyze your scRNA-Seq data."
- "Pay attention to points we have stressed in this course and points that are stressed in analysis vignettes that you may find elsewhere."
- "There is a vibrant single cell community at JAX and online (e.g. Twitter) where you can seek out help."
---



In this lesson we will dive into the data that you bring.
It is not required that you bring along data. We will not necessarily
discuss any new questions that we did not cover earlier in the course;
rather, this lesson is an opportunity to see how some of these issues
play out in a set of diverse datasets that have different
characteristics.

If you have your own data, you might set a goal of completing quality
control, normalization, basic clustering, and identifying major
cell clusters. If you are working with data from a publication, you might
try to reproduce -- as best you can -- one of the main figures from the
paper.


## Points to keep in mind as you work with your data

 * You can find a high-level overview of the scRNA-Seq workflow by reviewing
 lessons 3 and 4 of this course.
 * If you have your own data it may be helpful to find a published dataset 
 from the same tissue which will likely be valuable for confirming the cell 
 types that you see in your data.
 * Your cell/gene filtering parameters may be quite different from those we 
 used earlier in this course.
 * The number of PCs that you select for dimensionality reduction is an 
 important quantity and you may wish to examine results from more than one
 value to determine how they change.
 * Your clustering resolution will obviously affect the clusters that you
 detect and can easily be altered to match your intuition regarding the
 heterogeneity of cells in your sample.


## A review of a typical Seurat scRNA-Seq analysis pipeline

```{}
obj <- CreateSeuratObject(counts, project = 'my project',
        meta.data = metadata) %>%
        PercentageFeatureSet(pattern = "^mt-", col.name = "percent.mt")
```

Analyze using base Seurat workflow

```{}
obj <- NormalizeData(obj, normalization.method = "LogNormalize") %>% 
    FindVariableFeatures(nfeatures = 2000) %>% 
    ScaleData(vars.to.regress = c("percent.mt", "nCount_RNA")) %>%
    RunPCA(verbose = FALSE, npcs = 100)
```

Look at your PC's and decide how many to use for dimensionality reduction
and clustering:

```{}
ElbowPlot(obj, ndims = 100)
# let's use X PCs
obj <- FindNeighbors(obj, reduction = 'pca', dims = 1:X, verbose = FALSE) %>%
    FindClusters(verbose = FALSE, resolution = 0.7) %>%
    RunUMAP(reduction = 'pca', dims = 1:X, verbose = FALSE)
```

## Integration and Label Transfer

When analyzing your own data, you may find it useful to 
obtain someone else's previously published data (or your lab's
unpublished data) with which someone has already identified 
cell clusters. You might then "bootstrap" your own analyses off
of this existing data. Assuming the data you are using is derived
from the same (or a similar) tissue, and that there are no 
enormous differences in the techology used to profile the cells,
you can use the cell labels identified in the other dataset to
try to identify your own clusters. This process can be called
"integration" when two or more datasets are merged in some way, and 
"label transfer" when the cell cluster labels are transferred from
one dataset to another. 

If you wish to try this process with your own data, you may find
the Seurat 
[Integration and Label Transfer vignette](https://satijalab.org/seurat/archive/v3.0/integration.html)
helpful.



