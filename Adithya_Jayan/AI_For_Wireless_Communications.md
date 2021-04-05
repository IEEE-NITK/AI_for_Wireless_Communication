---
Title: "AI for Wireless Communication"
Description: "Using Self-Supervised learning to find User location based on the channel data"
Categories: Diode
---

### Mentors: 
- Adithya Jayan
- Shashank Holla S

### Members: 
- Chandan Kumar
- P. Ramyashri 

## Introduction

Our project User localization takes channel responses between the antenna and the user and SNR data as input and returns the user location as output. We have used self supervised learning to achieve the same. Self-supervised learning enables learning representations of data by just observations of how different parts of the data interact. It thereby drops the requirement of huge amounts of annotated data.

## Dataset

The dataset that we are using is borrowed from the IEEE MLC Open Dataset Initiative. The dataset was acquired by the massive MIMO channel sounder. Specifically, channel responses were measured outdoors between a moving transmitter and an 8x8 antenna array (horizontally polarized patch antennas). As the transmitter, an SDR-equipped cart, was used which was moved around in a residential area of several hundred square meters. The SDR transmitted uplink OFDM pilots with a bandwidth of 20 MHz and 1024 subcarriers at a carrier frequency of 1.27 GHz. Ten percent of the subcarriers were used as guard bands, leaving 924 usable subcarriers. 8 of the 64 antennas were perpetually malfunctioning, and hence, only 56 antennas provided useful measurements. Ground truth was acquired with the help of a differential GPS. For every location, 5 channel measurements were recorded. The GPS data was transformed to a local Cartesian coordinate system with the receiver placed at the origin on the XY–plane. The third coordinate represents the height.

MIMO - Multiple Input Multiple Output 

OFDM - Orthogonal Frequency Division Multiplexing

 SNR - Signal to Noise ratio

We have 2 types of data i.e., Channel state information data and SNR data. Since we see data in the frequency domain using Fourier Transform, we have real and imaginary parts of the channel matrices. Since MIMO technology is used, we have 56 antennas. As Multiplexing is used (OFDM), we have 924 sub-carriers. Hence the dimension of our data per location will be 56*924*5. 
Our total dataset consists of 36192 unlabeled samples and close to 4500 labeled samples 

## Our Approach

### a]Preprocessing

CSIs are complex numbers. Therefore features such as amplitude and phase can be utilized. However, the CSI phase is prone to noise and random fading. So ,the amplitude feature was created from the real and imaginary parts of the CSI input. Although CSI provides rich information from multiple subcarriers, it is sensitive to the change of environments, especially the interference of human blocking and movements. So preprocessing was required to get consistent and reliable data.1 D Median filters were adopted to remove the outliers. Normalization of data was also carried out using min-max normalization method so our network can be unbiased to higher value features.

### b]Proposed model architecture

Self Supervised learning Supervised learning usually requires a lot of labeled data. Getting good labeled data is an expensive and time-consuming task, especially for a complex task such as collecting CSI data with appropriate locations. On the other hand, unlabeled data is readily available in abundance. The motivation behind Self-Supervised learning is to learn useful representations of data from an unlabeled pool of data first and then tune the representations with few labels for the supervised downstream task. The fundamental idea for self-supervised learning is to create some auxiliary pre-text task for the model from the input data itself such that while solving the auxiliary task, the model learns the underlying structure of the data(for instance the structure of the object in case of image data).

#### 1) Pretext -1 Model to train CSI data to get SNR values using unlabeled data

In our first pretext task, we trained CSI images obtained after preprocessing to get SNR values. Here we used two Conv layers with tanh activation function. Then we used a couple of dense layers and finally reshaped the neurons to get final output as SNR values. Model summary of the 1st pretext task is shown in the figure: Layers in “sequential” will be used in our downstream model. 

#### ![img](https://lh5.googleusercontent.com/NG8vv4xa-U3yXlaLiaz9zyoU6RWclzMS35hP6I0saP4iUvDv8_PRCOfQQHdt6NUr-1S-nDHFoI1rRm5tRCMrTJRKatCJ5JCqnVGWIxYe4Tizjv6RjXqJQ8mLv6dgfqLY7XuqB2Wh)

#### 2) Pretext -2 Model to train SNR data to get CSI values using unlabeled data

In the second pretext task, we trained SNR values to get CSI images. Here we’ve created a simple dense neural network with 2 dense layers in it. The model summary of second pretext task is shown below:

We’ll be using layers of “sequential_2” for our downstream model.![img](https://lh5.googleusercontent.com/uTsfTfRR3v52b_BuPSkzDj1EHYAE9Ba1GavlBnvX_Xc4BWCMmlWqeG5FDT7KolgV7PfiDjU9Euzlk3dbRZ_s5vxvyPL2avx5cw8u8_R1gPnNmYeTTJpjxxmxedIDufsefDjRGbWG)

#### 3) Downstream task

Now we’ll combine both the models from our pretext task, add a few more dense layers and train it to get the position. Model summary of downstream task is shown below :

![img](https://lh5.googleusercontent.com/W-Dzn2k6hCYvtbK5sEcJ2ujoyzx2OIkvvKBX5krQSecCPN-XfBCf-DfWsv0OZCYTfmT_TYmNzZIL7dCPm3iaXhkp4f48_sCNfOq22O27jQjAI6GywBh_9jy9Qn9PiG8RDcsQ7f74)



## Application and Conclusion

User localization is an increasingly important feature of wireless communication systems enabling a wide range of applications, such as navigation, smart factories and cities, surveillance, security, and IoT. Moreover, accurate position information can be used for improved radio resource management, beamforming as well as channel estimation. Although machine learning-based methods can achieve state-of-the art localization accuracy, their biggest drawback is the need for large quantities of labeled data, i.e., channel state information (CSI) and the corresponding coordinates. Moreover, whenever significant changes in the radio environment occur, new data must be acquired which renders supervised learning approaches impractical for most scenarios. Hence our approach based on self supervised learning which tremendously reduces the need of labelled data, has the potential to implement user localization in practical scenarios.
