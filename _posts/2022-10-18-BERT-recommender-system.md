---
title: "BERT4Rec: BERT as a recommender system"
date: 2022-10-18 00:00:00 -500
categories: [blog]
tags: [Transformers, BERT, BERT4Rec, recommender system, NLP, machine learning]
images: /assets/images/bert4rec
---

## Introduction

In this post, we will be implementing a simple recommender system using the BERT4Rec model, which is a BERT-based model for sequential recommendation. The model is based on the paper [BERT4Rec: Sequential Recommendation with Bidirectional Encoder Representations from Transformer](https://arxiv.org/abs/1904.06690) by Zhen-Hua Ling, et al. The model is a simple BERT model with a few modifications to make it suitable for sequential recommendation. The model is trained on the [MovieLens 1M](https://grouplens.org/datasets/movielens/1m/) dataset.


## Autoregressive Language Modeling

Before we get into details about BERT4Rec we need to understand what autoregressive model means. An autoregressive model is a model that generates the next token in the sequence based on the previous tokens in the sequence. For example, if we have a sequence of tokens [I, like, to, watch, movies], the model will generate the next token based on the previous tokens. A sequence could contain words or numbers or anything else.

Most language models, recommender systems, time-series forecasting models, and many other models are autoregressive models. The model generates the next token based on the previous tokens in the sequence.


![Autoregressive models diagram]({{page.images | relative_url}}/autoregressive_models.drawio.png){:width="80%"}
*A simplified illustration of how autoregressive models work*



## BERT4Rec

BERT4Rec is a BERT-based model for sequential recommendation. The model is based on the paper [BERT4Rec: Sequential Recommendation with Bidirectional Encoder Representations from Transformer](https://arxiv.org/abs/1904.06690) by Zhen-Hua Ling, et al. The model is a simple BERT model with a few modifications to make it suitable for sequential recommendation. The model is trained on the [MovieLens 1M](https://grouplens.org/datasets/movielens/1m/) dataset.

What makes BERT4Rec different from the classic BERT is that BERT4Rec's vocabulary isn't words but rather ids of items in the sequence. So, a sequence of items, for example movies ["Harry Potter", "Silence of the lambs", ...] would be represented as a sequence of ids [4, 8, 15, 32, 100]. There are two separate embedding layers, one for items and one for user ids.

![BERT4Rec](https://www.researchgate.net/publication/357555116/figure/fig1/AS:1108425652092928@1641280677333/Model-architecture-of-MetaBERT4Rec-In-contrast-to-BERT4Rec-we-add-meta-embedding-for.ppm)
*BERT4Rec architecture. Image credit: Zhen-Hua Ling, et al.*

## Implementation

### Dataset preparation

The dataset we will be using is the [MovieLens 1M](https://grouplens.org/datasets/movielens/1m/) dataset. The dataset contains 1 million ratings from 6000 users on 4000 movies. The dataset is available in the [MovieLens 1M](https://grouplens.org/datasets/movielens/1m/) website. The dataset is available in the form of a zip file. We will be using the `ratings.dat` file from the dataset. The `ratings.dat` file contains the following columns:

- `UserID`
- `MovieID`
- `Rating`

The dataset needs to be preprocessed and converted into a format that is suitable for training. The preprocessing steps are as follows:

  - Sort the dataset by `Timestamp`
  - Get the `UserID` and `MovieID` columns
  - Split the dataset into train and valuation sets
  - Convert the `UserID` and `MovieID` columns into sequences

We can then create a DataLoader using `pytorch` library to load the data in batches.

```python	
from torch.utils.data import DataLoader

train_loader = DataLoader(
    train_data,
    batch_size=batch_size,
    num_workers=num_workers,
    shuffle=True,
)
val_loader = DataLoader(
    val_data,
    batch_size=batch_size,
    num_workers=num_workers,
    shuffle=False,
)
```

### Model

The model architecture is designed as in the original paper.
Here are some of the specifications of the model:

```bash
len(train_data) 162541
len(val_data) 162541
GPU available: True, used: True
TPU available: False, using: 0 TPU cores
LOCAL_RANK: 0 - CUDA_VISIBLE_DEVICES: [0]

  | Name                | Type               | Params
-----------------------------------------------------------
0 | item_embeddings     | Embedding          | 7.6 M 
1 | input_pos_embedding | Embedding          | 65.5 K
2 | encoder             | TransformerEncoder | 3.6 M 
3 | linear_out          | Linear             | 7.6 M 
4 | do                  | Dropout            | 0     
-----------------------------------------------------------

18.8 M    Trainable params
0         Non-trainable params
18.8 M    Total params
75.197    Total estimated model params size (MB)
```

### Training

The model is trained for 4 epochs with a batch size of 64. The model is trained on a single GPU using the Adam optimizer with a learning rate of 0.001. 


## References

[Source code](https://github.com/amrohendawi/recommender_BERT4Rec)
[BERT4Rec: Sequential Recommendation with Bidirectional Encoder Representations from Transformer](https://arxiv.org/abs/1904.06690)