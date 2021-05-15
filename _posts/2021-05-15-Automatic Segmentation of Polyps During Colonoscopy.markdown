---
layout: post
comments: true
title:  "[Computer Vision] Automatic Segmentation of Polyps During Colonoscopy"
excerpt: "BMI707 Class Team Project for Deep Learning for Biomedical Data"
date:   2021-05-15 10:00:00
mathjax: false
---

<style>
p {
  text-align: justify;
}
.post pre, .post code {
    border: none;
    background-color: #eee;
}
</style>


**Abstract**

Colonoscopy is a durable colon cancer screen and prevention method in the United States and worldwide. However, there is significant inter-operator variability and adenoma miss rates can be as high as 41%. Deep learning algorithms for computer aided detection and segmentation have been proposed as a potential method to decrease adenoma miss rate and improve colonoscopy. In this study, we aimed to create a deep learning algorithm for the purposes of image segmentation. We used 1,000 polyp images from the Kvasir-SEG database, an open-access data set of polyp images and corresponding segmentation masks. All images were resized to 256 x 256 pixels. Images were also randomly rotated and cropped as well as horizontally flipped to increase the dataset size. We performed image segmentation by training models with the U-net convolutional network architecture. We created two models: one based on U-net architecture without attention gates and one with attention gates. The final optimized hyperparameter values for both models were a learning rate of 1e-4, a batch size of 16, and a weight decay of 1e-8. For the U-net model, the lowest validation IoUBCE loss was -0.93, achieved after 44 epochs. For the attention U-net model, the lowest validation IoUBCE loss was 0.18, achieved after 12 epochs. Attention U-Net showed superior performance in loss results and training speed compared to U-net, however; U-net had better overall results, with a best validation IoU of 0.71 and best training IoU of 0.94, compared to Attention U-net with a best validation IoU of 0.28 and best training IoU of 0.27. In this study, we showed that automatic segmentation of polyps on colonoscopy images is feasible using a deep learning approach. Future iterations of our system could be used for polyp detection and procedural planning during colonoscopy to improve colonoscopy performance and decrease inter-operator variability. More work is needed to improve our model at the ground level before prospective testing and implementation.
<br>

**Introduction**

Colon cancer is the third most common cancer in the United States. It is also the third leading cause of cancer-related death in women and the second leading cause of cancer-related death in men in the United States.1 Most colorectal cancers arise from adenomas, which start in the form of small polyps, grow into larger polyps, and eventually progress to dysplasia and cancer, a process which generally takes 5-10 years from start to finish.2 Colonoscopy with removal of adenomatous polyps remains one of the most effective cancer screening strategies employed in the United States to date.<br>
However, adenoma detection rates vary significantly among physicians,3 and tandem colonoscopy studies have shown that adenoma miss rates can be as high as 41%.4, 5 Recently, several machine learning algorithms aimed at the detection of polyps have emerged as a potential solution to inter-operator variability in colonoscopy quality.6, 7 In addition, several groups have developed deep learning algorithms on static images or stills from video and tested their work on both live video and, more recently, prospectively on patients in randomized clinical trials.8-11 <br>
While computer aided detection has been a significant focus for deep learning research in gastroenterology, there has been less attention on image segmentation. Segmentation of colon polyps can be challenging, due in large part to variation in shape, color intensity and light reflection from frame to frame in colonoscopy images.12 Automatic segmentation is a development goal in its own right, and segmentation can also be used to train subsequent object detection and classification systems during colonoscopy. In this study, we aimed to create a deep learning algorithm for the purposes of image segmentation. <br>
<br>

**Methods**

**_Dataset description and image preprocessing_**

For successful segmentation and detection of polyps, we used the [Kvasir-SEG dataset](https://datasets.simula.no/kvasir-seg/).13 Kvasir-SEG is an open-access data set of polyp images and corresponding segmentation masks. This dataset consists of 1000 polyp images annotated with bounding boxes identifying polyp locations. These were collected from an endoscopy center at Vestre Viken Health Trust in Norway. Polyp boundary annotations were created by a physician and an engineer and annotations were verified by an experienced endoscopist at the Vestre Viken Health Trust. Each image mask consists of regions of interest (ROI) represented by a foreground white mask and a black background that contains no positive pixels (Figure 1). Image resolution ranged from 332x487 pixels to 1920x1072 pixels. A number of preprocessing and image augmentation steps were performed to improve the training accuracy. All images were resized to 256 x 256 pixels. Images were also randomly rotated and cropped as well as horizontally flipped to increase the dataset set size.
<br>

<div style="text-align:center;"><img src="/assets/20210515/figure01.PNG"></div>
figure01<br>


**_Training and Testing_**

We performed image segmentation by training models with the U-net convolutional network architecture. U-net was developed for biomedical image segmentation and consists of an encoder network followed by a decoder network. The encoder network extracts local features by downsampling the image into a lower-dimensional space via a series of convolution and max-pooling layers. The decoder network upsamples the image and projects the lower feature representation onto a higher resolution image (Figure 2).14 We used a VGG backbone for our U-net implementation, which represents the encoder portion of our network.  

<div style="text-align:center;"><img src="/assets/20210515/figure02.PNG"></div>
figure02<br>


We compared the results from two U-net models, one with attention gates (denoted as Attention U-net; Figure 3) and one without attention gates (denoted as U-net). Incorporating attention gates into U-net models resulted in gradients located in background pixels being down-weighted, essentially suppressing irrelevant information in the background region of the image.15 The original Attention U-Net paper used a 3D convolution block instead of the 2D convolution block in U-net because the model was originally designed for evaluating 3D CT images. Our study used a basic U-net model with attention gates because the images in this study are all 2D colonoscopy images. 

<div style="text-align:center;"><img src="/assets/20210515/figure03.PNG"></div>
figure03<br>


Both models were trained for 100 epochs with early stopping if the target criteria improved for 10 epochs. Additional optimized hyperparameter values included the learning rate (1e-4), batch size (16), and weight decay (1e-8). Of the 1000 total images, 800, 100, and 100 images were used for training, validation, and testing respectively.
<br>


**_Evaluation metrics for our deep learning models_**

To evaluate our deep learning model we used the intersection over union (IoU) metric, also known as the Jaccard index, which ranges from 0 to 1 (Figure 4). This method is a popular metric for image segmentation tasks. If the predicted mask has a high overlap with the ground-truth labelled mask, then the IoU score is close to 1. Conversely, if there is no overlap between the predicted mask and the ground truth label, then the IoU value is 0. One advantage of the IoU metric is that it is scale invariant and does not rely on the dimensions of the images or labeled masks.<br>
The IoUBCE loss function we implemented combines IoU loss (defined as the -IOU) with binary cross entropy loss (BCE). This combination of IoU with BCE has been shown to provide increased stability to the loss function.

<div style="text-align:center;"><img src="/assets/20210515/figure04.PNG"></div>
figure04<br>


**Results**

The final optimized hyperparameter values for both models were a learning rate of 1e-4, a batch size of 16, and a weight decay of 1e-8. For the U-net model, the lowest validation IoUBCE loss was -0.93, achieved after 44 epochs. For the attention U-net model, the lowest validation IoUBCE loss was 0.18, achieved after 12 epochs. Attention U-net showed superior performance in loss results and training speed compared to U-net, however; in the current study, U-net had better overall results, with a best validation IoU of 0.71 and best training IoU of 0.94, compared to Attention U-net with a best validation IoU of 0.28 and best training IoU of 0.27.

<div style="text-align:center;"><img src="/assets/20210515/figure05.PNG"></div>
figure05<br>


<div style="text-align:center;"><img src="/assets/20210515/figure06.PNG"></div>
figure06<br>


**Discussion**

In our current study, we showed good model performance for segmentation of polyps in the colon using two deep learning algorithms based on U-net and attention U-net architecture respectively. Several groups have explored traditional machine learning methods for polyp detection and segmentation. In 2003, Karkanis et al. used a color feature extraction system built on wavelet decomposition and a linear discriminant analysis procedure to develop a computer aided detection system that achieved 97% specificity and 90% sensitivity for detection polyps from normal colonic regions.16  In 2014, Fu et al. extracted polyp texture features from 365 generated images, used sequential forward selection and sequential floating forward selection to select input feature vectors for classification and used support vector machines for polyp detection and to classify polyp by type.17 Authors achieved an accuracy for polyp detection by type of 96% versus 85% by an experienced physician. More recently, authors have shown superior performance for polyp detection and segmentation using deep learning methods. Guo et al. used a fully convolutional neural network model for the Gastrointestinal Image ANAlysis (GIANA) polyp segmentation task and won first place in the 2017 challenge in both the standard definition and high definition categories. The resultant model showed a Dice coefficient of 0.8014 and precision of 0.8349.18 More recently, researchers have improved model performance using a ResUnet ++ architecture and have produced more efficient results than previous work by the same authors using U-net and ResUNet systems.19 <br>
Our study has several limitations and there are numerous future steps that we could take to improve our model. With more time and computing power, we would incorporate more annotated images into the model. In addition, we did not have enough information about the dataset to ensure that images were split at the patient level. There may be strong intercorrelations between patient images that can bias training. We also need to manually go through images in our current and future data sets to ensure that no devices (such as polypectomy snares or forceps) were present in polyp-specific images. This would help to minimize label leakage and work towards making our model performance generalizable in future test sets and during prospective validation in the clinical setting. Our images were also collected from a single endoscopy center in Norway. More work needs to be done prospectively using our CADe system on a different endoscopy system as non-imaging characteristics may be built into our current model from the endoscopy encoder and may be confounding our results.20<br>
In addition, the Kvasir-Seg database utilized single images of polyps rather than sequential images of polyps taken from colonoscopy video. Recently, authors have achieved superior performance for computer aided polyp detection using sequential images of polyps taken from colonoscopy video.21 <br>
In conclusion, we showed that automatic segmentation of polyps on colonoscopy images is feasible using a deep learning algorithm based on U-net or attention U-net architecture. Future iterations of our system could be used for polyp detection and procedural planning during colonoscopy to improve colonoscopy performance and decrease inter-operator variability. More work is needed to improve our model at the ground level before prospective testing and implementation.<br>

**References**

1.	Siegel RL, Miller KD, Jemal A. Cancer statistics, 2016. CA Cancer J Clin. 2016; 66: 7-30.
2.	Winawer SJ, Fletcher RH, Miller L, et al. Colorectal cancer screening: clinical guidelines and rationale. Gastroenterology. 1997; 112: 594-642.
3.	Corley DA, Jensen CD, Marks AR, et al. Adenoma detection rate and risk of colorectal cancer and death. N Engl J Med. 2014; 370: 1298-306.
4.	Ahn SB, Han DS, Bae JH, Byun TJ, Kim JP, Eun CS. The Miss Rate for Colorectal Adenoma Determined by Quality-Adjusted, Back-to-Back Colonoscopies. Gut and liver. 2012; 6: 64-70.
5.	Kudo T, Saito Y, Ikematsu H, et al. New-generation full-spectrum endoscopy versus standard forward-viewing colonoscopy: a multicenter, randomized, tandem colonoscopy trial (J-FUSE Study). Gastrointest Endosc. 2018; 88: 854-64.
6.	Tajbakhsh N, Gurudu SR, Liang J. Automated Polyp Detection in Colonoscopy Videos Using Shape and Context Information. IEEE Trans Med Imaging. 2016; 35: 630-44.
7.	Alexandre LA, Nobre N, Casteleiro J. Color and Position versus Texture Features for Endoscopic Polyp Detection.  2008 International Conference on BioMedical Engineering and Informatics, 2008; 38-42.
8.	Misawa M, Kudo SE, Mori Y, et al. Artificial Intelligence-Assisted Polyp Detection for Colonoscopy: Initial Experience. Gastroenterology. 2018; 154: 2027-9 e3.
9.	Wang P, Berzin TM, Glissen Brown JR, et al. Real-time automatic detection system increases colonoscopic polyp and adenoma detection rates: a prospective randomised controlled study. Gut. 2019; 68: 1813-9.
10.	Wu L, Zhang J, Zhou W, et al. Randomised controlled trial of WISENSE, a real-time quality improving system for monitoring blind spots during esophagogastroduodenoscopy. Gut. 2019; 68: 2161-9.
11.	Repici A, Badalamenti M, Maselli R, et al. Efficacy of Real-Time Computer-Aided Detection of Colorectal Neoplasia in a Randomized Trial. Gastroenterology. 2020.
12.	Akbari M, Mohrekesh M, Nasr-Esfahani E, et al. Polyp Segmentation in Colonoscopy Images Using Fully Convolutional Network. Annu Int Conf IEEE Eng Med Biol Soc. 2018; 2018: 69-72.
13.	Jha D, Ali S, Tomar NK, et al. Real-Time Polyp Detection, Localization and Segmentation in Colonoscopy Using Deep Learning. IEEE Access. 2021; 9: 40496-510.
14.	Ronneberger O, Fischer P, Brox T. U-Net: Convolutional Networks for Biomedical Image Segmentation. In: Navab N, Hornegger J, Wells WM, Frangi AF, eds. Medical Image Computing and Computer-Assisted Intervention – MICCAI 2015. Cham: Springer International Publishing, 2015; 234-41.
15.	Oktay O, Schlemper J, Folgoc LL, et al. Attention U-Net: Learning Where to Look for the Pancreas. 2018.
16.	Karkanis SA, Iakovidis DK, Maroulis DE, Karras DA, Tzivras M. Computer-aided tumor detection in endoscopic video using color wavelet features. IEEE Trans Inf Technol Biomed. 2003; 7: 141-52.
17.	Fu JJ, Yu YW, Lin HM, Chai JW, Chen CC. Feature extraction and pattern classification of colorectal polyps in colonoscopic imaging. Comput Med Imaging Graph. 2014; 38: 267-75.
18.	Guo YB, Matuszewski B. Giana polyp segmentation with fully convolutional dilation neural networks.  Proceedings of the 14th International Joint Conference on Computer Vision, Imaging and Computer Graphics Theory and Applications: SCITEPRESS-Science and Technology Publications, 2019; 632-41.
19.	Jha D, Smedsrud PH, Johansen D, et al. A Comprehensive Study on Colorectal Polyp Segmentation with ResUNet++, Conditional Random Field and Test-Time Augmentation. IEEE J Biomed Health Inform. 2021; PP.
20.	Badgeley MA, Zech JR, Oakden-Rayner L, et al. Deep learning predicts hip fracture using confounding patient and healthcare variables. NPJ Digit Med. 2019; 2: 31.
21.	Misawa M, Kudo SE, Mori Y, et al. Development of a computer-aided detection system for colonoscopy and a publicly accessible large colonoscopy video database (with video). Gastrointest Endosc. 2021; 93: 960-7 e3.
