# Model Zoo
Collection of more deep learning models, with tutorials on how to run them on paperspace.

## Content
1. [AttnGAN](#attention_gan)
2. [Google Deep Dream](#googledeepdream)
3. [Class-Based Deep Dreaming](#classdeepdream)
4. [2D Neural Style Transfer](#neuralstyle)
5. [2D to 3D Deep Dreaming](#3ddream)
6. [2D to 3D Style Transfer](#3dstyle)
7. [2D to 3D Vertex Optimizatoin](#3dvert)
8. [Pix2PixHD](#pix2pixhd)
9. [CycleGAN](#cyclegan)
8. [PG-GAN](#pggan)

<a name="attention_gan"></a>
## AttnGAN for Image generation from Text 
Pytorch implementation for reproducing AttnGAN results in the paper [AttnGAN: Fine-Grained Text to Image Generation
with Attentional Generative Adversarial Networks](http://openaccess.thecvf.com/content_cvpr_2018/papers/Xu_AttnGAN_Fine-Grained_Text_CVPR_2018_paper.pdf) by Tao Xu, Pengchuan Zhang, Qiuyuan Huang, Han Zhang, Zhe Gan, Xiaolei Huang, Xiaodong He. (This work was performed when Tao was an intern with Microsoft Research). 
Currently, it uses the weights trained on COCO dataset.

### Running AttnGAN
+ Upload and unzip the [coco.zip](https://drive.google.com/file/d/1BEwvpCyLvuTFsFuWlmX1NfWS13v0HcY4/view?usp=sharing) file in the storage folder using Jupyter notebook
+ In the coco folder modify `captions.txt` file for the desired text/caption input
+ Start an experiment with the following parameters in paperspace:
	+ **Container:** `brannondorsey/docker-stackgan`
    + **Workspace:** `https://github.com/dysdsyd/AttnGAN.git`
    + **Command:** `bash run.sh`
+ Generated images will be dumped in the `storage/coco/coco_AttnGAN2/captions` directory for each input sentence in the `captions.txt` file. Each sentence generates 5 image files and the file with `*_g2.png` extension is the final output.


<a name="googledeepdream"></a>
## Google Deep Dream
DeepDream is an experiment that visualizes the patterns learned by a neural network. Similar to when a child watches clouds and tries to interpret random shapes, DeepDream over-interprets and enhances the patterns it sees in an image.
It does so by forwarding an image through the network, then calculating the gradient of the image with respect to the activations of a particular layer. The image is then modified to increase these activations, enhancing the patterns seen by the network, and resulting in a dream-like image. This process was dubbed "Inceptionism". 
This is Google's implementation of deep dream that is used for their web UI. 

This code allows you to augment an input image with the learned features for a specifc neuron or every neuron in the final layer of a classification neural network. Thus, for a single input image, this code will either generate a single output image, or generate 143 output images, each using the learned features from a different neuron. 
When using a pre-trained network, you first need to upload the `inception5_weights.zip` file folder to `/storage` using the notebook tool in the web GUI, and unzip it. What is nice about using this code in leu of the google web UI is that this code takes in an image of any resolution!

2D deep dream Docker container image:

`tensorflow/tensorflow:1.1.0-devel-gpu`

Workspace:

`https://github.com/alexacarlson/DeepDesign2019.git`

Command Format:

`bash run_2Dgoogledeepdream_eval.sh IMAGE_DATA WHICH_NEURON MODEL_DIR RESULTS_DIR NUM_ITERS`

Where `IMAGE_DATA` is the location of your input image on paperspace, `WHICH_NEURON` is a number 0-143 to specify which neuron you would like to use for dreaming. If you would like to use all of them, specify `'all'` instead of a number. `MODEL_DIR` is the location of `inception5_weights` in paperspace, `RESULTS_DIR` is the location in paperspace where the output image(s) will be saved, and `NUM_ITERS` determines how many times the algorithm will perform the dreaming operation (more iterations will have a stronger dreaming effect in the input image).

Command Example:

`bash run_2Dgoogledeepdream_eval.sh /storage/2Dmodels/tree1.jpg /storage/inception5h_weights/tensorflow_inception_graph.pb /storage/test_dream 30`

<a name="classdeepdream"></a>
## Class-based Deep Dream
This code allows you to augment an input image with the learned features of a specific class from a trained classification neural network, specifically a VGG network. 
This technique is used for visualising the class models learnt by the image classification ConvNets. Given a learnt/trained classification ConvNet and a class of interest from the network's training dataset, this visualisation method generates an image with features that are representative of what the ConvNet has learned to represent/detect the given class; it lets us know what are the features expected in input image to maximize the output class node score.

### Evaluating 2D class-based deep dream
When using a pre-trained network, you first need to upload pretrained VGG neural network weights folder to `/storage` using the notebook tool in the web GUI. Note that the next subsection details how to train a VGG network on your own dataset. 
Note that the  input must be in RGB format (i.e., three channels).

2D deep dream Docker container image:

`acarlson32/visclass-tf:firstimage`

Workspace:

`https://github.com/alexacarlson/DeepDesign2019.git`

Command Format:

`bash run_2Dclassbaseddeepdream_eval.sh IMAGE_DATA WEIGHTS_DIR DREAM_CLASS RESULTS_DIR NUM_ITERS IMAGE_H IMAGE_W`

Where `IMAGE_DATA` is the location of your input image on paperspace, `WEIGHTS_DIR` is the location of the VGG network weights in paperspace,`DREAM_CLASS` is the class you would like to use for dreaming. If you would like to use all of them, specify `'all'` instead of a number.  `RESULTS_DIR` is the location in paperspace where the output image(s) will be saved, and `NUM_ITERS` determines how many times the algorithm will perform the dreaming operation (more iterations will have a stronger dreaming effect in the input image), `IMAGE_H` and `IMAGE_W` are the output image height and width, respectively.

Command Example:

`bash run_2Dclassbaseddeepdream_eval.sh /storage/2Dmodels/scene0_camloc_0_5_-20_rgb.png /storage/acadia_general_arch_styles_netweights gothic /storage/test 500 720 1280`

### Training a classifcation network on your dataset to use for 2D class-based deep dream
This code produces a trained VGG network that can be used to perform 2D class-based dreaming (as described in the previous section). Note that the weights directory in the below command is where the weights will be saved. You will need to upload your classification dataset to `/storage` before running this code. It assumes that your dataset contains a folder for each different class of images, and that each of these folders contains images only for that class. 

2D deep dream Docker container image:

`acarlson32/visclass-tf:firstimage`

Workspace:

`https://github.com/alexacarlson/DeepDesign2019.git`

Command Format:

`bash run_2Dclassbaseddeepdream_training.sh TRAIN_IMAGE_DIR TRAIN_EPOCHS WEIGHTS_DIR RESULTS_DIR`

Where `TRAIN_IMAGE_DIR` is the location of your classification training dataset on paperspace, `TRAIN_EPOCHS` determines how long your classification network will train for, `WEIGHTS_DIR` is the location of where the finetuned VGG network weights will be saved in paperspaced, , `IMAGE_H` and `IMAGE_W` are the desired image height and width that your trained VGG network will operate on, respectively. Note that the network can only operate on images that are the same size as it has been trained for.

Command Example:

`bash run_2Dclassbaseddeepdream_training.sh /storage/classificationdataset /storage/acadia_general_arch_styles_netweights gothic /storage/test 500`

<a name="neuralstyle"></a>
## Neural Style Transfer
Neural style transfer is an optimization technique used to take two images—a content image and a style reference image (such as an artwork by a famous painter)—and blend them together so the output image looks like the content image, but “painted” in the style of the style reference image.
This is implemented by optimizing the output image to match the content statistics of the content image and the style statistics of the style reference image. These statistics are extracted from the images using a convolutional network.

### Running 2D style transfer
First, you will need to create notebook via web GUI, upload vgg weights, called `imagenet-vgg-verydeep-19.mat`, to `/storage`
You will also need to upload a content image and style guide image to `/storage`. You can download the vgg weights from <http://www.vlfeat.org/matconvnet/models/imagenet-vgg-verydeep-19.mat>.

2D style transfer Docker container image:

`acarlson32/neuralstyle-tf:firstimage`

Workspace:

`https://github.com/alexacarlson/DeepDesign2019.git`

Command Format:

`bash run_2Dneuralstyletransfer.sh CONTENT_FILE STYLE_FILE OUTPUT_FILE IMAGE_SIZE CONTENT_WEIGHT STYLE_WEIGHT`

Command Example: 

`bash run_2Dneuralstyletransfer.sh /storage/2Dmodels/robotics_building_satellite.png /storage/2Dmodels/new000343.png /artifacts/roboticsbuilding_satellite_style000343_styleweight10.jpg 500 5.0 1.0`

<a name="3ddream"></a>
### Running 2D to 3D neural renderer for 3D deep dreaming
This project uses the neural 3D mesh renderer (CVPR 2018) by H. Kato, Y. Ushiku, and T. Harada to achieve dreaming and style transfer in 3D. It builds upon the code in (https://github.com/hiroharu-kato/neural_renderer.git)

Note that before running any jobs in this project, you will need to upload the desired 3D models (in `.obj` format) to the paperspace `/storage space`. Add each 3D model to `/storage/3Dmodels`. You do not need to worry about uploading pretrained weights, the code handles this under the hood. 

Neural Renderer Docker container image:

`acarlson32/2d3d_neuralrenderer:firstimage`

Workspace:

`https://github.com/alexacarlson/DeepDesign2019.git`

Command Format:

`bash run_2Dto3Ddeepdream.sh INPUT_OBJ_PATH OUTPUT_FILENAME OUTPUT_DIR NUM_ITER`

Command Example: 

`bash run_2Dto3Ddeepdream.sh /storage/3Dmodels/bench.obj 3Ddreamed_bench.gif /artifacts/results_3D_dream 300`

<a name="3dstyle"></a>
### Running 2D to 3D neural renderer for 2D to 3D style transfer
This project uses the neural 3D mesh renderer (CVPR 2018) by H. Kato, Y. Ushiku, and T. Harada to achieve dreaming and style transfer in 3D. It builds upon the code in (https://github.com/hiroharu-kato/neural_renderer.git)

Note that before running any jobs in this project, you will need to upload the desired 3D models (in `.obj` format) to the paperspace `/storage` space. Add each 3D model to `/storage/3Dmodels` and any 2D models (i.e., images) to `/storage/2Dmodels`. You do not need to worry about uploading pretrained weights, the code handles this under the hood. 

Neural Renderer Docker container image:

`acarlson32/2d3d_neuralrenderer:firstimage`

Workspace:

`https://github.com/alexacarlson/DeepDesign2019.git`

Command Format:

`bash run_2Dto3Dstyletransfer.sh INPUT_OBJ_PATH INPUT_2D_PATH OUTPUT_FILENAME OUTPUT_DIR STYLE_WEIGHT CONTENT_WEIGHT NUM_ITERS`

Command Example: 

`bash run_2Dto3Dstyletransfer.sh /storage/3Dmodels/TreeCartoon1_OBJ.obj /storage/2Dmodels/new000524.png 2Dgeo_3Dtree.gif /artifacts/results_2D_to_3D_styletransfer 1.0 2e9 1000`

<a name="3dvert"></a>
### Running 2D to 3D neural renderer for 2D to 3D vertex optimization
This project uses the neural 3D mesh renderer (CVPR 2018) by H. Kato, Y. Ushiku, and T. Harada to achieve dreaming and style transfer in 3D. It builds upon the code in (https://github.com/hiroharu-kato/neural_renderer.git)

Note that before running any jobs in this project, you will need to upload the desired 3D models (in `.obj` format) to the paperspace `/storage` space. Add each 3D model to `/storage/3Dmodels` and any 2D models (i.e., images) to `/storage/2Dmodels`. You do not need to worry about uploading pretrained weights, the code handles this under the hood. 

Neural Renderer Docker container image:

`acarlson32/2d3d_neuralrenderer:firstimage`

Workspace:

`https://github.com/alexacarlson/DeepDesign2019.git`

Command Format:

`bash run_2Dto3Dvertexoptimization.sh INPUT_OBJ_PATH INPUT_2D_PATH OUTPUT_FILENAME OUTPUT_DIR NUM_ITERS`

Command Example: 

`bash run_2Dto3Dvertexoptimization.sh /storage/3Dmodels/TreeCartoon1_OBJ.obj /storage/2Dmodels/new000524.png 2Dgeo_3Dtree /artifacts/results_vertoptim 250`

<a name="pix2pixhd"></a>
## Pix2pixHD for paired image-to-image translation
We will step through how to train and test the super resolution GAN model, `pix2pixHD` in the Paperspace Experiment Builder. 

First, `pix2pixHD` is a generative adversarial neural network that transforms one dataset of high resolution images, which we refer to as data domain A, into the style of a different high resolution dataset, which we refer to as data domain B. Note that the data in domain A must be paired with the data in domain B; this means that the spatial structure of an image in domain A must correpsond to an image in domain D that has the same spatial structure; a good example of this is having domain A be a collection of semantic segmentation maps (each object class is a different color pixel) and domain B is the segmentation maps corresponding RGB image. This means that, effectively, pix2pixHD is painting the RGB color palette/textures of the shapes in domain B onto the appropriate shapes in domain A.
We use the term domain to desrcibe a dataset because a limited amount of visual information is captured in each dataset, and can vary greatly between datasets. Thus, in a sense, each dataset is it's own little world, or domain! The easiest way to understand this is by thinking of a dataset of purely daytime images compared to a dataset of purely night time images. While both datasets may capture similar structures (buildings, roads cars, people etc), the overall appearance/style is drastically different. The `pix2pixHD` was originally developed to transform semantically segmented maps into corresponding images, but it can be trained to transfer any one dataset into the style of a different dataset. 

The pix2pixHD Docker container you can use for both training and testing your model:

`taesungp/pytorch-cyclegan-and-pix2pix`

The workspace you can use for both training and testing your model:

https://github.com/NVIDIA/pix2pixHD.git

### Training pix2pixHD
For training pix2pixHD, you will need to upload your input data domain A to `/storage/train_A`, and your output data domain B to `/storage/train_B`. AS A REMINDER, the pix2pixHD model requires that the images in domain A are paired with images in domain B; this means that the spatial structure for a pair of images should be similar. For example, domain A could be semantic segmentation maps  and domain B would be the corresponding RGB images, and a pair of images would be the semantic segmentation map of a specific scene and the corresponding RGB image. Because of this requirement, the filenames will need to be the same for image pairs. For example, an image pair would be  `/storage/train_A/0001.png` and `/storage/train_B/0001.png`. For more information please visit the pix2pix github repository, which includes instructions for training and testing.

Command Format:

`python train.py --name <RUNNAME> --dataroot /storage/example_dataset --label_nc 0 --no_instance`

### Testing pix2pixHD
For testing pix2pixHD, you will need to upload your input data domain A to `/storage/test_A`.

Command Format:

`python test.py --name <RUNNAME_OF_TRAINED_NETWORK> --dataroot /storage/example_dataset --results_dir /artifacts/pix2pixhd_outputs --resize_or_crop none $@`

<a name="pggan"></a>
## Progressive growing of GANs (PG-GAN)
PG-GAN functions like a standard GAN framework: the generator neural network takes in a latent noise vector and projects it into the pixel space of RGB images that constitute the 'real' dataset you wish the GAN to model. The Discriminator network determines if its input image is real or fake (i.e, rendered by the Generator network). Each network is influenced by the others error, which trains the Generator to produce highly realistic images. After training, the Discrimiantor network is discarded and the Generator is used to produce novel images that would reasonably come from the training dataset, but do not exist in the training dataset.

In the paperspace persistent storage (using the jupyter notebook) you will need to create the folder `/storage/pggan_dataset` and store your training and testing datasets there. The training folder should contain your training images, and your test folder should contain your test images; the naming convention of the images does not matter. They should all be resized and/or cropped to 1024 by 1024 (you can also do 512x512 or attempt 2048x2048; note that the larger images take much longer to run)

Your dataset should be stored in the following hierarchy:
~~~
---------------------------------------------
The training data folder should be formatted like : 
</storage/pggan_dataset>
                |--Your Folder of training images
                        |--image 1
                        |--image 2
                        |--image 3 ...
---------------------------------------------
~~~
The PG-GAN Docker container that you can use for both training and testing the model:


The workspace you can use for both training and testing your model: 

https://github.com/alexacarlson/pggan-pytorch

### Training PG-GAN
The command format used for training a model:


### Testing PG-GAN
The command format used for testing an already-trained model:


<a name="cyclegan"></a>
## CycleGAN for unpaired image-to-image translation:
The cycleGAN Docker container you can use for both training and testing your model:

The workspace you can use for both training and testing your model:

https://github.com/junyanz/pytorch-CycleGAN-and-pix2pix.git

### Training cycleGAN
The command format used for training a model:


### Testing cycleGAN
The command format used for testing an already-trained model:

