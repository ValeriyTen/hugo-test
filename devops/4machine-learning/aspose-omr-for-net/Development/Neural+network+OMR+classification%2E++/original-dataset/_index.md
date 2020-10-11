---
title: Original dataset
date: 25/09/2020 19:38
bookCollapseSection: true
path: web-services/devops/4machine-learning/aspose-omr-for-net/Development/Neural+network+OMR+classification%2E++/original-dataset
---
### Original dataset
#### **Links**:

* [https://files.saltov.dynabic.com/f/3901832](https://files.saltov.dynabic.com/f/3901832) - raw train dataset;
* [https://files.saltov.dynabic.com/f/3901725](https://files.saltov.dynabic.com/f/3901725) - raw validation dataset;
* [https://git.saltov.dynabic.com/sikorsky/omr/research/bubbles-recognition-experiments/blob/master/deep-omr-old/Extraction/dataset_extraction.ipynb](https://git.saltov.dynabic.com/sikorsky/omr/research/bubbles-recognition-experiments/blob/master/deep-omr-old/Extraction/dataset_extraction.ipynb) - original extraction pipeline;
* [https://wiki.saltov.dynabic.com/wiki/sikorsky/view/OMR/Home/Development/OMR%20Recognition%20algorithms/Recognition%20with%20neural%20networks%20experiments/dataset_extraction.ipynb/](https://wiki.saltov.dynabic.com/wiki/sikorsky/view/OMR/Home/Development/OMR%20Recognition%20algorithms/Recognition%20with%20neural%20networks%20experiments/dataset_extraction.ipynb/) - original extraction overview;
* [https://files.saltov.dynabic.com/f/3901919](https://files.saltov.dynabic.com/f/3901919) - original dataset (stored as each class of bubbles in a separate direcorry). 
* [https://git.saltov.dynabic.com/sikorsky/omr/research/bubbles-recognition-experiments/blob/new_omr/file_examples/3.1_1_pols.json](https://git.saltov.dynabic.com/sikorsky/omr/research/bubbles-recognition-experiments/blob/new_omr/file_examples/3.1_1_pols.json) - polygon file exmple;
* [https://git.saltov.dynabic.com/sikorsky/omr/research/bubbles-recognition-experiments/blob/new_omr/file_examples/patterns.json](https://git.saltov.dynabic.com/sikorsky/omr/research/bubbles-recognition-experiments/blob/new_omr/file_examples/patterns.json) - patterns file examples.

 

### **Dataset**

    The original dataset has 12 different hand-labeled template forms. 83000 bubbles in total. (72 000 training, 12000 validation)  
For multiclassification, there are 6 different classes:

* empty:

![[emp.png|emp.png]]

* fill;

![[fill.png|fill.png]]

* dot;

![[dot.png|dot.png]]

* tick;

![[tick.png|tick.png]]

* cross;

![[cross.png|cross.png]]

* bar (vertical line).

![[bar.png|bar.png]]

Wheres for binary there are only two classes:

* empty;
* fill (fill, dot, tick, cross, bar).

      The dataset stored as 180 printed forms. Also, there are some auxiliary files, such as [polygon files](https://git.saltov.dynabic.com/sikorsky/omr/research/bubbles-recognition-experiments/blob/new_omr/file_examples/3.1_1_pols.json) - with bubbles locations, and [pattern file](https://git.saltov.dynabic.com/sikorsky/omr/research/bubbles-recognition-experiments/blob/new_omr/file_examples/patterns.json) with bubbles labels.   
 

The dataset balance:

![[dataset_bal.png|dataset_bal.png]]

We find it reasonable, that there is such disbalance because the main goal is to classify bubbles with the lowest false-positive error.

    However, for more convenient usage, we decided to cut out the bubbles from the form and store it in the way that in each sub-directory there are bubbles of a particular class. 

Few examples:  
 

Form examples:  
![[3_2.jpg|3_2.jpg]]![[1_2.jpg|1_2.jpg]]