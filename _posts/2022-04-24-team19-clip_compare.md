---
layout: post
comments: true
title: Explore wider usage of CLIP 
author: Yuyue Wang, Yufeng Li 
date: 2022-04-22
---


> Explore wider usage of CLIP, a large scale self-supervised models. We believe CLIP have more usage than what's shown in the original paper, as it has great feature extraction ability.

<!--more-->
{: class="table-of-content"}
* TOC
{:toc}

## Motivition 

Imagine you are a machine learning engineer. One day, you want to add some new features for a classification task. For example you may need to do some fine grained classification on chopsticks like identify whether it is a Sushi chopstick, disposable chopstick or hot pot chopstick, but you do have any data. Then, one simple way to solve this problem is to do some data collection on your own, because your boss think find some new people to label it is too complex and time consuming. So, you followed the advice of your boss and then collect some chopstick data and train it. But things can happen over and over again, maybe today is chopsticks, tomorrow is soups and then your inference pipeline become more and more difficult to maintain. 

But could we just maintain some easily accessible data like image with language supervision and then train a model like clip instead of maintain lots of specialized model and data? For example, CLIP representation is very powerful and able to outperform many existing model with only CLIP image feature and linear classifier. Could we just maintain CLIP model and its training data and do simple ML classification instead of train deep model each time? 


## What we hope to demonstrate
Recently, there is a trend in industry to train large-scale self-supervised models. Such models utilize huge amount of unlabeled data to learn the intrinsic features, and in this way get more general and robust prediction result. CLIP is such a model consisting of an image encoder and a text encoder. In the forward stage, it calculates the loss based on the difference between the image/text feature vector pair with an image/text-description pair as input, and optimizes the two encoders' parameters simultaneously. Typical usage of the CLIP model includes using the image encoder as pretrained model to finetune, and formulizing the prediction task as text queries, together with the image feeding into CLIP to get a score vector. 

We believe CLIP have more usage than what's shown in the original paper, as it has great feature extraction ability.

First we'll compare CLIP with other models trained with general labeled dataset. We want to see if finetuning CLIP as pretrained model results in higher accuracy, fewer labeled data for a specific task.

Second, we'll use CLIP image encoder to extract feature vectors for a specific task/dataset, and unsupervised-learn to cluster the vectors. We'll examinate whether the cluster reflects the actual data label. If so, such clustering method can be used for auto-labeling and label imbalance elimination. 

Third, we'll also further explore the generality of CLIP. Whether its good features is sensitive to the specific data domain (whether similar data exists in original unlabeled training data or not)

## Method 

### Tested model: 

The experiment is run on the following model, there are 3 type of model

Type 1: language supervised model
- CLIP model( VIT-32 ) [1]

Type 2: model trained on open scource dataset (ImageNet)
- Vision Transformer: VIT-32 (base and large version), VIT-16 (base and large version) [2]
- New CNN: convnext_small, convnext_base, convnext_large [3]
- Traditional CNN: resnext50_32x4d, resnet18, efficientnet_b4,efficientnet_b6 [4]

Type 3: Model with self-supervised pretraining
- Beit [5]
- ViTMAE [6]
- DINO [7]
- Data2vec [8]

#### Dataset

There are two type of dataset we will test:

Dataset similar to CLIP pretraining data
- Flowers102: 102 category dataset, consisting of 102 flower categories. The flowers chosen to be flower commonly occuring in the United Kingdom. Each class consists of between 40 and 258 images.
- FGVCAircraft: The dataset contains 10,200 images of aircraft, with 100 images for each of 102 different aircraft model variants, most of which are airplanes. The (main) aircraft in each image is annotated with a tight bounding box and a hierarchical airplane model label.

Dataset different from CLIP pretraining data
- Dataset from torchxrayvision: TorchXRayVision is an open source software library for working with chest X-ray datasets and deep learning models.
- Dataset from torchgeo: TorchGeo is a PyTorch domain library, similar to torchvision, that provides datasets, transforms, samplers, and pre-trained models specific to geospatial data.

More dataset will be added in comming weeks.....

### Experiment: 

Classification:
 - For each dataset, sample different amount data for training and value it on test set
 - For each dataset, sample different amount data and add noise to label for training and value it on test set
Clustering
 - Use the input before classification layer as feature, do dimension reduction with Umap and do clustering with Kmeans++. 

### Evaluation

Classification task:
 - PR-AUC: Area under precision and recall curve.
 - Accuracy: Percentage of correction prediction

Clustering task: 
 - homogeneity score: each cluster contains only members of a single class.
 - completeness score: all members of a given class are assigned to the same cluster.
 - v measure score: (2 * homogeneity * completeness)/(homogeneity + completeness)
 - adjusted rand score: Rand index adjusted for chance. The Rand Index computes a similarity measure between two clusterings by considering all pairs of samples and counting pairs that are assigned in the same or different clusters in the predicted and true clusterings.
 - adjusted mutual info score: Adjusted Mutual Information (AMI) is an adjustment of the Mutual Information (MI) score to account for chance. It accounts for the fact that the MI is generally higher for two clusterings with a larger number of clusters, regardless of whether there is actually more information shared.





## Project Timeline
- ~~Week 1-2: Define problems and writing proposal.~~
- ~~Week 3: Do presentation and test feasibility of different kind of models.~~
- Week 4-6: Write training pipeline and clustering pipeline for different models
  - In week 5, we should have a runable pipeline for training and testing
  - In week 6, we should have some result for classification and clustering tasks.
- Week 7-8: Wait for training and test result and focus on further testing on label noise or distribution shift.
- Week 9-10: Start writing report and prepare for presentation.


## Reference

[1] Radford, Alec, et al. "Learning Transferable Visual Models From Natural Language Supervision" *arXiv*. 2021.

[2] Alexey, Lucas, et al. "An Image is Worth 16x16 Words: Transformers for Image Recognition at Scale" 

[3] Zhuang, Hanzi Mao1 et al. "A ConvNet for the 2020s"

[4] Mingxing, Quoc "EfficientNet: Rethinking Model Scaling for Convolutional Neural Networks"

[5] Hangbo, Li, Furu "BEiT: BERT Pre-Training of Image Transformers"

[6] Kaiming, Xinlei et al "Masked Autoencoders Are Scalable Vision Learners"

[7] Mathilde, Hugo et al "Emerging Properties in Self-Supervised Vision Transformers"

[8] Alexei, Wei-Ning et al  "data2vec: A General Framework for Self-supervised Learning in Speech, Vision and Language"

## Appendix

Part of clustering result:

|    | model                                                | dataset      |   homogeneity_score |   completeness_score |   v_measure_score |   adjusted_rand_score |   adjusted_mutual_info_score |   average score |
|---:|:-----------------------------------------------------|:-------------|--------------------:|---------------------:|------------------:|----------------------:|-----------------------------:|----------------:|
|  0 | efficientnet_b0(pretrained=True)                     | FGVCAircraft |            0.280353 |             0.285871 |          0.283085 |           0.00237903  |                   0.0126805  |        0.172874 |
|  1 | Beitfinetuning('facebook@data2vec-vision-base-ft1k') | FGVCAircraft |            0.295534 |             0.298089 |          0.296806 |           0.00529929  |                   0.0249349  |        0.184132 |
|  2 | vit_l_32(pretrained=True)                            | FGVCAircraft |            0.410752 |             0.41757  |          0.414133 |           0.067904    |                   0.19221    |        0.300514 |
|  3 | convnext_tiny(pretrained=True)                       | FGVCAircraft |            0.380526 |             0.38705  |          0.38376  |           0.0532303   |                   0.150006   |        0.270915 |
|  4 | resnet18(pretrained=True)                            | FGVCAircraft |            0.312754 |             0.317196 |          0.31496  |           0.0117581   |                   0.0535246  |        0.202039 |
|  5 | Beitfinetuning()                                     | FGVCAircraft |            0.295325 |             0.300912 |          0.298092 |           0.00669984  |                   0.0331566  |        0.186837 |
|  6 | convnext_small(pretrained=True)                      | FGVCAircraft |            0.333317 |             0.342245 |          0.337722 |           0.0246233   |                   0.0929353  |        0.226169 |
|  7 | mobilenet_v3_small(pretrained=True)                  | FGVCAircraft |            0.290288 |             0.295352 |          0.292798 |           0.00467789  |                   0.0247479  |        0.181573 |
|  8 | efficientnet_b2(pretrained=True)                     | FGVCAircraft |            0.280152 |             0.281896 |          0.281021 |           0.000196982 |                   0.00145821 |        0.168945 |
|  9 | DINOfinetuning()                                     | FGVCAircraft |            0.448063 |             0.457333 |          0.45265  |           0.106496    |                   0.247223   |        0.342353 |
| 10 | resnext50_32x4d(pretrained=True)                     | FGVCAircraft |            0.314706 |             0.318756 |          0.316718 |           0.012788    |                   0.0550234  |        0.203598 |
| 11 | ViTMAEfinetuning()                                   | FGVCAircraft |            0.302224 |             0.304761 |          0.303487 |           0.00669686  |                   0.0341009  |        0.190254 |
| 12 | convnext_base(pretrained=True)                       | FGVCAircraft |            0.347649 |             0.354916 |          0.351245 |           0.0388049   |                   0.108117   |        0.240147 |
| 13 | efficientnet_b6(pretrained=True)                     | FGVCAircraft |            0.367723 |             0.374323 |          0.370994 |           0.0363817   |                   0.133095   |        0.256503 |
| 14 | efficientnet_b4(pretrained=True)                     | FGVCAircraft |            0.280186 |             0.284512 |          0.282332 |           0.00162292  |                   0.00864032 |        0.171459 |
| 15 | CLIPfinetuning()                                     | FGVCAircraft |            0.513868 |             0.526313 |          0.520016 |           0.150122    |                   0.34106    |        0.410276 |
| 16 | mobilenet_v3_large(pretrained=True)                  | FGVCAircraft |            0.285225 |             0.29071  |          0.287941 |           0.00335403  |                   0.0188798  |        0.177222 |
| 17 | vit_b_16(pretrained=True)                            | FGVCAircraft |            0.418914 |             0.423972 |          0.421427 |           0.0742455   |                   0.199492   |        0.30761  |
| 18 | vit_l_16(pretrained=True)                            | FGVCAircraft |            0.413975 |             0.421121 |          0.417517 |           0.070864    |                   0.197035   |        0.304102 |
| 19 | convnext_large(pretrained=True)                      | FGVCAircraft |            0.352846 |             0.359386 |          0.356086 |           0.0393708   |                   0.113318   |        0.244201 |
| 20 | vit_b_32(pretrained=True)                            | FGVCAircraft |            0.403165 |             0.409235 |          0.406178 |           0.0630771   |                   0.180042   |        0.292339 |
| 21 | efficientnet_b0(pretrained=True)                     | Flowers102   |            0.506058 |             0.512237 |          0.509129 |           0.00191332  |                   0.00563277 |        0.306994 |
| 22 | Beitfinetuning('facebook@data2vec-vision-base-ft1k') | Flowers102   |            0.555486 |             0.563237 |          0.559335 |           0.0476004   |                   0.109238   |        0.366979 |
...
| 38 | vit_b_16(pretrained=True)                            | Flowers102   |            0.746649 |             0.763438 |          0.75495  |           0.364618    |                   0.509811   |        0.627893 |
| 39 | vit_l_16(pretrained=True)                            | Flowers102   |            0.752645 |             0.769485 |          0.760972 |           0.371584    |                   0.521877   |        0.635313 |
| 40 | convnext_large(pretrained=True)                      | Flowers102   |            0.706127 |             0.724302 |          0.715099 |           0.290091    |                   0.432502   |        0.573624 |
| 41 | vit_b_32(pretrained=True)                            | Flowers102   |            0.737382 |             0.755445 |          0.746304 |           0.339404    |                   0.493879   |        0.614483 |

---
