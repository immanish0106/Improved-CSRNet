# CSRNet-keras
Implementation of the CSRNet paper (CVPR 18) in keras-tensorflow. First ever to be done in keras-tf as of 21/9/18.
Mindspark Hackathon Winning Project(Kumbh-Mela Disaster Management):
Team : Dr. Rama Krishna Gorthi, Karanjeet singh (ML/DL)
In order to aid the ML/DL community, we have open sourced the deep learning part of the project.
### CVPR 2018 Paper : https://arxiv.org/abs/1802.10062
### Official Pytorch Implementation : https://github.com/leeyeehoo/CSRNet-pytorch
As we were searching over the internet, we could not find any keras implementation of the state of the art paper CSRNet. A large part of the deep learning community uses keras-tensorflow to implement their neural network models. Thus, we implemented the CSRNet model in keras-tensorflow. Tensorflow has a massive advantage when it comes to deployability(eg. Android,etc).

## Dataset :
The dataset used is ShanghaiTech dataset available here : [Drive Link](https://drive.google.com/file/d/16dhJn7k4FWVwByRsQAEpl9lwjuV03jVI/view)
## Weights can be downloaded from Drive Link : https://drive.google.com/drive/u/0/folders/1YcM01mrJR3aHKDDNdMvwaHFHpfg2h2zi

The dataset is divided into two parts, A and B. Part A consists of images with a high density of crowd. Part B consists of images with sparse crowd scenes.   

## Data Preprocessing  :
In data preprocessing, the main objective was to convert the ground truth provided by the ShanghaiTech dataset into density maps. For a given image the dataset provided a sparse matrix consisting of the head annotations in that image. This sparse matrix was converted into a 2D density map by passing through a Gaussian Filter. The sum of all the cells in the density map results in the actual count of people in that particular image. Refer the `Preprocess.ipynb` notebook for the same.
## Data preprocessing math explained:
Given a set of head annotations our task is to convert it to a density map.
1) Build a kdtree(a kdtree is a data structure that allows fast computation of K Nearest neighbours) of the head annotations.
2) Find the average distances for each head with K(in this case 4) nearest heads in the head annotations. Multpiply this value by a    factor, 0.3 as suggested by the author of the paper.
3) Put this value as sigma and convolve using the 2D Gaussian filter. 
## Model :
The CSRNet model uses Convolutional Neural Networks to map the input image to it's respective density map. The model does not make use of any fully connected layers and thus the size of the input image is variable. As a result, the model learns from a large amount of varied data and there is no information loss considering the image resolution. There is no need of reshaping/resizing the image while inferencing. The model architecture is such that considering the input image to be (x,y,3), the output is a density map of size (x/8,y/8,1).
The model architecture is divided into two parts, front-end and back-end. The front-end consists of 13 pretrained layers of the VGG16 model ( 10 Convolution layers and 3 MaxPooling layers ). The fully connected layers of the VGG16 are not taken. The back-end comprises of Dilated Convolution layers. The dilation rate at which maximum accuracy was obtained was experimentally found out be `2` as suggested in the CSRNet paper.
Batch Normalisation functionality is also provided in the code. As VGG16 does not have any BN layers, we built a custom VGG16 model and ported pretrained weights of VGG16 to this model.
#### Vairable Size Input
In keras it is difficult to train a model where the size of the input image is variable. Keras does not allow variable size inputs to be trained in the same batch. One way to tackle this is to combine all images having the same image dimension and train them as a batch. The ShanghaiTech dataset does not contain many images having the same image size and thus such batches could not be made. Another approach is to train each image independantly and run a loop over all images. This approach is not efficient in terms of memory usage and computation time. Thus, we built a custom data generator in keras to efficiently train variable sized images. With a data generator, efficient memory usage takes place and the time taken for training reduces drastically.
@karanjsingh
 
