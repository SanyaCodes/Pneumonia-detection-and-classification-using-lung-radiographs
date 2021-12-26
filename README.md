# Pneumonia Detection and Classification using Lung Radiographs

#### Challenge Via Kaggle : SIIM-FISABIO-RSNA COVID-19 Detection (https://www.kaggle.com/c/siim-covid19-detection/overview)
## Task :

This is an object detection and classification problem. For each test image, a bounding box and class need to be predicted for all findings. If there are none,  they are labelled as "none 1 0 0 1 1" ("none" is the class ID for no finding, and this provides a one-pixel bounding box with a confidence of 1.0). Further, for each test study, the following determinations need to be made :

<li> Negative for Pneumonia </li>
<li> Typical Appearance </li>
<li> Indeterminate Appearance </li>
<li> Atypical Appearance </li>

## Dataset :

The train dataset comprises 6,334 chest scans in **DICOM** format, which were de-identified to protect patient privacy. All images were labeled by a panel of experienced radiologists for the presence of opacities as well as overall appearance. Note that all images are stored in paths with the form study/series/image. The study ID here relates directly to the study-level predictions, and the image ID is the ID used for image-level predictions.

The hidden test dataset is of roughly the same scale as the training dataset.

<img src="https://www.googleapis.com/download/storage/v1/b/kaggle-user-content/o/inbox%2F603584%2Fd514aaf604bc9667b518b232a77d1aa7%2FCXR%20image1.jpg?generation=1620769201081719&alt=media" alt="lung" width=200/>

### Files :

<li> train_study_level.csv - the train study-level metadata, with one row for each study, including correct labels. </li>
<li> train_image_level.csv - the train image-level metadata, with one row for each image, including both correct labels and any bounding boxes in a dictionary format. Some images in both test and train have multiple bounding boxes. </li>
<li> sample_submission.csv - a sample submission file containing all image- and study-level IDs. </li>

## MY APPROACH : 

### 1. Balancing Dataset :
Plotting the data on a pie chart, it is seen that the data is unbalanced and since this is a object detection and classification task, the results are greatly affected by the distribution of data across classes.

Initial distribution :
<li> Typical Appearance          3007 </li>
<li> Negative for Pneumonia      1736 </li>
<li> Indeterminate Appearance    1108 </li>
<li> Atypical Appearance          483 </li>
<li> Name: target, dtype: int64 </li>

<img src="https://github.com/Ssanyachetwani/Pneumonia-detection-and-classification-using-lung-radiographs/blob/main/rim/rim1.png?raw=true" alt="dataub" width=350/>

Upon balancing this data set using Image Augmentation technique, the dataset becomes equally distributed across all 4 classes :
<li> Typical Appearance          3007 </li>
<li> Negative for Pneumonia      3472 </li>
<li> Indeterminate Appearance    3314 </li>
<li> Atypical Appearance         2415 </li>
<li> Name: target, dtype: int64 </li>

<img src="https://github.com/Ssanyachetwani/Pneumonia-detection-and-classification-using-lung-radiographs/blob/main/rim/rim2.png?raw=true" alt="datab" width=350/>

### 2. Masking :
When processing images, the term 'masking' refers to the practice of using a mask to protect a specific area of an image, just as we would use masking tape when painting the house. Masking an area of an image protects that area and deletes rest of the image, allowing the machine to focus on a single zone, rather than spreading attention to areas which are not of any use. 

Here I used UNet, the masked images obtained were of this kind :

<img src="https://github.com/Ssanyachetwani/Pneumonia-detection-and-classification-using-lung-radiographs/blob/main/rim/masking.png?raw=true" alt="dataub" width=250/>

### 3. Decolorizer :

The main aim of this step is to convert the B&W radiographs to color. This step is helpful in detecting pneumonia since the areas of the lungs affected are highlighted. It can be visually seen that it is easier to detect pneumonia in the colored images.

<img src="https://github.com/Ssanyachetwani/Pneumonia-detection-and-classification-using-lung-radiographs/blob/main/rim/original.png?raw=true" alt="datab" height=250/>                   <img src="https://github.com/Ssanyachetwani/Pneumonia-detection-and-classification-using-lung-radiographs/blob/main/rim/deoldify.png?raw=true" alt="datab" height=250/>

We make use of an open source library DeOldify to colorize the grayscale radiographs - https://github.com/jantic/DeOldify.

### 4. Superpixel Segmentation :
In an image the neighbouring pixels share similar characteristics, as a result there is a lot of redundant information which makes training difficult. A superpixel is a group of pixels that share common characteristics. 
Superpixels have the following advantages:
<li> Carry more information than pixels </li>
<li> They provide a convenient and compact representation of images that can be very useful for computationally demanding problems </li>
<li> Superpixels have a perceptual meaning since pixels belonging to a given superpixel share similar visual properties</li>

<img src="https://github.com/Ssanyachetwani/Pneumonia-detection-and-classification-using-lung-radiographs/blob/main/rim/superpixel.png?raw=true" alt="datab" width=350/> 

### 5. YOLOv5 Ensemble:
I made use of YoloV5 for detection of typical, indeterminate and atypical classes of pneumonia. Seperate models were trained for each class. YoloV5 can be used for multiclass detection, however it was observed that the accuracy suffered. The models performed significantly better when they were trained to detect every class individually. I created a set of 3 models for every class respectively. I make use of One Vs Rest (OVR) ensembling technique which combines the decision from several models to improve the overall performance.  

<img src="https://github.com/Ssanyachetwani/Pneumonia-detection-and-classification-using-lung-radiographs/blob/main/rim/ensemble.png?raw=true" alt="datab" height=200/>

Ensembling methods:
<li> OR method (Affirmative): A box is considered if it’s generated by at least one of the models </li>
<li> AND method (Unanimous): A box is considered if all of the models generate the same box (the box is considered the same if IOU > 0.5) </li>
<li> Consensus method: A box is considered if the majority of the models generate the same box (ie) if there are m models and (m/2 +1) models generate the same box, that box is considered as valid </li>
<li> Weighted Fusion: This is a novel method which was created to replace NMS and it’s shortcomings </li>

<img src="https://github.com/Ssanyachetwani/Pneumonia-detection-and-classification-using-lung-radiographs/blob/main/rim/typical.png?raw=true" alt="datab" height=250/>      <img src="https://github.com/Ssanyachetwani/Pneumonia-detection-and-classification-using-lung-radiographs/blob/main/rim/indeterminate.png?raw=true" alt="datab" height=250/>      <img src="https://github.com/Ssanyachetwani/Pneumonia-detection-and-classification-using-lung-radiographs/blob/main/rim/atypical.png?raw=true" alt="datab" height=250/>
 

## Results :
It is important to compare the results with different combinations in the pipeline, hence I compared results by applying **SUPERPIXEL + DEOLDIFY** along with only **SUPERPIXEL** and **DELODIFY** on three parameters : Precision, Recall and maP. The results have been tabulated below

Superpixel + DeOldify
| Class | Typical  | Indeterminate  | Atypical  |
| ------- | --- | --- | --- |
| Precision | 0.75 | 0.95 | 0.94 |
| Recall | 0.63 | 0.86 | 0.76 |
| maP | 0.67 | 0.73 | 0.73 |

Superpixel
| Class | Typical  | Indeterminate  | Atypical  |
| ------- | --- | --- | --- |
| Precision | 0.52 | 0.8 | 0.83 |
| Recall | 0.56 | 0.62 | 0.63 |
| maP | 0.3 | 0.61 | 0.65 |

DeOldify
| Class | Typical  | Indeterminate  | Atypical  |
| ------- | --- | --- | --- |
| Precision | 0.49 | 0.76 | 0.71 |
| Recall | 0.48 | 0.55 | 0.52 |
| maP | 0.28 | 0.61 | 0.6 |
