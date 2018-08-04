# Project Description - BehavioralCloning
This is the code for third project (Behavioral Cloning)

This project makes use of a Udacity-developed driving simulator and training data collected from the simulator. The training data need to be developed by running the car in the simulator in training mode. The training data is generated by  images captured from the different camera  in the simulator car (i.e camera mounted in the the centre, the left and the right of the car).
Three images and the steering angle will be generated per frame and a sample data set is show below

- Centre- center_2018_07_31_22_27_43_693.jpg
- Left- left_2018_07_31_22_27_43_693.jpg
- Right- right_2018_07_31_22_27_43_693.jpg
- Steering angle- -0.5494633

One the **training data** is generated for one lap of driving for the chosen test track, a deep neural network is used to clone car driving behaviour. It is a **supervised regression** problem between the car steering angles and the road images in front of a car.
The network is based on The [NVIDIA model](https://devblogs.nvidia.com/deep-learning-self-driving-cars/), which has been proven to work in this problem domain.
As image processing is involved, the model is using convolutional layers for automated feature engineering.

# Approach
## 1. Model Selection and Data preprocessing

As suggested by Udacity, in this project the [NVIDIA model](https://devblogs.nvidia.com/deep-learning-self-driving-cars/) was used,  
![NVidia model](/images/nVidia_model.png)

The **NVIDIA model** is well documented and hence very useful for the project

The following adjustments were done to training the model with the training data
- To avoid saturation and make gradients work better, one Lambda layer was used to normalized input images 
- An additional dropout layer to avoid overfitting after the convolution layers.
- The ELU activation function was used for every layer except for the output layer to introduce non-linearity.

The NVDIA model does not provide the activation function, hence ELU activiation function was selected. Finally the model selected is as follows
- Image normalization using a Keras Lambda function
- Convolution: 5x5, filter: 24, strides: 2x2, activation: ELU
- Convolution: 5x5, filter: 36, strides: 2x2, activation: ELU
- Convolution: 5x5, filter: 48, strides: 2x2, activation: ELU
- Convolution: 3x3, filter: 64, strides: 1x1, activation: ELU
- Convolution: 3x3, filter: 64, strides: 1x1, activation: ELU
- Drop out (0.5)
- Fully connected: neurons: 100, activation: ELU
- Fully connected: neurons: 50, activation: ELU
- Fully connected: neurons: 10, activation: ELU
- Fully connected: neurons: 1 (output)

# Data Preprocessing
## Image Sizing

•	the images are cropped so that the model won’t be trained with the sky and the car front parts
•	the images are resized to 66x200 (3 YUV channels) as per NVIDIA model
•	the images are normalized (image data divided by 127.5 and subtracted 1.0). As stated in the Model Architecture section, this is to avoid saturation and make gradients work better)

