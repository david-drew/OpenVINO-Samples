
# Get Started with OpenVINO™ on Linux

The OpenVINO™ toolkit optimizes and runs Deep Learning Neural Network models on Intel® hardware. This guide helps you get started with the OpenVINO™ toolkit you installed on a Linux* operating system. 

In this guide, you will:
* Learn the OpenVINO™ inference workflow.
* Run demo scripts that perform the steps for you. These demo scripts illustrate the workflow.
* Run the workflow steps yourself, using detailed instructions with a code sample and demo application.       

## <a name="openvino-components"></a>OpenVINO™ toolkit Components
The toolkit consists of three primary components:
* **Inference Engine:** The software libraries that run inference against the Intermediate Representation (optimized model) to produce inference results.
* **Model Optimizer:** Optimizes models for Intel® architecture, converting models into a format compatible with the Inference Engine. This format is called an Intermediate Representation (IR).
* **Intermediate Representation (IR):** The Model Optimizer output. A model converted to a format that has been optimized for Intel® architecture and is usable by the Inference Engine.

In addition, code samples and applications are provided to help you get up and running with the toolkit:
* [**Code Samples**](https://docs.openvinotoolkit.org/latest/_docs_IE_DG_Samples_Overview.html) - Small console applications that show you how to: 
    * Utilize specific OpenVINO capabilities in an application
    * Help developers perform specific tasks, such as loading a model, running inference, querying specific device capabilities, and more.
* [**Demo Applications**](https://docs.openvinotoolkit.org/latest/_demos_README.html) - Console applications that provide robust application templates to help you implement specific deep learning scenarios. These applications involve increasingly complex processing pipelines that gather analysis data from several models that run inference simultaneously, such as detecting a person in a video stream along with detecting the person's physical attributes, such as age, gender, and emotional state.

## <a name="openvino-installation"></a>Intel® Distribution of OpenVINO™ toolkit Installation and Directory Structure
This guide assumes you completed all Intel® Distribution of OpenVINO™ toolkit installation and configuration steps. If you have not yet installed and configured the toolkit, see [Install Intel® Distribution of OpenVINO™ toolkit for Linux*](https://docs.openvinotoolkit.org/latest/_docs_install_guides_installing_openvino_linux.html).

By default, the installation directory is `/opt/intel/openvino`, but the installation gave you the option to use the directory of your choice. If you installed the Intel® Distribution of OpenVINO™ toolkit to a directory other than the default, replace `/opt/intel` with the directory in which you installed the software.

<details>
    <summary>Click for the Intel® Distribution of OpenVINO™ toolkit directory structure.</summary>
   

| Directory&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Description                                                                           |  
|:----------------------------------------|:--------------------------------------------------------------------------------------|
| `demo/`                                 | Demo scripts. Demonstrate pipelines for inference scenarios, automatically perform steps and print detailed output to the console. For more information, see the [Use OpenVINO: Demo Scripts](#use-openvino-demo-scripts) section.|
| `inference_engine/`                     | Inference Engine directory. Contains Inference Engine API binaries and source files, samples and extensions source files, and resources like hardware drivers.|
| `~intel_models/` | Symbolic link to the `intel_models` subfolder of the `open_model-zoo` folder |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`include/`      | Inference Engine header files. For API documentation, see the [Inference Engine API Reference](./annotated.html). |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`lib/`          | Inference Engine binaries.|
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`samples/`      | Inference Engine samples. Contains source code for C++ and Python* samples and build scripts. See the [Inference Engine Samples Overview](./docs/IE_DG/Samples_Overview.md). |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`src/`          | Source files for CPU extensions.|
| `model_optimizer/`                      | Model Optimizer directory. Contains configuration scripts, scripts to run the Model Optimizer and other files. See the [Model Optimizer Developer Guide](./docs/MO_DG/Deep_Learning_Model_Optimizer_DevGuide.md).
| `open_model_zoo/`                       | Open Model Zoo directory. Includes the Model Downloader tool to download [pre-trained OpenVINO](./docs/Pre_Trained_Models.md) and public models, OpenVINO models documentation, demo applications and the Accuracy Checker tool to evaluate model accuracy.|
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`demos/`        | Demo applications for inference scenarios. Also includes documentation and build scripts.| 
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`intel_models/` | Pre-trained OpenVINO models and associated documentation. See the [Overview of OpenVINO™ Toolkit Pre-Trained Models](./docs/Pre_Trained_Models.md).|
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`tools/`        | Model Downloader and Accuracy Checker tools. |
| `tools/`                                | Contains a symbolic link to the Model Downloader folder and auxiliary tools to work with your models: Calibration tool, Benchmark and Collect Statistics tools.|

</details>

## <a name="workflow-overview"></a>OpenVINO™ Workflow Overview

The simplified OpenVINO™ workflow is:
1. **Get a trained model** for your inference task. Example inference tasks: pedestrian detection, face detection, vehicle detection, license plate recognition, head pose.
2. **Run the trained model through the Model Optimizer** to convert the model to an Intermediate Representation, which consists of a pair of `.xml` and `.bin` files that are used Inference Engine input.
3. **Use the Inference Engine API in the application** to run inference against the Intermediate Representation (pptimized model) and output inference results. The application can be an OpenVINO™ sample, demo, or your own application. 
<br>

![OpenVINO Workflow Diagram](OV_Workflow_FastPath_1.png)

<br>

## How to Use the Demo Scripts to Learn the Workflow

The demo scripts in `/opt/intel/openvino/deployment_tools/demo` give you a starting point to learn the OpenVINO workflow. These scripts automatically perform the workflow steps to demonstrate running inference pipelines for different scenarios. The demo steps let you see how to: 
* Compile several samples installed as part of the OpenVINO toolkit.
* Download trained models.
* Perform pipeline steps and see the output on the console.

> **NOTE**: You must have Internet access to run the demo scripts. If your Internet access is through a proxy server, make sure the operating system environment proxy information is configured.

The demo scripts can run inference on any [supported target device](https://software.intel.com/en-us/openvino-toolkit/hardware). Although the default inference device is CPU, you can use the `-d` parameter to change the inference device. The general command to run the scripts looks as follows:

```sh
./<script_name> -d [CPU, GPU, MYRIAD, HDDL]
```

Before running the demo applications on Intel® Processor Graphics or on an Intel® Neural Compute Stick 2 device, you must complete the  [Steps for Intel® Processor Graphics (GPU)](https://docs.openvinotoolkit.org/2020.1/_docs_install_guides_installing_openvino_linux.html#additional-GPU-steps) or [Steps for Intel® Neural Compute Stick 2](https://docs.openvinotoolkit.org/2020.1/_docs_install_guides_installing_openvino_linux.html#additional-NCS-steps).

The following paragraphs describe each demo script.

### Image Classification Demo Script
The `demo_squeezenet_download_convert_run` script illustrates the image classification pipeline.

The script: 
1. Downloads a SqueezeNet model. 
2. Runs the Model Optimizer to convert the model to the IR.
3. Builds the Image Classification Sample Async application.
4. Runs the compiled sample with the `car.png` image located in the `demo` directory.

<details>
    <summary><strong>Click for an example of running the Image Classification demo script</strong></summary>

To run the script to perform inference on a CPU:

```sh
./demo_squeezenet_download_convert_run.sh
```

When the script completes, you see the label and confidence for the top-10 categories:

```sh

Top 10 results:

Image /home/user/dldt/inference-engine/samples/sample_data/car.png

classid probability label
------- ----------- -----
817     0.8363345   sports car, sport car
511     0.0946488   convertible
479     0.0419131   car wheel
751     0.0091071   racer, race car, racing car
436     0.0068161   beach wagon, station wagon, wagon, estate car, beach waggon, station waggon, waggon
656     0.0037564   minivan
586     0.0025741   half track
717     0.0016069   pickup, pickup truck
864     0.0012027   tow truck, tow car, wrecker
581     0.0005882   grille, radiator grille


total inference time: 2.6642941
Average running time of one iteration: 2.6642941 ms

Throughput: 375.3339402 FPS

[ INFO ] Execution successful
```
</details>

### Inference Pipeline Demo Script
The `demo_security_barrier_camera` uses vehicle recognition in which vehicle attributes build on each other to narrow in on a specific attribute.

The script:
1. Downloads three pre-trained model IRs.
2. Builds the Security Barrier Camera Demo application.
3. Runs the applicaiton with the downloaded models and the `car_1.bmp` image from the `demo` directory to show an inference pipeline. 

This application:

1. Identifies an object identified as a vehicle. 
2. Uses the vehicle identification as input to the second model, which identifies specific vehicle attributes, including the license plate.
3. Uses the the licence plate as input to the third model, which recognizes specific characters in the license plate.

<details>
    <summary><strong>Click for an example of Running the Pipeline demo script</strong></summary>
    
To run the script performing inference on Intel® Processor Graphics:

```sh
./demo_security_barrier_camera.sh -d GPU
```

When the verification script completes, you see an image that displays the resulting frame with detections rendered as bounding boxes, and text:

![](https://docs.openvinotoolkit.org/latest/inference_pipeline_script_lnx.png)
</details>

### Benchmark Demo Script
The `demo_benchmark_app` script illustrates how to use the Benchmark Application to estimate deep learning inference performance on supported devices. 

The script: 
1. Downloads a SqueezeNet model.
2. Runs the Model Optimizer to convert the model to the IR.
3. Builds the Inference Engine Benchmark tool.
4. Runs the tool with the `car.png` image located in the `demo` directory.

<details>
    <summary><strong>Click for an example of running the Benchmark demo script</strong></summary>

To run the script that performs inference on Intel® Vision Accelerator Design with Intel® Movidius™ VPUs:

```sh
./demo_squeezenet_download_convert_run.sh -d HDDL
```
When the verification script completes, you see the performance counters, resulting latency, and throughput values displayed on the screen.
</details>

## <a name="using-sample-application"></a>How to Use the Workflow with Code Samples and Demo Applications

This section guides you through a simplified workflow for the Intel® Distribution of OpenVINO™ toolkit using code samples and demo applications. 

You will perform the following steps: 

1. <a href="#download-models">Use the Model Downloader to download suitable models.</a>
2. <a href="#convert-models-to-intermediate-representation">Convert the models with the Model Optimizer.</a> 
3. <a href="#download-media">Download media files to run inference on.</a>
4. <a href="#run-image-classification">Run inference on the Image Classification Code Sample and see the results</a>. 
5. <a href="#run-security-barrier">Run inference on the Security Barrier Camera Demo application and see the results</a>.

Each demo and code sample is a separate application, but they use the same behavior and components. The code samples and demo applications are:

* [Code Samples](https://docs.openvinotoolkit.org/latest/_docs_IE_DG_Samples_Overview.html) - Small console applications that show how to utilize specific OpenVINO capabilities within an application and execute specific tasks such as loading a model, running inference, querying specific device capabilities, and more.

* [Demo Applications](https://docs.openvinotoolkit.org/latest/_demos_README.html) - Console applications that provide robust application templates to support developers in implementing specific deep learning scenarios. They may also involve more complex processing pipelines that gather analysis from several models that run inference simultaneously. For example concurrently detecting a person in a video stream and detecting attributes such as age, gender and/or emotions.
 
Inputs you'll need to specify:
- **A compiled OpenVINO™ code sample or demo application** that runs inferencing against a model that has been run through the Model Optimizer, resuiting in an IR, using the other inputs you provide.
- **One or more models** in the Intermediate Representation format. Each model is trained for a specific task. Examples include pedestrian detection, face detection, vehicle detection, license plate recognition, head pose, and others. Different models are used for different applications. Models can be chained together to provide multiple features; for example vehicle + make/model + license plate recognition.
- **One or more media files**. The media is typically a video file, but can be a still photo.
- **One or more target device** on which you run inference. The target device can be the CPU, GPU, FPGA, or VPU accelerator.

### Build the Code Samples and Demo Applications 

To perform sample inference, run the Image Classification code sample and Security Barrier Camera demo application that were automatically compiled when you ran the Image Classification and Inference Pipeline demo scripts. The binary files are in the `~/inference_engine_cpp_samples_build/intel64/Release` and `~/inference_engine_demos_build/intel64/Release` directories, respectively.

To run other sample code or demo applications, build them from the source files delivered with the installation. To learn how to build these, see the [Inference Engine Code Samples Overview](https://docs.openvinotoolkit.org/latest/_docs_IE_DG_Samples_Overview.html#build_samples_linux) and the [Demo Applications Overview](https://docs.openvinotoolkit.org/latest/_demos_README.html#build_the_demo_applications) sections.

### <a name="download-models"></a> Step 1: Download the Models

You must have a model that is specific for you inference task. Example model types are:
- Classification (AlexNet, GoogleNet, SqueezeNet, others) - Detects one type of element in a frame.
- Object Detection (SSD, YOLO) - Draws bounding boxes around multiple types of objects.
- Custom (Often based on SSD)

Options to find a model suitable for the OpenVINO™ toolkit are:
- Download public and Intel's pre-trained models from the [Open Model Zoo](https://github.com/opencv/open_model_zoo) using [Model Downloader tool](https://docs.openvinotoolkit.org/latest/_tools_downloader_README.html#model_downloader_usage).
- Download from GitHub*, Caffe* Zoo, TensorFlow* Zoo, etc.
- Train your own model.
        
This guide uses the Model Downloader to get pre-trained models. Use one of these options to find a model:

* **List the models available in the downloader**: 
```sh
cd /opt/intel/openvino/deployment_tools/tools/model_downloader/
```
```sh
python3 info_dumper.py --print_all
```

* **Use `grep` to list models that have a specific name pattern**: 
```sh
python3 info_dumper.py --print_all | grep <model_name>
```

Use the Model Optimizer to download the models to a models directory. This guide uses `<models_dir>` as the models directory and `<models_name>` as the model name:
```sh
sudo python3 ./downloader.py --name <model_name> --output_dir <models_dir>
```
> **NOTE:** Always run the downloader with `sudo`.

Download the following models if you want to run the Image Classification Sample and Security Barrier Camera Demo application:

|Model Name                                     | Code Sample or Demo App                             |
|-----------------------------------------------|-----------------------------------------------------|
|`squeezenet1.1`                                | Image Classification Sample                         |
|`vehicle-license-plate-detection-barrier-0106` | Security Barrier Camera Demo application            |
|`vehicle-attributes-recognition-barrier-0039`  | Security Barrier Camera Demo application            |
|`license-plate-recognition-barrier-0001`       | Security Barrier Camera Demo application            |

<details>
    <summary><strong>Click for an example of downloading the SqueezeNet Caffe* model</strong></summary>

To download the SqueezeNet 1.1 Caffe* model to the `~/models` folder:

```sh
sudo python3 ./downloader.py --name squeezenet1.1 --output_dir ~/models
```

Your screen looks similar to this after the download:
```
###############|| Downloading topologies ||###############

========= Downloading /home/username/models/public/squeezenet1.1/squeezenet1.1.prototxt

========= Downloading /home/username/models/public/squeezenet1.1/squeezenet1.1.caffemodel
... 100%, 4834 KB, 3157 KB/s, 1 seconds passed

###############|| Post processing ||###############

========= Changing input dimensions in squeezenet1.1.prototxt =========
```
</details>

### <a name="convert-models-to-intermediate-representation"></a> Step 2: Convert the Models to the Intermediate Representation

In this step, your trained models are ready to run through the Model Optimizer to convert them to the Intermediate Representation format. This is required before using the Inference Engine with the model.

Models in the Intermediate Representation format always include an `.xml` and `.bin` file, and may also include other files, like `.json`, `.mapping`, or others. Make sure you have these files in the same directory for the Inference Engine to find them.
- **REQUIRED:** `model_name.xml`
- **REQUIREDL:** `model_name.bin`
- **OPTIONAL:** `model_name.json`, `model_name.mapping`, etc.

This guide uses the public SqueezeNet 1.1 Caffe\* model to run the Image Classification Sample. See the example to download a model in the <a href="#download-models">Download Models</a> section to learn how to download this model.

The `squeezenet1.1` model is downloaded in the Caffe* format. You must use the Model Optimizer to convert the model to the IR. 
The `vehicle-license-plate-detection-barrier-0106`, `vehicle-attributes-recognition-barrier-0039`, `license-plate-recognition-barrier-0001` models are downloaded in the Intermediate Representation format. You don't need to use the Model Optimizer to covert these models.

1. Create an `<ir_dir>` directory to contain the model's Intermediate Representation (IR). 

2. The Inference Engine can perform inference on different precision formats, such as `FP32`, `FP16`, `INT8`. To prepare an IR with specific precision, run the Model Optimizer with the appropriate `--data_type` option.

3. Run the Model Optimizer script:
   ```sh
   cd /opt/intel/openvino/deployment_tools/model_optimizer
   ```
   ```sh  
   python3 ./mo.py --input_model <model_dir>/<model_file> --data_type <model_precision> --output_dir <ir_dir>
   ```
   The produced IR files are in the `<ir_dir>` directory.

<details>
    <summary><strong>Click for an example of converting the SqueezeNet Caffe* model</strong></summary>

The following command converts the public SqueezeNet 1.1 Caffe\* model to the FP16 IR and saves to the `~/models/public/squeezenet1.1/ir` output directory:

```sh
   cd /opt/intel/openvino/deployment_tools/model_optimizer
   ```
   ```sh  
   python3 ./mo.py --input_model ~/models/public/squeezenet1.1/squeezenet1.1.caffemodel --data_type FP16 --output_dir ~/models/public/squeezenet1.1/ir
   ```

After the Model Optimizer script is completed, the produced IR files (`squeezenet1.1.xml`, `squeezenet1.1.bin`) are in the specified `~/models/public/squeezenet1.1/ir` directory.

Copy the `squeezenet1.1.labels` file from the `/opt/intel/openvino/inference-engine/samples/sample_data/` to `<ir_dir>`. This file contains the classes that ImageNet uses. Therefore, the inference results show text instead of classification numbers:
   ```sh   
   cp /opt/intel/openvino/inference-engine/samples/sample_data/squeezenet1.1.labels <ir_dir>
   ```
</details>

### <a name="download-media"></a> Step 3: Download a Video or a Still Photo as Media

Many sources are available from which you can download video media to use the code samples and demo applications. Possibilities include: 
- https://videos.pexels.com
- https://images.google.com

As an alternative, the Intel® Distribution of OpenVINO™ toolkit includes two sample images that you can use for running code samples and demo applications:
* `/opt/intel/openvino/deployment_tools/demo/car.png`
* `/opt/intel/openvino/deployment_tools/demo/car_1.bmp`

### <a name="run-image-classification"></a>Step 4: Run the Image Classification Code Sample

To run the **Image Classification** code sample with an input image on the IR: 

1. Set up the OpenVINO environment variables:
   ```sh
   source /opt/intel/openvino/bin/setupvars.sh
   ``` 
2. Go to the code samples build directory:
   ```sh
   cd ~/inference_engine_cpp_samples_build/intel64/Release
   ```
3. Run the code sample executable, specifying the input media file, the IR of your model, and a target device on which you want to perform inference:
   ```sh
   classification_sample -i <path_to_media> -m <path_to_model> -d <target_device>
   ```
<details>
    <summary><strong>Click for examples of running the Image Classification code sample on different devices</strong></summary>

The following commands run the Image Classification Code Sample using the `car.png` file from the `/opt/intel/openvino/deployment_tools/demo/` directory as an input image, the IR of your model from `~/models/public/squeezenet1.1/ir` and on different hardware devices:

**CPU:**
   ```sh
   ./classification_sample -i /opt/intel/openvino/deployment_tools/demo/car.png -m ~/models/public/squeezenet1.1/ir/squeezenet1.1.xml -d CPU
   ```

   **GPU:**
   >**NOTE**: Running inference on Intel® Processor Graphics (GPU) requires 
    [additional hardware configuration steps](https://docs.openvinotoolkit.org/latest/_docs_install_guides_installing_openvino_linux.html#additional-GPU-steps).
   ```sh
   ./classification_sample -i /opt/intel/openvino/deployment_tools/demo/car.png -m ~/models/public/squeezenet1.1/ir/squeezenet1.1.xml -d GPU
   ```
   
   **MYRIAD:** 

   >**NOTE**: Running inference on VPU devices (Intel® Movidius™ Neural Compute 
   Stick or Intel® Neural Compute Stick 2) with the MYRIAD plugin requires 
    [additional hardware configuration steps](inference-engine/README.md#optional-additional-installation-steps-for-the-intel-movidius-neural-compute-stick-and-neural-compute-stick-2).
   ```sh   
   ./classification_sample -i /opt/intel/openvino/deployment_tools/demo/car.png -m ~/models/public/squeezenet1.1/ir/squeezenet1.1.xml -d MYRIAD
   ```

When the Sample Application completes, you see the label and confidence for the top-10 categories on the display. Below is a sample output with inference results on CPU:    
```sh
Top 10 results:

Image /home/user/dldt/inference-engine/samples/sample_data/car.png

classid probability label
------- ----------- -----
817     0.8363345   sports car, sport car
511     0.0946488   convertible
479     0.0419131   car wheel
751     0.0091071   racer, race car, racing car
436     0.0068161   beach wagon, station wagon, wagon, estate car, beach waggon, station waggon, waggon
656     0.0037564   minivan
586     0.0025741   half track
717     0.0016069   pickup, pickup truck
864     0.0012027   tow truck, tow car, wrecker
581     0.0005882   grille, radiator grille


total inference time: 2.6642941
Average running time of one iteration: 2.6642941 ms

Throughput: 375.3339402 FPS

[ INFO ] Execution successful
```
</details>

### <a name="run-security-barrier"></a>Step 5: Run the Security Barrier Camera Demo Application

To run the **Security Barrier Camera Demo Application** using an input image on the prepared IRs:

1. Set up the OpenVINO environment variables:
   ```sh
   source /opt/intel/openvino/bin/setupvars.sh
   ``` 
2. Go to the demo application build directory:
   ```sh
   cd ~/inference_engine_demos_build/intel64/Release
   ```
3. Run the demo executable, specifying the input media file, list of model IRs, and a target device on which to perform inference:
   ```sh
   ./security_barrier_camera_demo -i <path_to_media> -m <path_to_model>/vehicle-license-plate-detection-barrier-0106.xml -m_va <path_to_vehicle_attributes model>/vehicle-attributes-recognition-barrier-0039.xml -m_lpr <path_to_license_plate_recognition_model>/license-plate-recognition-barrier-0001.xml -d <target_device>
   ```

<details>
    <summary><strong>Click for examples of running the Security Barrier Camera demo application on different devices</strong></summary>


**CPU:**
```sh
./security_barrier_camera_demo -i /opt/intel/openvino/deployment_tools/demo/car_1.bmp -m <path_to_model>/vehicle-license-plate-detection-barrier-0106.xml -m_va <path_to_model>/vehicle-attributes-recognition-barrier-0039.xml -m_lpr <path_to_model>/license-plate-recognition-barrier-0001.xml -d CPU
```

**GPU:**
>**NOTE**: Running inference on Intel® Processor Graphics (GPU) requires 
    [additional hardware configuration steps](https://docs.openvinotoolkit.org/latest/_docs_install_guides_installing_openvino_linux.html#additional-GPU-steps).
```sh
./security_barrier_camera_demo -i /opt/intel/openvino/deployment_tools/demo/car_1.bmp -m <path_to_model>/vehicle-license-plate-detection-barrier-0106.xml -m_va <path_to_model>/vehicle-attributes-recognition-barrier-0039.xml -m_lpr <path_to_model>/license-plate-recognition-barrier-0001.xml -d GPU
```

**MYRIAD:** 
   >**NOTE**: Running inference on VPU devices (Intel® Movidius™ Neural Compute 
   Stick or Intel® Neural Compute Stick 2) with the MYRIAD plugin requires 
    [additional hardware configuration steps](https://docs.openvinotoolkit.org/latest/_docs_install_guides_installing_openvino_linux.html#additional-NCS-steps).
   ```sh   
   ./classification_sample -i <DLDT_DIR>/inference-engine/samples/sample_data/car.png -m <ir_dir>/squeezenet1.1.xml -d MYRIAD
   ```

</details>

## <a name="basic-guidelines-sample-application"></a>Basic Guidelines for Using Code Samples and Demo Applications

Following are some basic guidelines for executing the OpenVINO™ workflow using the code samples and demo applications:

1. Before using the OpenVINO™ samples, always set up the environment: 
```sh
source /opt/intel/openvino/bin/setupvars.sh
``` 
2. Have the directory path for the following:
- Code Sample binaries located in `~/inference_engine_cpp_samples_build/intel64/Release`
- Demo Application binaries located in `~/inference_engine_demos_build/intel64/Release`
- Media: Video or image. See <a href="#download-media">Download Media</a>.
- Model: Neural Network topology converted with the Model Optimizer to the IR format (.bin and .xml files). See <a href="#download-models">Download Models</a> for more information.

## <a name="syntax-examples"></a> Typical Code Sample and Demo Application Syntax Examples

Template to call sample code or a demo application:

`<path_to_app> -i <path_to_media> -m <path_to_model> -d <target_device>`

With the sample information specified, the command might look like this:

`./object_detection_demo_ssd_async -i ~/Videos/catshow.mp4 \
-m ~/ir/fp32/mobilenet-ssd.xml -d CPU`

## <a name="advanced-samples"></a> Advanced Demo Use 

Some demo applications let you use multiple models for different purposes. In these cases, the output of the first model is usually used as the input for later models.

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


## Additional Resources

Use these resources to learn more about the OpenVINO™ toolkit:

* [OpenVINO™ Release Notes](https://software.intel.com/en-us/articles/OpenVINO-RelNotes)
* [Introduction to Intel® Deep Learning Deployment Toolkit](https://docs.openvinotoolkit.org/latest/_docs_IE_DG_Introduction.html)
* [Inference Engine Developer Guide](https://docs.openvinotoolkit.org/latest/_docs_IE_DG_Deep_Learning_Inference_Engine_DevGuide.html)
* [Model Optimizer Developer Guide](https://docs.openvinotoolkit.org/latest/_docs_MO_DG_Deep_Learning_Model_Optimizer_DevGuide.html)
* [Inference Engine Samples Overview](https://docs.openvinotoolkit.org/latest/_docs_IE_DG_Samples_Overview.html)
* [Overview of OpenVINO™ Toolkit Pre-Trained Models](https://software.intel.com/en-us/openvino-toolkit/documentation/pretrained-models)
* [OpenVINO™ Hello World Face Detection Exercise](https://github.com/intel-iot-devkit/inference-tutorials-generic)
