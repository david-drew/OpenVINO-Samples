
# OpenVINO™ Overview 

## <a name="openvino-components"></a>OpenVINO Components
The OpenVINO toolkit optimizes and runs Deep Learning Neural Network models on Intel hardware.
It consists of three primary components:
1.	**Model Optimizer:** Optimizes models for Intel architecture, as well as converts models into a format compatible with the Inference Engine. This format is called an Intemediate Representation (IR).
2.	**Intermediate Representation (IR):** The output of the Model Optimizer. A model converted to a format that has been optimized for Intel architecture and is usable by the Inference Engine.
3.	**Inference Engine:** The software libraries that run inference against the Intermediate Representation (Optimized Model) and produce inference results

## <a name="workflow-overview"></a>OpenVINO Workflow Overview

The basic OpenVINO workflow is as follows:
1.	Run an existing trained model through the Model Optimizer. 
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

<details>
    <summary>OpenVINO Directory Structure</summary>

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

## <a name="using-sample-application"></a> Using OpenVINO: Sample Applications

Sample applications are provided to get you working in your OpenVINO environment. This section will guide you through a simplified OpenVINO workflow using those sample applications. You will convert a model using the model optimizer then use a sample application to run inference and see the results.

Each of the included samples is a separate application, but there is a common behavior and common components needed to use each of them.

Inputs you'll need to specify:
- **A sample application** from Intel that runs inferencing against a model that has been optimized by the Model Optimizer into an Intermediate Representation (IR), using the other inputs you provide.
- **A model** must be provided for the application. Each model is trained for a specific task (pedestrian detection, face detection, vehicle detection, license plate recognition, head pose, etc.).  Different models will be used for different applications. Models can be chained together to provide multiple features (vehicle + make/model + license plate recognition).
- **A media file** to run through the application. For these samples, the media is typically a video file but can be a still photo for some.
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


## Exercises

[Practical Exercises with OpenVINO on the Linux Commmand Line](OpenVINO_Samples_Guide.md)

[Practical Exercises with OpenVINO with the DL Workbench on Linux (some commmand line)](UX_Samples_Guide.md)


## Additional Resources

Use these resources to learn more about the OpenVINO™ toolkit:

* [OpenVINO™ Release Notes](https://software.intel.com/en-us/articles/OpenVINO-RelNotes)
* [Introduction to Intel® Deep Learning Deployment Toolkit](./docs/IE_DG/Introduction.md)
* [Inference Engine Developer Guide](./docs/IE_DG/Deep_Learning_Inference_Engine_DevGuide.md)
* [Model Optimizer Developer Guide](./docs/MO_DG/Deep_Learning_Model_Optimizer_DevGuide.md)
* [Inference Engine Samples Overview](./docs/IE_DG/Samples_Overview.md)
* [Overview of OpenVINO™ Toolkit Pre-Trained Models](./docs/Pre_Trained_Models.md)
* [OpenVINO™ Hello World Face Detection Exercise](https://github.com/intel-iot-devkit/inference-tutorials-generic)
