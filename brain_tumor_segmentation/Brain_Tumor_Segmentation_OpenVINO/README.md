# Brain Tumor Segmentation (BraTS) with Intel® Distribution of OpenVINO™ toolkit

| Details           |              |
|-----------------------|---------------|
| Target OS:            |  Ubuntu\* 16.04 or 18.04 LTS   |
| Programming Language: |  Python* 3.5 or later |
| Time to Complete:    |  30-40min     |

![brain-tumor-segmentation-OpenVINO](docs/images/brain-tumour-segmentation.png)

## What it does

This reference implementation applies the U-Net architecture to segment brain tumors from raw MRI scans. The application plots the brain tumor matter segmented and calculates the [Dice coefficient](https://en.wikipedia.org/wiki/S%C3%B8rensen%E2%80%93Dice_coefficient) between ground truth and the predicted result.

## Requirements
### Hardware
* 6th to 8th Generation Intel® Core™ processor

### Software
* [Ubuntu\* 16.04 LTS](http://releases.ubuntu.com/16.04/)<br>
* [Ubuntu\* 18.04 LTS](http://releases.ubuntu.com/18.04/)<br>
  
* Intel® Distribution of OpenVINO™ toolkit 2019 R3 or newer
* Matplotlib

## How It works

The application uses MRI scans as the input data source. The results from the model are used to calculate Dice coefficient and to plot prediction results of the brain tumor matter segmented.

![Architecture-Diagram](docs/images/arch_diag.png)

The Dice coefficient (the standard metric for the BraTS dataset used in the application) for our model is about 0.82-0.88. Menze et al. [reported]( https://ieeexplore.ieee.org/document/6975210 ) that expert neuroradiologists manually segmented these tumors with a cross-rater Dice score of 0.75-0.85, meaning that the model’s predictions are on par with what expert physicians have made. The below MRI brain scans highlight brain tumor matter segmented using deep learning.

![brain-tumor-segmentation](docs/images/figure1.png)

### What is U-Net?  
The [U-Net]( https://en.wikipedia.org/wiki/U-Net ) architecture is used to create deep learning models for segmenting [nerves]( https://github.com/jocicmarko/ultrasound-nerve-segmentation ) in ultrasound  images, [lungs]( https://www.kaggle.com/c/data-science-bowl-2017#tutorial ) in CT scans, and even [interference]( https://github.com/jakeret/tf_unet ) in radio telescopes.

U-Net is designed like an [auto-encoder]( https://en.wikipedia.org/wiki/Autoencoder ). It has an encoding path (“contracting”) paired with a decoding path (“expanding”) which gives it the “U” shape. However, in contrast to the autoencoder, U-Net predicts a pixelwise segmentation map of the input image rather than classifying the input image as a whole. For each pixel in the original image, it asks the question: “To which class does this pixel belong?” This flexibility allows U-Net to predict different parts of the tumor simultaneously.

![U-Net](docs/images/unet.png)

## Setup

### Move to the tutorial directory
```sh
mkdir ~/brainseg
cd ~/brainseg
```

### Get the code

If the MKL-DNN sample was NOT completed, clone the reference implementation:
```sh
sudo apt-get update && sudo apt-get install git
git clone https://github.com/david-drew/OpenVINO-Samples.git
``` 

### Install the Intel® Distribution of OpenVINO™ toolkit
Refer to [Install Intel® Distribution of OpenVINO™ toolkit for Linux*](https://software.intel.com/en-us/articles/OpenVINO-Install-Linux) on how to install and setup the Intel® Distribution of OpenVINO™ toolkit.

You will need the OpenCL™ Runtime Package if you plan to run inference on the GPU. It is not mandatory for CPU inference.

### Other dependencies

#### Matplotlib
Matplotlib is a plotting library for the Python programming language and its numerical mathematics extension NumPy. It provides an object-oriented API for embedding plots into applications.

### Which model to use
This application uses a pre-trained model (unet_model_for_decathlon.hdf5), that is provided in the `/resources` directory. This model is trained using the __Task01_BrainTumour.tar__ dataset from the [Medical Segmentation Decathlon](http://medicaldecathlon.com/), made available under the [(CC BY-SA 4.0)](https://creativecommons.org/licenses/by-sa/4.0/) license. Instructions on how to train your model can be found here [https://github.com/IntelAI/unet/tree/master/2D](https://github.com/IntelAI/unet/tree/master/2D)

To install the dependencies of the RI and to optimize the pre-trained model, run the following command:

```sh
cd ~/brainseg/OpenVINO-Samples/brain_tumor_segmentation/Brain_Tumor_Segmentation_OpenVINO/
chmod +x setup.sh
./setup.sh
```

### What Input to use

The application uses MRI scans from __Task01_BrainTumour.h5__, that is provided in the `/resources` directory.

## Setup the environment
You must configure the environment to use the Intel® Distribution of OpenVINO™ toolkit one time per session by running the following command:

    source /opt/intel/openvino/bin/setupvars.sh
    
__Note__: This command needs to be executed only once in the terminal where the application will be executed. If the terminal is closed, the command needs to be executed again.
    
## Run the Application

```sh
cd application
chmod +x brain_tumor_segmentation.py
```

To see a list of the various options:

```sh
./brain_tumor_segmentation.py -h
```

A user can specify what target device to run on by using the device command-line argument `-d` followed by one of the values `CPU`, `GPU`, `HDDL`, `MYRIAD` or `HETERO:FPGA,CPU`.<br>

### Running on the CPU
Although the application runs on the CPU by default, this can also be explicitly specified through the `-d CPU` command-line argument:
```
./brain_tumor_segmentation.py -r ../results/ -m ../resources/output/IR_models/FP32/saved_model.xml -d CPU --data_file ../resources/Task01_BrainTumour.h5
```

### Check Results
Several images have been created in the results directory.  Look at them to see the output of the application.

    cd ../results
    eog pred0.png
    eog pred1.png
    
Examine other images as desired.   


