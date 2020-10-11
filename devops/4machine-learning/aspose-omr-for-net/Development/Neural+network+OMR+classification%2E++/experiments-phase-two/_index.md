---
title: Experiments. Phase two
date: 25/09/2020 19:38
bookCollapseSection: true
path: web-services/devops/4machine-learning/aspose-omr-for-net/Development/Neural+network+OMR+classification%2E++/experiments-phase-two
---
### Experiments. Phase two
### Links:

* 

Original dataset: [https://wiki.saltov.dynabic.com/wiki/sikorsky/view/OMR.Net/Development/Neural%20network%20OMR%20classification.%20%20/Dataset/](https://wiki.saltov.dynabic.com/wiki/sikorsky/view/OMR.Net/Development/Neural%20network%20OMR%20classification.%20%20/Dataset/)
* 

Previous training experiments: [https://wiki.saltov.dynabic.com/wiki/sikorsky/view/OMR.Net/Development/Neural%20network%20OMR%20classification.%20%20/Continuation%20of%20the%20multi-class%20bubble%20classifier%20development%20by%20means%20of%20neural%20networks./](https://wiki.saltov.dynabic.com/wiki/sikorsky/view/OMR.Net/Development/Neural%20network%20OMR%20classification.%20%20/Continuation%20of%20the%20multi-class%20bubble%20classifier%20development%20by%20means%20of%20neural%20networks./)
* 

Augmentation: [https://wiki.saltov.dynabic.com/bin/view/Machine%20Learning/aspose-omr-for-net/Development/Neural%20network%20OMR%20classification.%20%20/Image%20augmentation./](https://wiki.saltov.dynabic.com/bin/view/Machine%20Learning/aspose-omr-for-net/Development/Neural%20network%20OMR%20classification.%20%20/Image%20augmentation./)

 

 

### Dataset

    After the previous experiment, we have noticed that the original training dataset was not perfect. There were about 300 mislabeled images. We decided to clean the dataset with our best model. We have run the whole training dataset through the network and manually checked the error. Then, we applied an augmentation and retrained the network.

 

 

**Hyperparameters:**

* epochs: 33
* batch size: 256
* learning rate: 0.00001
* optimizer: SGD
* loss function: categorical cross-entropy   
 

**Results:**

Validation multi-class accuracy: 0.988

Validation binary accuracy: 99.8%

**Confusion matrix:                           **

**![[confusion_matrix_abs_e21:9961821056.png|confusion_matrix_abs_e21:9961821056.png]]**

This is an example of false-positive results:  
  
![[Screen Shot 2019-11-04 at 7.41.40 PM.png|Screen Shot 2019-11-04 at 7.41.40 PM.png]]  
  
Conclusion:  
  
    When we clean the dataset and re-trained the model we got a quite good performance boost, from 98.8 to 99.8 % in when it comes to binary classification. However, on multi-class classification, the network still has some problems dealing with classification between a dot and empty.  
![[1.png|1.png]]![[2.png|2.png]]  
  
From the picture above, you can see the uncertainty.  
  
Possible improvements:

* train the network on normalize input;
* experiment with optimizers;
* experiment with augmentations;
* use regularization;
* use warm-up;