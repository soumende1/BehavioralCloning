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


#This was used for setting the environment

# Use TensorFlow without GPU
conda env create -f environment.yml 

# Use TensorFlow with GPU
conda env create -f environment-gpu.yml

## To train the model
The following python scrip needed to be run in the working folder

python model.py

This will generate a file model-000.h5 whenever the performance in the epoch is better than the previous best. For example, the first epoch will generate a file called model-001.h5.


## To Test the model
Once the training is comple , we need to check the performance of the model by first 
Start up the Udacity self-driving simulator, choose a scene and press the Autonomous Mode button. 

Then, run the model as follows:
python drive.py model.h5


## To record the images from frames when the car is running in autonomous mode

python drive.py model.h5 run1

## To create a video of from the above images

The image file name from above is a timestamp of when the image was seen.. This information is used by video.py to create a chronological video of the agent driving.

python video.py run1


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

* the images are cropped so that the model won’t be trained with the sky and the car front parts
* the images are resized to 66x200 (3 YUV channels) as per NVIDIA model
* the images are normalized (image data divided by 127.5 and subtracted 1.0). As stated in the Model Architecture section, this is to avoid saturation and make gradients work better)

# Model Training
## Image Augumentation
The following augumentation technique along with Python generator to generate unlimited number of images:

- Randomly choose right, left or center images.
- For left image, steering angle is adjusted by +0.2
- For right image, steering angle is adjusted by -0.2
- Randomly flip image left/right
- Randomly translate image horizontally with steering angle adjustment (0.002 per pixel shift)
- Randomly translate image virtically
- Randomly added shadows
- Randomly altering image brightness (lighter or darker)
Using the left/right images is useful to train the recovery driving scenario. The horizontal translation is useful for difficult curve handling (i.e. the one after the bridge).

# Training, Validation and Test

I splitted the images into train and validation set in order to measure the performance at every epoch. Testing was done using the simulator.

As for training,

I used mean squared error for the loss function to measure how close the model predicts to the given steering angle for each image.
I used Adam optimizer for optimization with learning rate of 1.0e-4 which is smaller than the default of 1.0e-3. The default value was too big and made the validation loss stop improving too soon.
I used ModelCheckpoint from Keras to save the model only if the validation loss is improved which is checked for every epoch.

# Final Model

Once the model to performed optimally one of the track, the model was frozen. 
Here is the output

    <div  markdown="1">
        <video width="99%" height="540" autoplay loop muted markdown="1">
        <source src="/Videos/run1.mp4" type="video/mp4" markdown="1" >
        </video>
     </div>  

| Autonomous  Driving  | 
|:--------------------:| 
|![Controlled](./Videos/run1.gif) | 

- [The Lake Track - YouTube Link](https://youtu.be/7QdL3Az55jU)
- [The Mountain Track - YouTube Link](https://youtu.be/fqaibk81eM4)

