
# Get Started with OpenVINO™ Deep Learning Workbench and Samples for Linux*

## <a name="proposal"></a>Study Proposal (to delete later)
### Steps for participants in the study
**Introduction**
    - Explain the basic process
    - Brief overview of OpenVINO, samples/demos, and workbench.

**Samples:** are the collection of example applications (code and demos) provided with OpenVINO™.<br><br>
**DL Workbench:** is a tool for converting models and testing performance (speed and accuracy) with different model configuration settings.<br>

**Requirements**
- OpenVINO 2019.r3.1
- Docker Installation
- DL Workbench (downloaded from Docker hub)
- squeezenet, mobilenet-ssd models (on disk)
- ImageNet dataset (on disk) 
- Internet connectivity
- Ubuntu 16.04 (18 probably works, untested)

**Study Steps**
1. User reads introduction. 
2. Follow the introductory SqueezeNet steps - "Run a Sample Application".    
3. Follow the human pose steps - Exercise 1.
4. Duplicate the SqueezeNet section, this time using the Workbench instead of Model Optimizer.
5. Use the OpenVINO™ vehicle detection model to convert a model from FP32 to FP16.  
    - Run the FP16 model with the security barrier demo on the GPU.
    - This requires a "behind the scenes" download of the model via the Workbench.
    - [Optional] Encourage user to explore Workbench features?
6. Use the DL Workbench to convert a face detection model (provided on desktop).
    - [Optional] Encourage user to explore Workbench features?
    - Run simple face detection
    - Run face detection with additional models (age/gender, landmarks, emotion recognition, etc.) 

**Note:** there won't be time to complete Exercise 4, so I'm planning on cutting it.<br>
**Note2:** we're intending to add an extra "int8 conversion" step that's not currently detailed.

## ---- DELETE HERE ----



## <a name="openvino-components"></a>OpenVINO Components
The OpenVINO toolkit optimizes and runs Deep Learning Neural Network models on Intel hardware.
It consists of three primary components:
1.	**Model Optimizer:** Optimizes models for Intel architecture, as well as converts models into a format compatible with the Inference Engine. This format is called an Intemediate Representation (IR).
2.	**Intermediate Representation (IR):** The output of the Model Optimizer. A model converted to a format that has been optimized for Intel architecture and is usable by the Inference Engine.
3.	**Inference Engine:** The software libraries that run inference against the Intermediate Representation (Optimized Model) and produce inference results

## <a name="workflow-overview"></a>OpenVINO Workflow Overview

The basic OpenVINO workflow is as follows:
1.	Run an existing trained model through the Model Optimizer (or DL Workbench). 
2.	The Model Optimizer produces an Interemediate Representation file (IR).
3.	Application (an Intel sample or one you have built) uses the Inference Engine to run inference against the Intermediate Representation (Optimized Model).
4.	Application outputs inference results. 

## <a name="openvino-installation"></a>OpenVINO Installation
This guide assumes that you have completed installation of OpenVINO. <br>
The installation created a new directory in your home directory:

`~/omz_demos_build`

Note: There are 2 similar directories that contain samples, but we won't be using them for this tutorial.  For completeness, they are:
    `~/inference_engine_samples_build`
    `~/inference_engine_demos_build`

This is where sample applications are installed, after they are compiled.  You will have to compile most of the samples.


## <a name="dldt-installation-directory-structure"></a> Installation Directory Structure

By default, the installation directory is`/opt/intel/openvino`, though the location is up to the user.


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


## <a name="using-workbench"></a> Using OpenVINO: Deep Learning Workbench 
The DL Workbench is a tool to help assess model performance in OpenVINO, in terms of both speed and accuracy.  The workbench will automatically run the Model Optimizer on supported Caffe, ONNX, MXNet, and TensorFlow models, and then provide performance estimates based on tuning model parameters and selected target hardware.


## <a name="using-sample-application"></a> Using OpenVINO: Sample Applications

Sample applications are provided to get you working in your OpenVINO environment. This section will guide you through a simplified OpenVINO workflow using those sample applications. You will convert a model using the model optimizer then use a sample application to run inference and see the results.

Each of the included samples is a separate application, but there is a common behavior and common components needed to use each of them.

Inputs you'll need to specify:
- **A sample application** from Intel that runs inferencing against a model that has been optimized by the Model Optimizer into an Intermediate Representation (IR), using the other inputs you provide.
- **A model** must be provided for the application. Each model is trained for a specific task (pedestrian detection, face detection, vehicle detection, license plate recognition, head pose, etc.).  Different models will be used for different applications. Models can be chained together to provide multiple features (vehicle + make/model + license plate recognition).
- **A media file** to run through the application. For these samples, the media is typically a video file, though some applications require a image files. 
- **The processor type** to run on, which can be the CPU, GPU, FPGA, or VPU accelerator (e.g., Movidius board). 

## <a name="basic-guidelines-sample-application"></a> Basic Guidelines for Using Sample Applications

Following are some basic guidelines for executing the OpenVINO workflow and using the sample applications:
1.	Before using OpenVINO, always setup the environment: 

    `source /opt/intel/openvino/bin/setupvars.sh` 
    
2.	Have the directory pathways for the following:
- Sample application:
These are binaries located in:
`~/omz_demos_build/intel64/Release`
- Media: video or image.
- Model: Neural Network topology converted to a Model Optimizer compatible format, an XML file. See **Models** below for more information.

3.	Choose the correct precision for a model:
- FP32: Used when running the model on the CPU.
- Int8: Experimental, but runs on the CPU.
- FP16: Use when running on any hardware other than the CPU (except for some special cases, e.g., GPU can run FP32 but more slowly).

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
- Intel’s Model Downloader tool:
    - https://software.intel.com/en-us/articles/model-downloader-essentials)
    
    1.	To list the FP32 models available in the downloader enter the following command: 
    
        `cd /opt/intel/openvino/deployment_tools/tools/model_downloader/`
        
        `grep name list_topologies.yml |grep -vi fp16|grep -vi int8|grep xml|sort`

    2.	Optional add (at the end): 

        `| grep <model_name>`

    3.	Always run the downloader with `sudo`.

## <a name="downloading-media"></a> Downloading Media

There are multiple sources for downloading video media for use with samples. Possibilities include: 
- https://videos.pexels.com
- https://images.google.com

## <a name="syntax-examples"></a> Typical Sample Application Syntax Examples

Compiled sample applications for this tutorial are found in: `~/omz_demos_build/intel64/Release`.

Here is a template for calling a sample app:

`path_to_app -i path_to_media -m path_to_model -d processor_type`

With sample information filled in, the command could look like this:

`./object_detection_demo_ssd_async -i ~/Videos/catshow.mp4 \
-m ~/ir/fp32/mobilenet-ssd.xml -d CPU`

## <a name="advanced-samples"></a> Advanced Use of Samples 

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

## <a name="run-sample-application"></a> Run A Sample Application 

Convert a model using the Model Optmizer then use a sample application to load the model and run inference.

**NOTE:** If you are running inference only on a CPU, you already have the required FP32 model. If you want to run inference on any hardware other than the CPU, you'll need an FP16 version of the model.

**Set Up a Neural Network Model**

In this section, you will convert an FP32 model suitable for running on a CPU.

1.	Make a directory for the FP32 SqueezeNet Model:

    `mkdir ~/squeezenet1.1_FP32`

2.	Go to ~/squeezenet1.1_FP32:

    `cd ~/squeezenet1.1_FP32`

3.	Use the Model Optimizer to convert an FP32 SqueezeNet Caffe model into an optimized Intermediate Representation (IR):

    `python3 /opt/intel/openvino/deployment_tools/model_optimizer/mo.py --input_model ~/openvino_models/models/FP32/classification/squeezenet/1.1/caffe/squeezenet1.1.caffemodel --data_type FP32 --output_dir .`

4.	The `squeezenet1.1.labels` file contains the classes that ImageNet uses. This file is included so that the inference results show text instead of classification numbers. Copy `squeezenet1.1.labels` to your optimized model location:

    `cp ~/openvino_models/ir/FP32/classification/squeezenet/1.1/caffe/squeezenet1.1.labels .`

5.	Copy a sample image to the release directory. You will use this with your optimized model:

    `sudo cp /opt/intel/openvino/deployment_tools/demo/car.png  ~/omz_demos_build/intel64/Release`

6. Once your setup is complete, you're ready to run a sample application:

    `cd ~/omz_demos_build/intel64/Release`

    `./classification_sample -i car.png -m ~/squeezenet1.1_FP32/squeezenet1.1.xml -d CPU`

7. Note: you can usually see an applications help information (parameters, etc.) by using `-h`.

    `./classification_sample -h`

## <a name="Exercises"></a> Exercises

The following series of exercises guide you through using samples of increasing complexity. As you move through each exercise you will get a sense of how to use OpenVINO in more sophisticated use cases. 

**NOTE:** Before starting these sample exercises, change directories into the samples directory:

`cd ~/omz_demos_build/intel64/Release`

**NOTE:** During this exercise you will be asked to move to multiple different directories, and occasionally to copy files (so that you don't have to specify full paths in commands).  You are welcome to set up environment variables to make these tasks easier, but we leave that to you.

**Exercise 1: Human Pose Estimation**

This demo detects people and draws a stick figure inside them to show limb positions. This model has already been converted for use with OpenVINO. 
- Requires downloading the human-pose-estimation-0001 (ICV) Model.
- Requires video or camera input.

General Syntax:

`./human_pose_estimation_demo -i path/to/video -m \
path/to/model/human-pose-estimation-0001.xml -d CPU`

**Steps to Run the Human Pose Demo:**

1. Setup the environment variables:

    `source /opt/intel/openvino/bin/setupvars.sh`

2. Move to the Model Downloader Directory:

    `cd /opt/intel/openvino/deployment_tools/tools/model_downloader/`

3. Find a suitable model:

    `grep human-pose list_topologies.yml |grep -vi int8|grep -vi fp16`

**NOTE:** `list_topologies.yml` is a text (YAML) file with a list and details about every model available in the Intel Model Zoo.  Models can also be manually downloaded at the github page.

4. Download the model:

    `sudo ./downloader.py --name human-pose*`

5. Move the model to a more convenient location:

    `mkdir ~/ir`

    `cp /opt/intel/openvino/deployment_tools/tools/model_downloader/Transportation/human_pose_estimation/mobilenet-v1/dldt/FP32/human-pose-estimation-0001* ~/ir/`

6. Download an appropriate video:

    Open a web browser to the following URL and download the video: 
https://www.pexels.com/video/couple-dancing-on-sunset-background-2035509/

    Rename the video for convenience:

    `mv ~/Downloads/Pexels\ Videos\ 2035509.mp4 ~/Videos/humpose.mp4`

7. Run the sample:

    `cd ~/omz_demos_build/intel64/Release/`

    `./human_pose_estimation_demo -i ~/Videos/humpose.mp4 -m ~/ir/human-pose-estimation-0001.xml -d CPU`


**Exercise 2: Interactive Face Detection**

The face detection demo draws bounding boxes around faces, and optionally feeds the output of the primary model to additional models. This model has already been converted for use with OpenVINO.

The Face Detection Demo supports face detection, plus optional functions:

- Age-gender recognition
- Emotion recognition
- Head pose
- Facial landmark display

Syntax:

`./interactive_face_detection -i path/to/video -m path/to/face/model -d CPU`

Steps to complete:

1.	Find and download an appropriate face detection model (there are several already available in the Intel Model Zoo).
    - You can use the [Pretrained Models](https://software.intel.com/en-us/openvino-toolkit/documentation/pretrained-models) page from the online OpenVINO documentation to review model options.
    - You may need to try out different models to find one that works, or that works best.
2.	Find and download a video that features faces.
3.	Run the demo with just the face detection model.
4.	OPTIONAL: Run the demo using additional models (age-gender, emotion recognition, head pose, etc.).
    - Note that when you use multiple models, there is always a primary model that is used, and then a number of optional models that use the output from the initial model.


**Exercise 3: Object Detection (Vehicles with multiple models)**

As mentioned in the previous exercise, it's possible to chain models together and use the output of an initial object detection model as input of additional models.

The following exercise expects the use of the `security_barrier_camera_demo`.

Steps to complete:
1. Find and download a model that supports vehicle detection.
2. Find and download a video that contains cars to be detected.
3. Run the `security_barrier_camera_demo` and check that vehicles are detected.
4. Choose at least one additional model (such as attribute recognition), and run the application, now using the initial model plus the new one.
5. OPTIONAL: Run the demo with vehicle detection (primary), plus attribute recognition and license plate recognition.


**Exercise 4: Object Detection (YOLO v3)**

This demo draws bounding boxes around detected objects. The class of objects detected depends on the model, but it’s often 1000 categories. 

The YOLO topology is a very popular one because it offers both accuracy and performance. Intel does not provide any YOLO models in IR format, so you'll need to find a suitable model to download, convert it with the Model Optimizer, and then run it through the Inference Engine.

Different sample applications are used for SSD and YOLO models because the output format of YOLO is different than the output format for SSD. The same application could support both, but in these samples they’re kept separate for simplicity. Different versions of YOLO have slightly different output. This sample only supports the most recent version, YOLO V3.


## Additional Resources

Use these resources to learn more about the OpenVINO™ toolkit:

* [OpenVINO™ Release Notes](https://software.intel.com/en-us/articles/OpenVINO-RelNotes)
* [Introduction to Intel® Deep Learning Deployment Toolkit](./docs/IE_DG/Introduction.md)
* [Inference Engine Developer Guide](./docs/IE_DG/Deep_Learning_Inference_Engine_DevGuide.md)
* [Model Optimizer Developer Guide](./docs/MO_DG/Deep_Learning_Model_Optimizer_DevGuide.md)
* [Inference Engine Samples Overview](./docs/IE_DG/Samples_Overview.md)
* [Overview of OpenVINO™ Toolkit Pre-Trained Models](./docs/Pre_Trained_Models.md)
* [OpenVINO™ Hello World Face Detection Exercise](https://github.com/intel-iot-devkit/inference-tutorials-generic)
