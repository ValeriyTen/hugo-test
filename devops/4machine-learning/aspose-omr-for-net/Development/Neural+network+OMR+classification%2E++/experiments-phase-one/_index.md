---
title: Experiments. Phase one
date: 25/09/2020 19:38
bookCollapseSection: true
path: web-services/devops/4machine-learning/aspose-omr-for-net/Development/Neural+network+OMR+classification%2E++/experiments-phase-one
---
### Experiments. Phase one
## **    Training experiments with a neural network. **

* [https://files.saltov.dynabic.com/f/3901832](https://files.saltov.dynabic.com/f/3901832) - raw train dataset;
* [https://files.saltov.dynabic.com/f/3901725](https://files.saltov.dynabic.com/f/3901725) raw test dataset;
* [https://files.saltov.dynabic.com/f/3901919](https://files.saltov.dynabic.com/f/3901919) - separated dataset (babbles stored as a separate image, each class in different dirrectory). 

#### **Dataset description:**

      For the dataset, we’ve used photos and scans of marked forms (by hand). The **training** and **validation** **files** can be found [here](https://files.saltov.dynabic.com/apps/files/?dir=/OMR%20NN%20Data/OlegMaterials/Raw%20Data&fileid=3901711) and **the** **full description **of the dataset is [here](https://wiki.saltov.dynabic.com/wiki/sikorsky/view/OMR/Home/Development/OMR%20Recognition%20algorithms/Recognition%20with%20neural%20networks%20experiments/dataset_extraction.ipynb/). Also, we slightly modified the dataset extractor for more convenient usage. Here's the original extractor [pipeline example](https://git.saltov.dynabic.com/sikorsky/omr/research/bubbles-recognition-experiments/blob/new_omr/deep-omr-old/Extraction/dataset_extraction.ipynb).

The dataset consists of 180 raw images where [data_pipeline.py](https://git.saltov.dynabic.com/sikorsky/omr/research/bubbles-recognition-experiments/blob/new_omr/new_omr/extraction/data_pipeline.py) script is used for bubbles extraction. Training script [experiment.py](https://git.saltov.dynabic.com/sikorsky/omr/research/bubbles-recognition-experiments/blob/new_omr/new_omr/experiment.py).

#### **The model:**

As the model, we considered a regular **resnet18** architecture to be our base-line.

## ------------------------------------------------------------------------------------------------------------------------

[[Edit|path:/wiki/sikorsky/edit/OMR.Net/Development/Neural%20network%20OMR%20classification.%20%20/Continuation%20of%20the%20multi-class%20bubble%20classifier%20development%20by%20means%20of%20neural%20networks./WebHome?section=2]]

### **The first experiment, original dataset:**

#### Train the network on the original dataset.

Training dataset size: ~ 72k

**Hyperparameters:**

* epochs: 15
* batch size: 256
* learning rate: 0.00001
* optimizer: SGD
* loss function: categorical cross-entropy   
 

**Results:**

* Training accuracy: 99.99%
* Validation accuracy: ~98.12%

**Confusion matrix:                                                            **

**![[Screen Shot 2019-08-24 at 10.35.00 PM.png|Screen Shot 2019-08-24 at 10.35.00 PM.png]]**

**Validation graphs during the training:**

![[train_accof experiment_origi_5.png|train_accof experiment_origi_5.png]] ![[val_accof experiment_orig5.png|val_accof experiment_orig5.png]] ![[val_lossof experimen_orig5.png|val_lossof experimen_orig5.png]]

**Conclusion:**  
    It can be seen that the network gets a pretty good result on the original dataset. Also, the val accuracy looks quite smooth, however, it is not enough for production quality.

**Some false positive images:**

![[fp_orig_5-15.png|fp_orig_5-15.png]]

PS: you can notice the strage false-positive pattern, that it is mostly empty bubbles with latter B or D inside it.

**Some false negative images:**

**![[fn_orig_5-15.png|fn_orig_5-15.png]]**  
 

## ------------------------------------------------------------------------------------------------------------------------

# **The second experiment (basic aug, aug-1):**

In the next three experiments, we've used threshold binarization. 

Train the network on the augmented dataset.

Augmentation: basic (affine shear + mirror)

Training dataset size: ~ 144k

**Hyperparameters:**

* epochs: 40
* batch size: 256
* learning rate: 0.00001
* optimizer: SGD
* loss function: categorical cross-entropy   
 

**Best result:**

* Training accuracy: 98.93%
* Validation accuracy: 98.28%

**Confusion matrix:                                                                       **

![[cm_4.png|cm_4.png]]

**Validation graphs during the training:**

![[train_accof experiment_basic.png|train_accof experiment_basic.png]] ![[val_accof experiment_basic.png|val_accof experiment_basic.png]] ![[val_lossof experiment_basic.png|val_lossof experiment_basic.png]]

**Conclusion:**

The final validation accuracy looks a bit better than in the previous experiment. 

## ------------------------------------------------------------------------------------------------------------------------

# **The third experiment (aug-2):**

Train the network on the augmented dataset.

Augmentation: basic (affine shear + mirror  and affine shear )

Training dataset size: ~ 210k

**Hyperparameters:**

* epochs: 40
* batch size: 256
* learning rate: 0.00001
* optimizer: SGD
* loss function: categorical cross-entropy   
 

**Results:**

* Training accuracy: ~99.99%
* Validation accuracy: 98.99%

**Confusion matrix:**

**      ![[confusion_matrix_abs_20_ag_20.png|confusion_matrix_abs_20_ag_20.png]]                                                              **

**Validation graphs during the training:**

![[train_accof experiment.png|train_accof experiment.png]] ![[val_accof experiment_ag_3.png|val_accof experiment_aug_2.png]] ![[val_lossof experiment.png|val_lossof experiment.png]]

**Conclusion:**

The final validation accuracy looks a bit better than in the previous experiment. Minimal false positive is 37.

## ------------------------------------------------------------------------------------------------------------------------

# **The fourth experiment (aug 3):**

Train the network on the augmented dataset.

Augmentation:  basic (affine shear + mirror  and affine shear ) 

Training dataset size: ~ 280k

**Hyperparameters:**

* epochs: 40
* batch size: 256
* learning rate: 0.00001
* optimizer: SGD
* loss function: categorical cross-entropy   
 

**Best results:**

* Training accuracy: ~96.5%
* Validation accuracy: 99.41%

**Confusion matrix:**

![[confusion_matrix_abs_32_ag_3.png|confusion_matrix_abs_32_ag_3.png]]

**Validation graphs during the training: **

![[train_accof experiment_ag_3.png|train_accof experiment_aug_2.png]]  ![[val_accof experiment.png|val_accof experiment_aug_3.png]] ![[val_lossof experiment_ag_4.png|val_lossof experiment_ag_4.png]]

**Conclusion:**

The last combination looks the best one. Although the training accuracy looks a bit frequent, the validation accuracy is the highest. Minimal false-positive is 48.

**Some false positive with threshold binarization:**

**![[Screen Shot 2019-08-24 at 11.17.30 PM.png|Screen Shot 2019-08-24 at 11.17.30 PM.png]]**

**Some false negative with threshold binarization:**

![[Screen Shot 2019-08-24 at 11.17.23 PM.png|Screen Shot 2019-08-24 at 11.17.23 PM.png]]

PS: from the image examples above, you can notice corrupted images. The reason is just that some bubbles are in shadow and the static threshold in the binarization.

## ------------------------------------------------------------------------------------------------------------------------

# **The fifth experiment (aug 3 with adaptive binarization):**

Train the network on the augmented dataset.

Augmentation: (affine shear + mirror  and affine shear + flip and noise)

Training dataset size: ~ 280k

**Hyperparameters:**

* epochs: 40
* batch size: 256
* learning rate: 0.00001
* optimizer: SGD
* loss function: categorical cross-entropy   
 

**Best results:**

* Training accuracy: 99%
* Validation accuracy: 99.53%

**Confusion matrix:**

**                                           5-th epoch                                                                                               15-th epo**

![[confusion_matrix_abs_5_ag_4.png|confusion_matrix_abs_5_ag_4.png]]  ![[confusion_matrix_abs_15_ag_4.png|confusion_matrix_abs_15_ag_4.png]]

**Validation graphs during the training:**

![[train_accof experiment_ag_3.png|train_accof experiment_ag_3.png]] ![[val_accof experiment_ag_4.png|val_accof experiment_ag_4.png]] ![[val_lossof experiment_ag_4.png|val_lossof experiment_ag_4.png]]

**Some false positive with adaptive binarization:**

![[Screen Shot 2019-08-24 at 11.13.18 PM.png|Screen Shot 2019-08-24 at 11.13.18 PM.png]]

PS: Again, we can notice the strange pattern in false-positive. Some of the letters (mostly B), the model predicts are as a marked bubble.

**Some false negative with adaptive binarization:**

![[Screen Shot 2019-08-24 at 11.13.33 PM.png|Screen Shot 2019-08-24 at 11.13.33 PM.png]]

**Conclusion:**

After we experimented with the adaptive binarization, the results show that the training process looks almost as smooth as on original dataset, although it converges much faster. Validation accuracy is the best one. while false-positive 44. False-negative is the lowest compared to the other experiments.

### **TODO:**

* Display false-positive results, for more efficent evaluation.
* Experiment more with augmentations (rotate, blur, combinations);
* Try resnet_34 on the last combination;
* Pass to the network paired images;
* Use siamise networks;
* Use custom architecture.

#### {pattern}

#### **The fifth experiment (to do):**

Train the network on the augmented dataset.

Augmentation: 

Training dataset size: ~

**Hyperparameters:**

* epochs: 40
* batch size: 256
* learning rate: 0.00001
* optimizer: SGD
* loss function: categorical cross-entropy   
 

**Results:**

* Training accuracy: %
* Validation accuracy:%

**Confusion matrix:**

**                                           5-th epoch                                                                                               15-th epoch**

**Validation graphs during the training:**

**Conclusion:**

### 