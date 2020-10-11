---
title: Image binarization.
date: 25/09/2020 19:38
bookCollapseSection: true
path: web-services/devops/4machine-learning/aspose-omr-for-net/Development/Neural+network+OMR+classification%2E++/image-binarization
---
### Image binarization.
## Links:

* [https://git.saltov.dynabic.com/sikorsky/omr/research/bubbles-recognition-experiments/blob/new_omr/new_omr/augment_data.py](https://git.saltov.dynabic.com/sikorsky/omr/research/bubbles-recognition-experiments/blob/new_omr/new_omr/augment_data.py) - augmentation/binariaztion script.

# Intro / Summary

          In order to simplify the classification task, we decided to use binarization.  The first approach was to use a naive binarization, where each pixel lower than the given threshold is turning to zero, otherwise, it becomes 255. The first task was to choose that threshold. We run a few experiments and decided that the optimal value for the threshold is 185.

#### **                                                               Examples of naive binarization:**

 

![[Screen Shot 2019-08-25 at 12.45.08 AM.png|Screen Shot 2019-08-25 at 12.45.08 AM.png]]

![[Screen Shot 2019-08-25 at 12.44.58 AM.png|Screen Shot 2019-08-25 at 12.44.58 AM.png]]

 

 

           However, when we started the evaluation of the trained model we noticed that in the validation dataset there are corrupted images. It can be explained by simple shadow on the bubble form. It’s worth to notice that even though we haven't noticed any corrupted images in training dataset, they were probably in there. These are examples from false-negative validation dataset.

#### **                       Examples failed binarizations:**

![[Screen Shot 2019-08-25 at 12.46.32 AM.png|Screen Shot 2019-08-25 at 12.46.32 AM.png]]

 

        Obviously, the simple binarization wouldn’t work in our case, so we decided to use adaptive binarization. After we re-trained the model on the adaptive-binarization dataset, we didn’t notice any corrupted images.  
 

#### **                                                         Examples of adaptive binarization:**

![[Screen Shot 2019-08-25 at 1.12.05 AM.png|Screen Shot 2019-08-25 at 1.12.05 AM.png]]

 

![[Screen Shot 2019-08-25 at 1.14.32 AM.png|Screen Shot 2019-08-25 at 1.14.32 AM.png]]  
 