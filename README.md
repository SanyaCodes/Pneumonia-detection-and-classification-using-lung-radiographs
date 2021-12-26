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
