---
title: Image augmentation.
date: 25/09/2020 19:38
bookCollapseSection: true
path: web-services/devops/4machine-learning/aspose-omr-for-net/Development/Neural+network+OMR+classification%2E++/image-augmentation
---
### Image augmentation.
# **Image augmentation for OMR**

 

### Links:

* [https://git.saltov.dynabic.com/sikorsky/omr/research/bubbles-recognition-experiments/blob/new_omr/new_omr/augment_data.py](https://git.saltov.dynabic.com/sikorsky/omr/research/bubbles-recognition-experiments/blob/new_omr/new_omr/augment_data.py) - augmentation script.
* [https://wiki.saltov.dynabic.com/wiki/sikorsky/view/OMR.Net/Development/Neural%20network%20OMR%20classification.%20%20/Continuation%20of%20the%20multi-class%20bubble%20classifier%20development%20by%20means%20of%20neural%20networks./](https://wiki.saltov.dynabic.com/wiki/sikorsky/view/OMR.Net/Development/Neural%20network%20OMR%20classification.%20%20/Continuation%20of%20the%20multi-class%20bubble%20classifier%20development%20by%20means%20of%20neural%20networks./) - experiments reslutls.

 

          In order to increase the original omr dataset we decided to apply some image augmentation. The first task was to choose appropriate augmentators for our dataset. After some research, we decided to use these types of augmentations:

* _binarization,_
* _affine shear,_
* _affine rotate,_
* _mirror,_
* _flip,_
* _additive gaussian noise,_
* _gaussian blur_.

    For the binarization, separate research has been done. We decided to use different combinations of image transformations. The pipeline looks like this: first, we take the original dataset and then we apply one of the augmentations, the next step is to concatenate these two datasets. 

 

### First “base” combination.

    We began from the base augmentations which is:

**_binarization_** and **_affine shear + mirror._**

    For each image, we applied the sequence of augmentations.  
This leads to a double increase in the original dataset (original + augmented).

##### **                                                                           examples of "base" augmentation**

![[affine_shear + mirror.png|affine_shear + mirror.png]]

 

### Second combination.

     The second type of our combinations is based on the first one. It means that after the base combination is done, we apply one more combination, _affine shear + flip,_ to the original dataset, which leads us to increase the total dataset by a factor of 3. 

**_binarization _**_and**  affine shear + mirror  **and** affine shear + flip**_

It’s **worth to notice that for class tick, we didn’t apply flip** because it would create unknown mark if multi-classification will be performed.

##### **                                                                               examples of second type augmentation**

 

![[aug_2_.png|aug_2_.png]]

### 

### Third combination.

    This type of combination is just an extension of the second one. Here we just add one more augmented subset (_gaussian noise_) based on the original dataset. 

**_binarization _**_and**  affine shear + mirror  **and** affine shear + flip **and _ **_gaussian_**_ noise_.

#### **                                                                   examples of third type augmentation**  
![[noise.png|noise.png]]

 

### Conclusion:

    We've trained the models with different augmented datasets and concluded that the best result shows the "third combination.". More on that in the [experiment report](https://wiki.saltov.dynabic.com/wiki/sikorsky/view/OMR.Net/Development/Neural%20network%20OMR%20classification.%20%20/Continuation%20of%20the%20multi-class%20bubble%20classifier%20development%20by%20means%20of%20neural%20networks./).  
  
 

### In future:

     Our next possible steps in image augmentations are to apply a few more transformations such as gaussian blur, affine shear.  Also, a good idea would be to experiment with different augmentation combinations.

 

The script for the augmentation can be found [here](https://git.saltov.dynabic.com/sikorsky/omr/research/bubbles-recognition-experiments/blob/new_omr/new_omr/augment_data.py).  
 