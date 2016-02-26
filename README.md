# Facial Expression Recognition

## Introduction
For this problem I used the Toronto Faces Dataset, where the task was to classify images of faces based on their expression. Additionally, I was given access to 2925 labeled images for training and validation. The goal was to write a program that would achieve the highest accuracy on an unseen test set. In addition to the 2925 labeled data, I was provided with 98,058 unlabeled images that was used to pretrain the CNN (details below). **Achieved 9th or 98 entries https://inclass.kaggle.com/c/csc411f15-facial-expression-prediction.**

### a. High-Level Introduction
There were essentially three flavours of classifiers that were used as a way to solve the problem of facial expression recognition. (1) The first was required, the baseline, which is essentially a K-NN classifier. In this instance, classification was conducted using 10-fold cross validation with varying K’s, with the facial images at full scale, but normalized. Later classification with K-NN was done with a varying number of principal components derived from the unlabeled set. (2) The second classifier that was used was multi-class support vector machines. It was chosen due to its property of soft-separation of classes and the fact that it is a popular classifier. Similar to (1), training and test images were used at full scale, and classification was done with varying number of principal
components. (3) The final classification method that was used was a convolutional neural network. It was chosen due to its general success in image classification. The CNN was first pre-trained, by first training a denoising auto-encoder on the unlabeled set, and then using its encoder as a model to train for classification using the training and validation sets.

On the validation set the classifier that performed best was the convolutional neural network. This classifier, when fully trained, was used on the public and hidden test sets with good results. In the following, I will discuss this classifier in relation to the other two alternatives.

### b. Classifier Description
As described earlier, the CNN used in the facial expression recognition task is the encoder of a convolutional auto-encoder. Below is detailed the model specifications, the auto-encoder is simply a mirror of this model. As described previously, unlabeled facial expression images were used to pre-train the network, by using the model as an encoder and its reverse as a decoder. Unlabeled images were forward and backward propagated as a way for the network to initialize parameters which would hopefully represent characteristic of facial expression. All training and assembling of the model was done using an Lua, neural network package, Torch.

**CNN Model Specifications**

1. **Input → Convolution Layer 1 → Max Pooling Layer 1**: Input image of size of 32 × 32 pixel is convolved with a filter of 5 × 5 pixels of depth 96 followed by rectified linear units and then a max pooling that scales the results by 0.5.
2. **Max Pooling Layer 1 → Convolution Layer 2**: Input which is now 14 × 14 × 96, is convolved with a filter of size 3, and depth 128 followed by rectified linear units.
3. **Convolution Layer 2 → Convolution Layer 3**: Input which is now 12 × 12 × 128, is convolved with a filter of size 3, and depth 128 followed by rectified linear units.
4. **Convolution Layer 3 → Convolution Layer 4**: Input which is now 10 × 10 × 128, is convolved with a filter of size 3, and depth 128 followed by rectified linear units.
5. **Convolution Layer 4 → Convolution Layer 5 → Max Pooling Layer 2**: Input which is now 8 × 8 × 128, is convolved with a filter of size 3, and depth 128 followed by rectified linear units. This is followed by max pooling that scales the result by 0.5. 
6. **Convolution Layer 5 → Fully Connected Layer 1**: Input propogates through 2048 (4 * 4 * 128) input units fully connected to 1024 output units followed by rectified linear units.
7. **Fully Connected Layer 1 → Fully Connected Layer 2**: Input propogates through 1024 input units fully connected to 7 units.

**Data Preprocessing**

Cross validation was not used in determining the best model parameters due to how time expensive it would be to train a reasonable amount of CNNs. Instead the dataset was split into training and validation sets, the former containing 2598 labeled images and the latter containing 327 labeled images. As required, in the project description, neither set contains the image of the same identity. The training set was further augmented in two ways. The first was to use an external matlab function ’lensdistort’ that can pinch the image in positive and negative directions, and the second was to slightly scale up the training images. Training data was distorted in small positive and negative directions adding 5196 images and increasing the size of the image added another 2598 images, so that the training set was augmented to 10392 datapoints. The training data was augmented in an attempt to give the CNN better intuition for facial expressions and to create a more robust model. Also, it is the nature of neural networks that more data will improve results, although the data is a slightly distorted copy of the original it is possible that the CNN will perform better. Additionally the data was normalized before being by the final CNN.
