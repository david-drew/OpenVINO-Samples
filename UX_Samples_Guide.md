
# Get Started with OpenVINO™ Deep Learning Workbench and Samples for Linux*

## <a name="openvino-components"></a>OpenVINO Components
The OpenVINO toolkit optimizes and runs Deep Learning Neural Network models on Intel hardware.
It consists of three primary components:
1.	**Model Optimizer:** Optimizes models for Intel architecture, as well as converts models into a format compatible with the Inference Engine. This format is called an Intemediate Representation (IR).
2.	**Intermediate Representation (IR):** The output of the Model Optimizer. A model converted to a format that has been optimized for Intel architecture and is usable by the Inference Engine.
3.	**Inference Engine:** The software libraries that run inference against the Intermediate Representation (Optimized Model) and produce inference results
4. **DL (Deep Learning) Workbench:** The DL Worbench is a new utility that provides a simple GUI to run the Model Optimizer, and a set of tools to perform tests, analyze and tune model parameters, and compare the results of different settings.

## <a name="workflow-overview"></a>OpenVINO Workflow Overview

The OpenVINO workflow is as follows:
1.	Run an existing trained model through the Model Optimizer (or DL Workbench). 
2.	The Model Optimizer produces Interemediate Representation files (IR).<br>
    Note: The DL Workbench may be used instead of the Model Optmizer.
3.	An application (an Intel sample or one you have built) uses the Inference Engine to run inference using the Intermediate Representation (Optimized Model).
4.	The application outputs inference results. 

## <a name="openvino-installation"></a>OpenVINO Installation
This guide assumes that you have completed the installation of OpenVINO. <br>
The installation created 2 new directories in your home directory:

* `~/omz_demos_build`
* `~/inference_engine_demos_build`

We'll use omz_demos_build for vehicle detection, and inference_engine_samples_build for the first 2 (classification with squeezenet) exercises.
    
This is where sample applications are installed, after they are compiled.  You will have to compile most of the samples.

<br>

<details>
   <summary>Installation Directory Structure</summary>
    
## <a name="dldt-installation-directory-structure"></a> Installation Directory Structure

By default, the installation directory is`/opt/intel/openvino`, though the location is  the configurable by the user.


| Directory&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Description                                                                           |  
|:----------------------------------------|:--------------------------------------------------------------------------------------|
| `demo/`                                 | Demo scripts. Used to verify the installation and compile two sample applications included into the installation. See the *Run the Verification Scripts to Verify Installation and Compile Samples* section in the [installation steps](./docs/install_guides/installing-openvino-linux.md).|
| `inference_engine/`                     | Inference Engine directory. Contains Inference Engine API binaries and source files, samples and extensions source files, and additional resources such as hardware drivers.|
| `~intel_models/` | Symbolic link to the `intel_models` subfolder of the `open_model-zoo` folder |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`include/`      | Inference Engine header files. For API documentation, see the [Inference Engine API Reference](./annotated.html). |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`lib/`          | Inference Engine binaries.|
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`samples/`      | Inference Engine samples. Contains source code for C++ and Python* samples and build scripts. See the [Inference Engine Samples Overview](./docs/IE_DG/Samples_Overview.md). |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`src/`          | Source files for CPU extensions.|
| `model_optimizer/`                      | Model Optimizer directory. Contains configuration scripts, scripts to run the Model Optimizer and other files. See the [Model Optimizer Developer Guide](./docs/MO_DG/Deep_Learning_Model_Optimizer_DevGuide.md).
| `open_model_zoo/`                       | Open Model Zoo directory. Includes the Model Downloader tool to download [pre-trained OpenVINO's models](./docs/Pre_Trained_Models.md) and public ones, OpenVINO's models documentation, demo applications and the Accuracy Checker tool to evaluate model accuracy.|
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`demos/`        | Demo applications for various inference scenarios. Documentation and build scripts are also provided.| 
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`intel_models/` | OpenVINO pre-trained models and models documentation. See the [Overview of OpenVINO™ Toolkit Pre-Trained Models](./docs/Pre_Trained_Models.md).|
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`tools/`        | Contains the Model Downloader and Accuracy Checker tools. |
| `tools/`                                | Contains a symbolic link to the Model Downloader folder and auxiliary tools to work with your models: Calibration tool, Benchmark and Collect Statistics tools.|

</details>

## <a name="using-workbench"></a> Using OpenVINO: Deep Learning Workbench 
The DL Workbench is a tool to help assess model performance in OpenVINO, in terms of both speed and accuracy.  The workbench will automatically run the Model Optimizer on supported Caffe, ONNX, MXNet, and TensorFlow models, and then provide performance estimates based on tuning model parameters and selected target hardware.


## <a name="using-sample-application"></a> Using OpenVINO: Sample Applications

Sample applications are provided to get you working in your OpenVINO environment. This section will guide you through a simplified OpenVINO workflow using those sample applications. You will convert a model using the Model Optimizer then use a sample application to run inference and see the results.

Each of the included samples is a separate application, but there is a common behavior and common components needed to use each of them.

Inputs you'll need to specify:
- **A sample application** from Intel that runs inferencing against a model that has been optimized by the Model Optimizer into an Intermediate Representation (IR), using the other inputs you provide.
- **A model** must be provided for the application. Each model is trained for a specific task (pedestrian detection, face detection, vehicle detection, license plate recognition, head pose, etc.).  Different models will be used for different applications. Models can be chained together to provide multiple features (vehicle + make/model + license plate recognition).
- **A media file** to run through the application. For these samples, the media is typically a video file, though some applications require a image files. 
- **The processor type** to run on, which can be the CPU, GPU, FPGA, or VPU accelerator (e.g., Movidius board). 

<br>

<details>
   <summary>Guidelines for Using Sample Applications</summary>
    
## <a name="basic-guidelines-sample-application"></a> Basic Guidelines for Using Sample Applications

Following are some basic guidelines for executing the OpenVINO workflow and using the sample applications:
1.	Before using OpenVINO, always setup the environment: 

    `source /opt/intel/openvino/bin/setupvars.sh` 
    
2.	Have the directory paths for the following:
- Sample application:
These are binaries located in:
`~/omz_demos_build/intel64/Release` or `~/inference_engine_samples_build/intel64/Release`
- Media: video or image.
- Model: Neural Network model converted to a Model Optimizer compatible format, an XML file. See **Models** below for more information.

3.	Choose the correct precision for a model:
- FP32: Used when running the model on the CPU.
- Int8: Experimental, but runs on the CPU.
- FP16: Use when running on any hardware other than the CPU (except for some special cases, e.g., GPU can run FP32 but more slowly).
</details>

<details>
   <summary>Models</summary>
    
## <a name="models"></a> Models

Models in an Intel compatible format (compatible with the Model Optimizer) always include an `.xml` and `.bin` file, and may also require other files, like `.json`, `.mapping`, etc.  Verify that you have all of the needed files.  They should be in the same directory for the Inference Engine to find them.
- `model_name.xml`
- `model_name.bin`
- OPTIONAL: `model_name.json`, `model_name.mapping`, etc.

Models May Be:
- Classification (AlexNet, GoogleNet, SqueezeNet, etc.)
    - These detect one type of element in a frame.
- Object Detection (SSD, yolo)
    - These draw bounding boxes around multiple types of objects.
- Custom (Often based on SSD)

Descriptions of Intel’s pre-trained models (links to download are not included): 

https://software.intel.com/en-us/openvino-toolkit/documentation/pretrained-models

## <a name="downloading-models"></a> Downloading Models
There are several ways to find models suitable for use with OpenVINO:
- Download from Intel’s Open Model Zoo: 
    - https://github.com/opencv/open_model_zoo

- Download from GitHub, Caffe Zoo, TensorFlow Zoo, etc.
- Train your own model
- Use the "Import Model" option in the DL Workbench.
- Use Intel’s Model Downloader tool:
    - https://software.intel.com/en-us/articles/model-downloader-essentials)
    
    1.	To list the FP32 models available in the downloader enter the following command: 
    
        `cd /opt/intel/openvino/deployment_tools/tools/model_downloader/`
        
        `grep name list_topologies.yml |grep -vi fp16|grep -vi int8|grep xml|sort`

    2.	Optional add (at the end): 

        `| grep <model_name>`

    3.	Always run the downloader with `sudo`.

</details>

<details>
   <summary>Downloading Media</summary>

## <a name="downloading-media"></a> Downloading Media

There are multiple sources for downloading video media for use with samples. Possibilities include: 
- https://videos.pexels.com
- https://images.google.com

</details>


## <a name="syntax-examples"></a> Typical Sample Application Syntax Examples

Compiled sample applications for this tutorial are found in: `~/omz_demos_build/intel64/Release` or `~/inference_engine_samples_build/intel64/Release`.

Here is a template for calling a sample app:

`path_to_app -i path_to_media -m path_to_model -d processor_type`

With sample information filled in, the command could look like this:

`./object_detection_demo_ssd_async -i ~/Videos/catshow.mp4 \
-m ~/ir/fp32/mobilenet-ssd.xml -d CPU`

<br>

<details>
   <summary>Advanced Use of Samples</summary>
    
## <a name="advanced-samples"></a> Advanced Use of Samples

NOTE: The commands in this section are examples and not meant to be run.  These could be executed as displayed if the required setup (downloading models, etc.) was performed, but the details are left to the reader.

Some applications allow the use of multiple models for different purposes. Usually for these cases, the output of the first model is used as the input of later models.

For example, an SSD will detect a variety of objects in a frame, then age, gender, head pose, emotion recognition and similar models target the objects classified by the SSD to perform their functions.

In these cases, the use pattern in the last part of the template above is usually:

`-m_<acronym> … -d_<acronym> …`

For head pose:

`-m_hp <headpose model> -d_hp <headpose hardware target>`

**Example of an Entire Command (object_detection + head pose):**

`./object_detection_demo_ssd_async -i ~/Videos/catshow.mp4 \
-m ~/ir/fp32/mobilenet-ssd.xml -d CPU -m_hp headpose.xml \
-d_hp CPU` 

**Example of an Entire Command (object_detection + head pose + age-gender):**

`./object_detection_demo_ssd_async -i ~/Videos/catshow.mp4 \
-m ~/r/fp32/mobilenet-ssd.xml -d CPU -m_hp headpose.xml \
-d_hp CPU -m_ag age-gender.xml -d_ag CPU`

You can see all the sample application’s parameters by adding the `-h` or `--help` option at the command line.

</details>


## <a name="Exercises"></a> Exercises

The following series of exercises guide you through using samples of increasing complexity. As you move through each exercise you will get a sense of how to use OpenVINO in more sophisticated use cases. 

**NOTE:** Before starting these sample exercises, change directories into the samples directory:

`cd ~/omz_demos_build/intel64/Release`

**NOTE:** During this exercise you will be asked to move to multiple different directories, and occasionally to copy files (so that you don't have to specify full paths in commands).  You are welcome to set up environment variables to make these tasks easier, but we leave that to you.


<details>
   <summary>Exercise 1 - Run A Sample Application</summary>
    
## <a name="run-sample-application"></a> Run A Sample Application 

Convert a model using the Model Optmizer then use a sample application to load the model and run inference.

**NOTE:** If you are running inference only on a CPU, you already have the required FP32 model. If you want to run inference on any hardware other than the CPU, you'll need an FP16 version of the model.

**Set Up a Neural Network Model**

In this section, you will convert an FP32 model suitable for running on a CPU.

Note: Remember to setup the environment variables when logging in, changing users, or launching a new terminal.
    `source /opt/intel/openvino/bin/setupvars.sh` 

1.	Make a directory for the FP32 SqueezeNet Model:

    `mkdir ~/squeezenet1.1_FP32`

2.	Go to ~/squeezenet1.1_FP32:

    `cd ~/squeezenet1.1_FP32`

3.	Use the Model Optimizer to convert an FP32 SqueezeNet Caffe model into an optimized Intermediate Representation (IR):

    `python3 /opt/intel/openvino/deployment_tools/model_optimizer/mo.py --input_model ~/Desktop/Data/models/Caffe/squeezenet1.1/squeezenet1.1.caffemodel --data_type FP32 --output_dir .`    

4.	The `squeezenet1.1.labels` file contains the classes that ImageNet uses. This file is included so that the inference results show text instead of classification numbers. Copy `squeezenet1.1.labels` to your optimized model location:

    `cp ~/openvino_models/ir/public/squeezenet1.1/FP16/squeezenet1.1.labels .`

5.	Copy a sample image to the release directory. You will use this with your optimized model:

    `sudo cp /opt/intel/openvino/deployment_tools/demo/car.png  .`

6. Once your setup is complete, you're ready to run a sample application:

    `cd  ~/inference_engine_samples_build/intel64/Release`

    `./classification_sample_async -i car.png -m ~/squeezenet1.1_FP32/squeezenet1.1.xml -d CPU`

7. Note: you can usually see an applications help information (parameters, etc.) by using `-h`.

    `./classification_sample -h`

</details>


<details>
   <summary>Exercise 2 - Using the DL Workbench</summary>
    
## <a name="run-sample-application"></a> Run A Sample Application with the DL Workbench

Convert a model using the DL Workbench, then use a sample application to load the model and run inference.

**NOTE:** If you are running inference only on a CPU, you already have the required FP32 model. If you want to run inference on any hardware other than the CPU, you'll need an FP16 version of the model.

**Set Up a Neural Network Model**

In this section, you will convert an FP32 model suitable for running on a CPU.

1.	Return to the previously indicated FP32 SqueezeNet directory:

    `cd ~/squeezenet1.1_FP32`

2.	Use the DL Workbench to convert the FP32 SqueezeNet Caffe model into an FP16 optimized Intermediate Representation (IR):
/home/vino/Desktop/Data/models/Caffe/squeezenet1.1/squeezenet1.1.caffemodel
    1. In a web browser, launch the DL Workbench:
    `http://127.0.0.1:5665/`
    2. Select "Get Started" to show the setup interface.
    3. The setup interface is divided into two sections.
        - The top section is for loading models.
        - The bottom section is for loading data sets (for example, images that will be used by the model during inference).
     4. Select the "Original Model" tab - the default tab is for downloading models from the OpenVINO Model Zoo.
     5. Select Framework -> "Caffe"
     6. Choose the prototxt file with the first "Choose File" button.
        - ~/Desktop/Data/models/Caffe/squeezenet1.1/squeezenet1.1.protoxt
     7. Choose the caffemodel file with the second "Choose File" button.
        - ~/Desktop/Data/models/Caffe/squeezenet1.1/squeezenet1.1.caffemodel
     8. The Model Name will be automatically populated, but change it if desired.  
        - This can be useful when tracking multiple loaded models
     9. Select "Import Model" when ready.  
        - This will create and FP16 model by default, though configuration settings may be edited later.
    10. In some cases this will work and you will be returned to the setup interface.  If the DL Workbench is unable to determine required information, it will display a configuration screen and specify information that is required.
        - If you built the model, you will know all of the details of the model, but you may have to do some searching if the model was downloaded from a third party.
     11. The "Import Model" screen is being displayed, because some additional information must be provided by the user.
     12. The requirements are specified in the grey box on the right.
     13. In this, the only missing information is the colorspace.  Click on the box next to "Original Color Space" and select "BGR".
         - BGR is often used (as opposed to RGB and other color formats) when training models.
     14. Note that the grey box no longer contains any red warning text.
     15. Select "Convert" and you will be returned to the setup interface.  Model conversion may take several minutes.

3. Import a Dataset into the DL Workbench
    1. We could autogenerate a set of simuluated imaged, but for this task we'll select a subset of the ImageNet dataset.
        - Note: results will often be better using real images.
    2. Select "Import Local Dataset"
    3. Select "Choose File" and browse to "~/Desktop/Workbench/Data/Imagenet_200_224x224.zip"
    4. Select "Import Dataset"
        - Note: The height and width of images in the dataset must satisfy the requirements of the target model.
        
4. Run Inference with the DL Workbench
    1. Select the model from the top part of the interface.
    2. Select the dataset from the bottom part of the interface.
    3. Select the Environment (target hardware).
    4. All items in the status box next to the Environment box should now have green checks.
    5. Select 'Go'
        - This will take a few minutes

5. Normally, we would encourage analysis and optimization of the model, but for this exercise, we'll just export the model from the Workbench to our system.  
    1. Take a few minutes to look at the output if desired.
    2. When ready, find the download icon at the right, in the top squeezenet model bar.
    3. Select Download.  This will drop a tar.gz archive in your default downloads directly (~/Downloads).
    4. `cd ~/Downloads`
    5. `mkdir squeezenet`
    6.  `mv squeeze*gz squeezenet`
    7. `cd squeezenet`
    8. `tar zxvf squeezenet1.1.tar.gz`

6.	The `squeezenet1.1.labels` file contains the classes that ImageNet uses. This file is included so that the inference results show text as well as classification percentages. Copy `squeezenet1.1.labels` to your optimized model location:

    `cp ~/openvino_models/ir/FP32/classification/squeezenet/1.1/caffe/squeezenet1.1.labels .`

7.	Copy a sample image to the release directory. You will use this with your optimized model:

    `cd  ~/inference_engine_samples_build/intel64/Release`

    `sudo cp /opt/intel/openvino/deployment_tools/demo/car.png  .`

8. Once your setup is complete, you're ready to run a sample application:

    `./classification_sample_async -i car.png -m ~/Downloads/squeezenet1.1/squeezenet1.1.xml -d CPU`

9. Note: you can usually see an applications help information (parameters, etc.) by using `-h`.

    `./classification_sample_async -h`

</details>

<details>
   <summary>Exercise 3 - Vehicle Detection on CPU with FP32</summary>
    
    
**Exercise 3: Vehicle Detection on CPU**

***Step 1 - Setup the Model***

    1. In the Workbench GUI, Select "Import Model"
    2. Select vehicle-detection-adas-0002 (FP32)
    3. Select Import
    4. Note: If there are problems downloading, try the alternate method below.

Alternate Method:

    1.  In the DLW Web GUI (web browser):
        - Select Import Model -> Original Model
    2. Select Choose File:
        - ~/Desktop/Data/Models/Intel/FP32/vehicle-detection-adas-0002.xml
    3. Select the Second Choose File Button:
         - ~/Desktop/Data/Models/Intel/FP32/vehicle-detection-adas-0002.bin 
    4. Select Import

***Step 2 - Import Dataset***

    1. Select Import Local Dataset -> Choose File
    2. Select: ~/Desktop/Data/datasets/VOC7_248.zip
    3. Select Import Dataset

***Step 3 - Select Environment***
    1. Select CPU
   
***Step 4 - Download the Model***
Find the download icon in the top right bar of the DL Workbench (next to the trash can).  This will download the model to the default directory: ~/Downloads, as a tar.gz file.  We'll move this file in the next step for convenience.
   
***Step 5 - Additional Setup for Ease of Use***

We'll create a set of directories to store our models, and separate them by their format.  

Remember:
FP32 is for CPU (CPU can run FP16, but it will be slower).  GPU can run FP32 models but it will be sub-optimal.
FP16 is for all hardware except CPU.
INT8 is for CPU.  Some models will be very fast in the INT8 format.

NOTE:  FPGA may support FP11 in some cases through bitstreams.  This will usually be the fastest option for FPGA, and conversion is automatically handled with FP16 models.

    1. mkdir ~/ir
    2. mkdir ~/ir/FP32
    3. mkdir ~/ir/FP16
    4. mkdir ~/ir/INT8

Now we move some files around to make running the samples a little easier.

    1. cp ~/Downloads/vehicle-detection-adas-* ~/ir/FP32
    2. cd ~/ir/FP32
    3. mv ~/Downloads/vehicle-detection-adas-*tar.gz .
    4. tar zxvf vehicle-detection-adas-0002.tar.gz
    5. rm vehicle*tar.gz
    6. cd ~/omz_demos_build/intel64/Release

And we're ready to execute the sample:

    `./security_barrier_camera_demo -i ~/Videos/cars_hwy.mp4 -m ~/ir/FP32/vehicle-detection-adas-0002.xml -d CPU`

</details>


<details>
   <summary>Exercise 4 - Vehicle Detection GPU using FP16 format</summary>

**Exercise 4: Vehicle Detection on GPU**

***Step 1 - Setup the Model***

    1. In the DL Workbench Web GUI:  
        - Select Import Model
    2. Select vehicle-detection-adas-0002 (FP16)
    3. Select Import
    4. Note: If there are problems downloading, try the alternate method below.

Alternate Method:
    1.  In the DLW Web GUI:
        - Select Import Model -> Original Model
    2. Select Choose File:
        - ~/Desktop/Data/Models/Intel/FP16/vehicle-detection-adas-0002.xml
    3. Select the Second Choose File Button:
         - ~/Desktop/Data/Models/Intel/FP16/vehicle-detection-adas-0002.bin 
    4. Select Import

***Step 2 - Import Dataset***

    1. Import Local Dataset -> Choose File
    2. Select: ~/Desktop/Data/datasets/VOC7_248.zip
    3. Import Dataset

***Step 3 - Select Environment***
    1. Select GPU
   
***Step 4 - Download the Model***
Find the download icon in the top right bar of the DL Workbench (next to the trash can).  This will download the model to the default directory: ~/Downloads, as a tar.gz file.  We'll move this file in the next step for convenience.

***Step 5 - Additional Setup for Ease of Use***

We'll create a set of directories to store our models, and separate them by their format.  

Remember:
FP32 is for CPU (CPU can run FP16, but it will be slower).  GPU can run FP32 models but it will be sub-optimal.
FP16 is for all hardware except CPU.
INT8 is for CPU.  Some models will be very fast in the INT8 format.

NOTE:  FPGA may support FP11 in some cases through bitstreams.  This will usually be the fastest option for FPGA, and conversion is automatically handled with FP16 models.

Skip this step if it was done in the previous exercise.
    1. `mkdir ~/ir`
    2. `mkdir ~/ir/FP32`
    3. `mkdir ~/ir/FP16`
    4. `mkdir ~/ir/INT8`

Now we move some files around to make running the samples a little easier.

    1.` cp ~/Downloads/vehicle-detection-adas-* ~/ir/FP16`
    2.` cd ~/ir/FP16`
    3. `tar zxvf vehicle-detection-adas-0002.tar.gz`
    4. `rm vehicle*tar.gz`
    5. `cd ~/omz_demos_build/intel64/Release`

And we're ready to execute the sample:

    `./security_barrier_camera_demo -i ~/Videos/cars_hwy.mp4 -m ~/ir/FP16/vehicle-detection-adas-0002.xml -d GPU`

</details>

<details>
   <summary>Exercise 5 - Vehicle Detection with INT8</summary>
    
**Exercise 5: Vehicle Detection on CPU using INT8**

***Step 1 - Setup the Model***

Skip this section if done in previous exercise.

    1. In the DL Workbench GUI, select Import Model
    2. Select vehicle-detection-adas-0002 (FP32)
    3. Select Import

***Step 2 - Import Dataset***

Skip this section if done in previous exercise.

    1. Select Import Local Dataset
    2. Press the "Choose File" button.
    2. Select: ~/Desktop/Data/datasets/VOC7_248.tar.gz
    3. Select Import

***Step 3 - Select Environment***

    1. In the upper panel, select the FP32 vehicle detection model.
    2. In the middle panel, select the VOC 248 dataset.
    3. In the lower left panel, select the CPU.
    4. Press the GO button.
    5. This will take several minutes.
    
***Step 5 - Convert the Model to the INT8 format.***

    1. Look for the Profile and Optimize tabs.  Select Optimize.
    2. Select INT8.
    3. Press the Optimize button.  This will take a few minutes.

    4. In the upper panel (model summary), click on the cog icon in the Accuracy column.
    5. Select "Object Detection" and "ssd" if not set.
    6. Press the "Run Accuracy Check" Button.  This will take several minutes.
    7. You should have been returned to the main screen.  Press the Execute button.

    8. Download the model.
   
***Step 6 - Additional Setup for Ease of Use***

Now we move some files to make running the samples a little easier.
NOTE:  The unzipped int8 bin and xml files are dynamically generated and may not exactly match the names listed below (11_int8.xml).  Make sure to substitute the correct xml name.

    1. mv ~/Downloads/"vehicle-detection-adas-0002_- Int 8.tar.gz" ~/ir/INT8
    2. cd ~/ir/INT8
    3. tar zxvf vehicle-detection-adas-0002*.tar.gz
    4. rm vehicle*tar.gz
    5. cd ~/omz_demos_build/intel64/Release

And we're ready to execute the sample:

    ./security_barrier_camera_demo -i ~/Videos/cars_hwy.mp4 -m ~/ir/INT8/11_int8.xml -d CPU

</details>


## Additional Resources

Use these resources to learn more about the OpenVINO™ toolkit:

* [OpenVINO™ Release Notes](https://software.intel.com/en-us/articles/OpenVINO-RelNotes)
* [Introduction to Intel® Deep Learning Deployment Toolkit](./docs/IE_DG/Introduction.md)
* [Inference Engine Developer Guide](./docs/IE_DG/Deep_Learning_Inference_Engine_DevGuide.md)
* [Model Optimizer Developer Guide](./docs/MO_DG/Deep_Learning_Model_Optimizer_DevGuide.md)
* [Inference Engine Samples Overview](./docs/IE_DG/Samples_Overview.md)
* [Overview of OpenVINO™ Toolkit Pre-Trained Models](./docs/Pre_Trained_Models.md)
* [OpenVINO™ Hello World Face Detection Exercise](https://github.com/intel-iot-devkit/inference-tutorials-generic)
