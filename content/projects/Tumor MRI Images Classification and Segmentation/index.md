---
title: "Brain Tumor MRI Images Classification and Segmentation"
date: 2025-02-18
author: ["Chenhao Wu", "Yubing Zhao", "Tongyu Wu", "Yuntian Zhang"]
description: "Imperial College Winter School Honor Project"
longImage: "longpic.png"
showImage: "showpic.png"
katex: true
---

- github link: https://github.com/G-dab/Brain-Tumor-MRI-Images-Classification-and-Segmentation
- CV Report: 

## ABSTRACT

Non-invasive imaging techniques, particularly Magnetic Resonance Imaging (MRI), play a pivotal role in diagnosing brain tumors by providing detailed anatomical and functional insights. In the classification task, we employed the VGG16 neural network architecture and achieved an accuracy of 96% on the test set. For the segmentation task, we referred to the U-Net architecture, designed our own loss function, and incorporated extensive data augmentation techniques. Ultimately, we developed a U-Net3D network architecture that directly processes 3D files. The Dice coefficient reached 83.0% on the training set and 83.2% on the test set.

**Keywords**: MRI · Brain tumor · Image classification · VGG16 · Image segmentation · Unet3D

## 1 Literature Review

### 1.1 Overview of Non-invasive Imaging Techniques

Non-invasive techniques like CT, PET, and MRI are widely used for diagnosing internal organs. CT uses X-rays, and PET involves radioactive isotopes, while MRI, being harmless, provides clear imaging and is often chosen for diagnosing organ abnormalities. Despite advancements in deep learning, confirming the malignancy of brain tumors non-invasively remains a challenge.

### 1.2 MRI Imaging Principle and Image Types

MRI works by using a magnetic field to excite hydrogen atoms in the body, which emit radio frequency (RF) pulses when the field is removed. These atoms return to their original state, and the time required for this process is called the relaxation time, which is divided into longitudinal (T1) and transverse (T2) relaxation times [1][2]. Based on that, three main MRI sequences are obtained: T1-weighted (T1w), T2-weighted (T2w), and FLAIR. T1w images show bone structures, T2w highlights tumor cores, and FLAIR suppresses normal CSF, enhancing the visibility of abnormal tissues, crucial for tumor detection.

### 1.3 Tumor MRI Image Classification

Various methods have been developed for classifying brain tumors in MRI images. CNNs are frequently used in tumor classification. Jun Cheng et al. [3] achieved 94.68 accuracy without neural networks, while Gawande and Mendre [4] employed DNNs and autoencoders. Pereira et al. [5] used small 3x3 kernels to reduce overfitting. Other architectures like AlexNet [6], VGG16 [5], and ResNet [7] were tested, showing the flexibility of CNNs for brain tumor classification.

### 1.4 Tumor MRI Image Segmentation

Traditional segmentation methods rely on techniques like edge detection and region growing. However, CNNs have revolutionized this task, eliminating manual feature engineering. U-Net, proposed by Ronneberger et al. [8], is a deep learning network designed for medical image segmentation. Its key feature is the use of skip connections, allowing for precise localization while preserving spatial information. U-Net’s architecture has been enhanced through modifications such as skip connection improvements, backbone design changes, and transformer integration [9]. For 3D data, Cicek et al. [10] introduced 3D U-Net, which reduces manual annotation efforts by utilizing adjacent slice information, improving segmentation performance in volumetric datasets. [11] In 2024, Paul Jaeger et al. reassessed the performance of the nnU-Net framework in 3D medical image segmentation, providing empirical evidence supporting the continued competitiveness of 3D U-Net. [12] In the same year, Tianrun Chen et al. proposed using Vision-LSTM as the backbone of U-Net, combining the local feature extraction capability of convolution with the long-range dependency modeling of LSTM. Recent models like the Segment Anything Model (SAM) [13] and its adaptation MedSAM [14] have further advanced segmentation. SAM uses prompt-based learning, allowing for zero-shot segmentation across diverse tasks. MedSAM, optimized for high-resolution medical images, improves segmentation accuracy by leveraging expert annotations and adapting to 3D CT and MRI data. These advancements, especially with U-Net and its variants, have significantly enhanced segmentation efficiency and accuracy in medical imaging, particularly for brain tumor detection.

## 2 Classification

### 2.1 Design of a Transfer Learning Classification Model Based on VGG16

#### 2.1.1 DataGenerator

Use keras.utils.Sequence to implement a custom data generator (DataGenerator) for batch loading of .npy format image data, performing data preprocessing, augmentation, and binary classification based on segmentation data.

#### 2.1.2 VGG16 Transfer Learning

Using VGG16 shown in Figure 1 as a feature extractor by loading ImageNet pre-trained weights, removing the original fully connected layers while retaining only the convolutional part. The last 12 convolutional layers are unfrozen, allowing the model to adapt to a new classification task while reducing computational cost.

<center>
<img src=pic1.png width=60%>
<br>
<div style="font-size: 16px; color: #333;">
    Figure 1: VGG16 network architecture.
</div>
</center>

#### 2.1.3 Training Process and Early Stopping Strategy

A well-designed early stopping strategy can effectively prevent the model from overfitting. If the validation accuracy (val_accuracy) does not improve within 15 epochs, training will be halted, and the model will revert to the best weights. The learning rate is dynamically adjusted if the validation loss (val_loss) does not show improvement within 6 epochs; the learning rate is reduced by 20%, with a lower bound of 1e-9 to prevent the learning rate from becoming too small. In the model training phase, the optimal model was obtained by adjusting the early stopping condition, modifying the number of trainable layers, and modifying the dynamic adjustment of the learning rate parameter.

<center>
<img src=pic2.png width=60%>
<br>
<div style="font-size: 16px; color: #333;">
    Figure 2: Test Accuracy Improvement Over Adjustments.
</div>
</center>

## 3 Segmentation

### 3.1 Data Visualization

The provided dataset consists of 210 3D brain MRI images with a resolution of 240×240×155. Each entry includes two files: xxx_fla.nii.gz and xxx_seg.nii.gz, which represent the 3D brain MRI image and the tumor mask data, respectively. .nii is a medical imaging data format that stores 3D MRI image data of the brain. Each data entry comprises 155 axial slices. There are two ways to visualize the data.

The most straightforward method is to utilize the ITK-SNAP software. By importing the two files as visualization data and mask data respectively, one can observe the brain MRI images and the corresponding tumor locations from various sectional views, as depicted in Figure 3.

<center>
<img src=pic3.png width=60%>
<br>
<div style="font-size: 16px; color: #333;">
    Figure 3: Visualization using ITK-SNAP software. The tumor region is marked in red in Figure 3b.
</div>
</center>

Alternatively, the Python library Nibabel can be employed to read and load the data as three-dimensional arrays. We have also conducted some visualization demonstrations, as shown in Figure 4.

<center>
<img src=pic4.png width=60%>
<br>
<div style="font-size: 16px; color: #333;">
    Figure 4: Visualization using Nibabel library in Python.The brain slices are visualized by overlaying multiple axial
views, with the tumor regions marked in red.
</div>
</center>

### 3.2 Data Argumentation

In the initial training phase, we did not incorporate data augmentation. However, this led to overfitting, as evidenced by the high accuracy on the training set and the significantly lower accuracy on the validation set. By introducing data augmentation, we were able to reduce the model’s sensitivity to specific image features, thereby enhancing its generalization capability.

<center>
<img src=pic5.png width=60%>
<br>
<div style="font-size: 16px; color: #333;">
    Figure 5: Several data augmentation methods
</div>
</center>

### 3.3 Methodology

#### 3.3.1 Loss Function

For medical image segmentation, the IoU loss and Dice loss are commonly used as loss functions. They are defined as below.

$$
\text{IoU loss} = 1 - \frac{Area\quad of\quad Overlap}{Area\quad of\quad Union}
$$

$$
\text{Dice loss} = 1 - \frac{2 \times Area\quad of\quad Overlap}{Total\quad Area}
$$

Here we combine the binary cross-entropy loss with the dice loss [15]. The overall loss function is calculated as below:

$$
    L_{total} = L_{BCE} - D
$$

$$
    L_{BCE} = -\frac{1}{N} \sum_{i=1}^{N} \left[ y_i \log(\hat{y}_i) + (1 - y_i) \log(1 - \hat{y}_i) \right]
$$


#### 3.3.2 Optimizer

We chose Adam optimizer as our optimizer. It is widely used in deep learning due to its efficiency and adaptability. Adam combines the advantages of both RMSprop and Momentum methods, allowing for adaptive learning rates and faster convergence.

#### 3.3.3 Network Architecture

We have replicated the 3D U-Net architecture. The network consists of an encoder path and a decoder path, each with four resolution steps.

<center>
<img src=pic6.png width=60%>
<br>
<div style="font-size: 16px; color: #333;">
    Figure 6: 3D U-Net architecture
</div>
</center>

### 3.4 Experiment Design

All experiments were conducted on a NVIDIA GeForce RTX 4090 with 24GB of memory.

**Data Preprocessing**: Dataset is randomly divided into an 80% training set and a 20% validation set. Before training, the non-zero regions of the 3D images in the entire dataset are normalized. Subsequently, data augmentation is performed on the training set, including random flipping, random rotation, random scaling, adding noise, and random cropping.

**Training**: Due to limitations of memory, we set the batch size of the model at 1 and the learning rate at 3 × 10−4. We used StepLR module to dynamically decrease the learning rate in the training process to achieve higher accuracy.

**Results**: During training, we initially refrained from using extensive data augmentation techniques and only applied simple normalization. The results are shown in Figure 7a. The model was trained for a total of 30 epochs, achieving a Dice coefficient of 80% on the training set and 83% on the validation set. After employing data augmentation techniques, the results are shown in Figure 7b. The model was trained for 30 epochs, achieving a Dice coefficient of 83.0% on the training set and 83.2% on the validation set. It can be observed that the accuracy on both the training and validation sets is more balanced, which reduces the overfitting of the model and enhances its robustness.

<center>
<img src=pic7.png width=60%>
<br>
<div style="font-size: 16px; color: #333;">
    Figure 7: Training Results
</div>
</center>

Finally, a series of data was randomly selected, and the prediction results along with the ground truth are shown in Figure 8. It can be observed that most regions of the prediction are consistent with the ground truth, with only a few areas of significant color change failing to be accurately predicted.

<center>
<img src=pic8.png width=60%>
<br>
<div style="font-size: 16px; color: #333;">
    Figure 8: Training Results shown with ITK-SNAP
</div>
</center>

## References

[1] Stuart Clare. Functional mri: methods and applications. University of Nottingham, page 155, 1997.  
[2] Lakshmaiah C Kuntegowdenahalli, Linu Abraham Jacob, Ashok S Komaranchath, and Usha Amirtham. A rare case of primary anaplastic large cell lymphoma of the central nervous system. Journal of Cancer Research and Therapeutics, 11(4):943–945, 2015.  
[3] Jun Cheng, Wei Huang, Shuangliang Cao, Ru Yang, Wei Yang, Zhaoqiang Yun, Zhijian Wang, and Qianjin Feng. Enhanced performance of brain tumor classification via tumor region augmentation and partition. PloS one, 10(10):e0140381, 2015.  
[4] SS Gawande and V Mendre. Brain tumor diagnosis using deep neural network (dnn). International Journal of Advanced Research in Electrical, Electronics and Instrumentation Engineering, 5(5):10196–10203, 2017.  
[5] Sérgio Pereira, Adriano Pinto, Victor Alves, and Carlos A Silva. Brain tumor segmentation using convolutional neural networks in mri images. IEEE transactions on medical imaging, 35(5):1240–1251, 2016.  
[6] Alex Krizhevsky, Ilya Sutskever, and Geoffrey E Hinton. Imagenet classification with deep convolutional neural networks. Advances in neural information processing systems, 25, 2012.  
[7] Karen Simonyan. Very deep convolutional networks for large-scale image recognition. arXiv preprint arXiv:1409.1556, 2014.  
[8] Olaf Ronneberger, Philipp Fischer, and Thomas Brox. U-net: Convolutional networks for biomedical image segmentation. In Medical image computing and computer-assisted intervention–MICCAI 2015: 18th international conference, Munich, Germany, October 5-9, 2015, proceedings, part III 18, pages 234–241. Springer, 2015.  
[9] Reza Azad, Ehsan Khodapanah Aghdam, Amelie Rauland, Yiwei Jia, Atlas Haddadi Avval, Afshin Bozorgpour, Sanaz Karimijafarbigloo, Joseph Paul Cohen, Ehsan Adeli, and Dorit Merhof. Medical image segmentation review: The success of u-net. IEEE Transactions on Pattern Analysis and Machine Intelligence, 2024.  
[10] Özgün Çiçek, Ahmed Abdulkadir, Soeren S Lienkamp, Thomas Brox, and Olaf Ronneberger. 3d u-net: learning dense volumetric segmentation from sparse annotation. In Medical Image Computing and Computer-Assisted Intervention–MICCAI 2016: 19th International Conference, Athens, Greece, October 17-21, 2016, Proceedings, Part II 19, pages 424–432. Springer, 2016.  
[11] Fabian Isensee, Tassilo Wald, Constantin Ulrich, Michael Baumgartner, Saikat Roy, Klaus Maier-Hein, and Paul F Jaeger. nnu-net revisited: A call for rigorous validation in 3d medical image segmentation. In International Conference on Medical Image Computing and Computer-Assisted Intervention, pages 488–498. Springer, 2024.  
[12] Tianrun Chen, Chaotao Ding, Lanyun Zhu, Tao Xu, Deyi Ji, Yan Wang, Ying Zang, and Zejian Li. xlstm-unet can be an effective 2d & 3d medical image segmentation backbone with vision-lstm (vil) better than its mamba counterpart. arXiv preprint arXiv:2407.01530, 2024.  
[13] Alexander Kirillov, Eric Mintun, Nikhila Ravi, Hanzi Mao, Chloe Rolland, Laura Gustafson, Tete Xiao, Spencer Whitehead, Alexander C Berg, Wan-Yen Lo, et al. Segment anything. In Proceedings of the IEEE/CVF International Conference on Computer Vision, pages 4015–4026, 2023.  
[14] Jun Ma, Yuting He, Feifei Li, Lin Han, Chenyu You, and Bo Wang. Segment anything in medical images. Nature Communications, 15(1):654, 2024.  
[15] Fabian Isensee, Jens Petersen, Andre Klein, David Zimmerer, Paul F. Jaeger, Simon Kohl, Jakob Wasserthal, Gregor Koehler, Tobias Norajitra, Sebastian Wirkert, and Klaus H. Maier-Hein. nnu-net: Self-adapting framework for u-net-based medical image segmentation, 2018.

