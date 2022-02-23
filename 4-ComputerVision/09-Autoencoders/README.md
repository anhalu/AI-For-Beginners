# Autoencoders

When training CNNs, one of the problems is that we need a lot of labeled data. In the case of image classification, we need to separate images into different classes, which is a manual effort.

However, we might want to use raw (unlabeled) data for training CNN feature extractors, which is called **self-supervised learning**. Instead of labels, we will use training images as both network input and output. The main idea of **autoencoder** is that we will have an **encoder network** that converts input image into some **latent space** (normally it is just a vector of some smaller size), the then **decoder network**, whose goal would be to reconstruct the original image.

Since we are training autoencoder to capture as much of the information from the original image as possible for accurate reconstruction, the network tries to find the best **embedding** of input images to capture the meaning.л.

![AutoEncoder Diagram](images/autoencoder_schema.jpg)

*Image from [Keras blog](https://blog.keras.io/building-autoencoders-in-keras.html)*

## Scenarios for using Autoencoders

While reconstructing original images does not seem useful in its own right, there are a few scenarios where autoencoders are especially useful:

* **Lowering the dimension of images for visualization** or **training image embeddings**. Usually autoencoders give better results than PCA, because it takes into account spatial nature of images and hierarchical features
* **Denoising**, i.e. removing noise from the image. Because noise carries out a lot of useless information, autoencoder cannot fit it all into relatively small latent space, and thus it captures only important part of the image. When training denoisers, we start with original images, and use images with artificially added noise as input for autoencoder.
* **Super-resolution**, increasing image resolution. We start with high-res images, and use the image with lower resolution as autoencoder input.
* **Generative models**. Once we train autoencoder, the decoder part can be used to create new objects starting from random latent vectors.

## Variational Autoencoders (VAE)

Traditional autoencoders reduce the dimension of the input data somehow, figuring out the important features of input images. However, latent vectors ofter do not make much sense. In other words, taking MNIST dataset as an example, figuring out which digits correspond to different latent vectors is not an easy task, because close latent vectors would not necessarily correspond to the same digits. 

On the other hand, to train *generative* models it is better to have some understanding of the latent space. This idea leads us to **variational autoencoder** (VAE).

VAE is the autoencoder that learns to predict *statistical distribution* of the latent parameters, so-called **latent distribution**. For example, we may want latent vectors to be distributed normally with some mean z<sub>mean</sub> and standard deviation z<sub>sigma</sub> (both mean and standard deviation are vectors of some dimensionality d). Encoder in VAE learns to predict those parameters, and then decoder takes a random vector from this distribution to reconstruct the object.

To summarize:

 * From input vector, we predict `z_mean` and `z_log_sigma` (instead of predicting the standard deviation itself, we predict it's logarithm)
 * We sample a vector `sample` from the distribution N(z<sub>mean</sub>,exp(z<sub>log\_sigma</sub>))
 * Decoder tries to decode the original image using `sample` as an input vector

 <img src="images/vae.png" width="50%">

Variational auto-encoders use complex loss function that consists of two parts:
* **Reconstruction loss** is the loss function that shows how close reconstructed image is to the target (can be MSE). It is the same loss function as in normal autoencoders.
* **KL loss**, which ensures that latent variable distributions stays close to normal distribution. It is based on the notion of [Kullback-Leibler divergence](https://www.countbayesie.com/blog/2017/5/9/kullback-leibler-divergence-explained) - a metric to estimate how similar two statistical distributions are.

One important advantage of VAEs is that they allow us to generate new images relatively easy, because we know which distribution to sample latent vectors from. For example, if we train VAE with 2D latent vector on MNIST, we can then vary components of the latent vector to get different digits:

<img src="images/vaemnist.png" width="50%"/>

Observe how images blend into each other, as we start getting latent vectors from the different portions of the latent parameter space. We can also visualize this space in 2D:

<img src="images/vaemnist-diag.png" width="50%"/> 

## Continue to Notebooks

* [Autoencoders in Tensorflow](AutoencodersTF.ipynb)

## Properties of Autoencoders

* **Data Specific** - they only work well with the type of images they have been trained on. For example, if we train super-resolution network on flowers, it will not work well on portraits. This is because the network can produce higher resolution image by taking fine details from features learnt from the training dataset.
* **Lossy** - reconstructed image is not the same as the original image. The nature of loss is defined by the *loss function* used during training
* Works on **unlabeled data**

## Reference

* [Building Autoencoders in Keras](https://blog.keras.io/building-autoencoders-in-keras.html)
* [Blog post on NeuroHive](https://neurohive.io/ru/osnovy-data-science/variacionnyj-avtojenkoder-vae/)
* [Variational Autoencoders Explained](https://kvfrans.com/variational-autoencoders-explained/)