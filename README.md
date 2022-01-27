# severstal-steel-defect-detection

Definition, Analysis, and Conclusion section

1. Business Problem

    Steel is one of the most important building materials of modern times. Steel buildings are resistant to natural and man-made wear which has made the material ubiquitous around the world. The production process of flat sheet steel is especially delicate. From heating and rolling, to drying and cutting, several machines touch flat steel by the time it’s ready to ship.
    
    Today, Severstal is leading the charge in efficient steel mining and production. Severstal is now looking for machine learning to identify defects in steel which will help make production of steel more efficient. This competition will help engineers improve the defect detection algorithm by localizing and classifying surface defects on a steel sheet.

2. Source of Data
    
    It is a Kaggle competition held by Severstal. Data is available here.
3. Data Overview

   - train_images/ — folder with 12568 .jpg training images.
   - test_images/ — folder with 5516 .jpg test images (we are segmenting and classifying these images).
   - train.csv — training annotations which provide segments for defects with total 4 defect classes (ClassId=[1,2,3,4]).
   - test.csv — a submission file in the correct format (for each ImageId 4 rows, one for each of the 4 defect classes).
   - Each image is of 256x1600 resolution
   
4. Mapping real world problem with Deep Learning problem

   4.1. Image Segmentation

        - Image segmentation is the process of partitioning a digital image into multiple segments. 
          A segmentation model returns much more detailed information about the image. 
          More precisely, image segmentation is the process of assigning a label to every pixel in an image such that pixels with the same label share certain characteristics. 
          Image segmentation has many applications in medical imaging, self-driving cars and satellite imaging to name a few.

   The Different Types of Image Segmentation

      A) Semantic Segmentation
         - Every pixel belongs to a particular class(either background or person). Also, all the pixels belonging to a particular class are represented by the same color (background as black and person as pink). This is an example of semantic segmentation.
            
![alt tag](https://github.com/changhyucklee/severstal-steel-defect-detection/blob/main/asset/SemanticSegmentation.png)
    
      B) Instance Segmentation
         - Here also assigned a particular class to each pixel of the image. However, different objects of the same class have different colors (Person 1 as red, Person 2 as green, background as black, etc.). This is an example of instance segmentation

![alt tag](https://github.com/changhyucklee/severstal-steel-defect-detection/blob/main/asset/InstanceSegmentation.png)

      For Steel defect detection we will use Semantic Segmentation.

   4.2. Encoded Pixels

        In order to reduce the submission file size, metric uses run-length encoding on the pixel values. Instead of submitting an exhaustive list of indices for segmentation, I will make pairs of values that contain a start position and a run length. 
        E.g. ‘1 3’ implies starting at pixel 1 and running a total of 3 pixels (1,2,3).
        The competition format requires a space delimited list of pairs. 
        For example, ‘1 3 10 5’ implies pixels 1,2,3,10,11,12,13,14 are to be included in the mask. 
        The metric checks that the pairs are sorted, positive, and the decoded pixel values are not duplicated. 
        The pixels are numbered from top to bottom, then left to right: 1 is pixel (1,1), 2 is pixel (2,1), etc.

7. Performance Metrics

   - The Dice coefficient can be used to compare the pixel-wise agreement between a predicted segmentation and its corresponding ground truth.

   The formula is given by:

   where A is the predicted set of pixels and B is the ground truth. The Dice coefficient is defined to be 1 when both A and B are empty.

![alt tag](https://github.com/changhyucklee/severstal-steel-defect-detection/blob/main/asset/PerformnaceMetrics.png)

6. Objective
  
   - Each image may have no defects, a defect of a single class, or defects of multiple classes. For each image one must segment defects of each class (ClassId = [1,2,3,4]).
   - With this web application, we can analysis defect distribution  and compare targe image and predicted defect mask with train / test images.

![alt tag](https://github.com/changhyucklee/severstal-steel-defect-detection/blob/main/asset/SteelDefectDectionScreen.png)

7. Instructions
   1> EDA_&_Data_Preprocessing.ipynb : Exploratory Data Analysis
   2> UNET.ipynb : preprocssing and machine learning model generation and submission file(reanmed to submission.csv to test.csv)
   3> Ananlysis_Defect.py : Web application to analyze defect distribution and predition result.
      - Run the following command in the app's directory to run your web app. python Ananlysis_Defect.py 
      - Got to http://127.0.0.1:8005
      - Random date has assing to train.csv and test.csv to navigate by dates and dropdowbox to mimic more real data analysis.
      - If you select the date and it show different distribution of defect and select random image in selected period.
      - If you select the defect class (1~4), it shows random 3 target image and mask images.

![alt tag](https://github.com/changhyucklee/severstal-steel-defect-detection/blob/main/asset/SteelDefectDectionScreen2.png)   

8. Exploratory Data Analysis

![alt tag](https://github.com/changhyucklee/severstal-steel-defect-detection/blob/main/asset/DefectDistribution.png)

  - The data is very imbalanced.
  - Data augmentation and resampling techniques will be required to perform the defect detection.

![alt tag](https://github.com/changhyucklee/severstal-steel-defect-detection/blob/main/asset/NoOfLabelsPerImage.png)

  - There are 5902 images with no labels, 6239 images with 1 label, 425 images with 2 labels and 2 images with 3 labels.
  - Almost half of images doesn’t contain any defects.
  - Most of images with defects contain the defects of only one type.
  - In rare cases an image contains the defects of two different types.

Plot of few images for each defect

![alt tag](https://github.com/changhyucklee/severstal-steel-defect-detection/blob/main/asset/ImageForEachDefect.png)

Mask Area

![alt tag](https://github.com/changhyucklee/severstal-steel-defect-detection/blob/main/asset/MaskArea.png)

  - Mask area for each defect will help to decide area thresholds during segment prediction (later at the time of modelling).
  - Masks with large areas seem very suspicious to me, so I will try to plot few images with large mask areas picked by random index.

![alt tag](https://github.com/changhyucklee/severstal-steel-defect-detection/blob/main/asset/Defect_3_image.png)

   Large masks seem to be okay except for the fact that these masks seem to contain a lot of empty space without any defects.
   
8. Future Work
   - I have use submission file(test.csv) to checked the prediction result.
     But I think that I can modify this program to select small amount of test image to predict and check the result image.
   - I can add function to upload new submission file which is from other kaggler's files and check the result.

10. References
  - https://segmentation-models.readthedocs.io/en/latest/api.html#unet
  - https://www.kaggle.com/paulorzp/rle-functions-run-lenght-encode-decode
  - https://dipanshurana.medium.com/steel-defect-detection-image-segmentation-using-keras-and-tensorflow-6118bc586ad2
  - https://www.kaggle.com/cdeotte/keras-unet-with-eda
  - https://www.kaggle.com/hengck23/efficientb0-unet-plus-v4-1/script
