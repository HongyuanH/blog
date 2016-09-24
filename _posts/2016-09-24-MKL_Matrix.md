---
layout:            post
title:             "Understanding Input Parameters to MKL Matrix"
menutitle:         "MKL Matrix"
date:              2016-09-24
tags:              MKL HPC
category:          High Performance Computing
author:            hongyuan

---

When using MKL for matrix-related calculation, one may easily get confused because there are too many input parameters to tell MKL how to construct a matrix:

```cpp
const <TYPE> *a					//pointer to array a
const MKL_INT m					//number of rows
const MKL_INT n					//number of columns
const LAYOUT layout				//RowMajor/ColMajor
const TRANSPOSE trans			//NoTrans/Trans/ConjTrans
const MKL_INT lda				//leading dimension of a
```

## Matrix Dimension
 
Dead simple but easy to forget: `m` -- number of rows; `n` -- number of columns.
 
<figure>
<img src="{{ site.github.url }}/media/img/2016-09-24-MKL_Matrix/Matrix-Dimension.png" />
</figure>


## Matrix Layout

The data are stored as a 1-dimension array in the memory (length l):

<figure>
<img src="{{ site.github.url }}/media/img/2016-09-24-MKL_Matrix/Array.png" />
</figure>

The parameter `lda` determines the offset of the first element in each row/column:

<figure>
<img src="{{ site.github.url }}/media/img/2016-09-24-MKL_Matrix/lda1.png" />
</figure>

`layout` determines whether the data are continuous in each row or column:
  
<figure>
<img src="{{ site.github.url }}/media/img/2016-09-24-MKL_Matrix/Row-Col-Major-Arrow.png" />
</figure>

If normal `lda` is used, i.e.:

<figure>
<img src="{{ site.github.url }}/media/img/2016-09-24-MKL_Matrix/lda2.png" />
</figure>

We have the following matrix layout:

<figure>
<img src="{{ site.github.url }}/media/img/2016-09-24-MKL_Matrix/Row-Col-Major.png" />
</figure>

General case:

<figure>
<img src="{{ site.github.url }}/media/img/2016-09-24-MKL_Matrix/Row-Col-Major-Gen.png" />
</figure>

## Transpose

You can think that this parameter is applied to the matrix after its memory layout has been determined.









