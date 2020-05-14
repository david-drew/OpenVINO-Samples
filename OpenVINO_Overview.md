
# Get Started with OpenVINO™ 

This guide provides you with the information that will help you to get started with OpenVINO toolkit on Linux*. With this guide, you will:
* Learn the OpenVINO™ inference workflow.
* Run the demo scripts that illustrate the workflow by performing all steps automatically.
* Perform the workflow steps using the detailed instructions and running a code sample and demo application.       

## <a name="openvino-components"></a>OpenVINO™ Components
OpenVINO™ toolkit optimizes and runs Deep Learning Neural Network models on Intel® hardware.
It consists of three primary components:
1. **Inference Engine:** The software libraries that run inference against the Intermediate Representation (optimized model) to produce inference results.
2. **Model Optimizer:** Optimizes models for Intel® architecture, converting models into a format compatible with the Inference Engine. This format is called an Intermediate Representation (IR).
3. **Intermediate Representation (IR):** Is the output of the Model Optimizer. A model converted to a format that has been optimized for Intel® architecture and is usable by the Inference Engine.

## <a name="openvino-installation"></a>Installation of Intel® Distribution of OpenVINO™ toolkit
This guide assumes that you have completed all installation and configuration steps for Intel® Distribution of OpenVINO™ toolkit. For instructions, see [Install Intel® Distribution of OpenVINO™ toolkit for Linux*](https://docs.openvinotoolkit.org/latest/_docs_install_guides_installing_openvino_linux.html).

By default, the installation directory is `/opt/intel/openvino`, though the location is up to the user.

> **NOTE**: If you installed the Intel® Distribution of OpenVINO™ to the non-default install directory, replace `/opt/intel` with the directory in which you installed the software.

<details>
    <summary>Structure of the Intel® Distribution of OpenVINO™ toolkit Directory</summary>

| Directory&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Description                                                                           |  
|:----------------------------------------|:--------------------------------------------------------------------------------------|
| `demo/`                                 | Demo scripts. Demonstrate pipelines for different inference scenarios, automatically perform the steps and print detailed output to the console. For more information, see the [Use OpenVINO: Demo Scripts](#use-openvino-demo-scripts) section.|
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

## <a name="workflow-overview"></a>OpenVINO™ Workflow Overview

The simplified OpenVINO™ workflow is as follows:
1. **Get a trained model** for your inference task, for example, pedestrian detection, face detection, vehicle detection, license plate recognition, head pose etc.
2. **Run the trained model through the Model Optimizer** to convert it to an Intermediate Representation - pair of `.xml` and `.bin` files used as the input for Inference Engine.
3. **Use the Inference Engine API in the application** (an OpenVINO™ sample, demo or one you have built) to run inference against the Intermediate Representation (Optimized Model) and output inference results.

## Learn the Workflow Using Demo Scripts

Use the demo scripts from the `/opt/intel/openvino/deployment_tools/demo` directory as a start point of learning the OpenVINO workflow. The scripts demonstrate execution of inference pipelines for different scenarios by automatically performing the workflow steps: compile several samples included into the installation, download trained models and perform pipeline steps with printing detailed output to the console.

> **IMPORTANT**: The Internet access is required to run the demo scripts. If you have access to the Internet through the proxy server only, please make sure that it is configured in your OS environment.

The scripts can run inference on any of [supported target devices](https://software.intel.com/en-us/openvino-toolkit/hardware). While the default inference device is CPU, though you can specify another one using the `-d` parameter. The general command to run the scripts looks as follows:

```sh
./<script_name> -d [CPU,GPU,MYRIAD,HDDL]
```
> **NOTE:** To run the demo applications on Intel® Processor Graphics or Intel® Neural Compute Stick 2 devices, make sure you first completed the additional [Steps for Intel® Processor Graphics (GPU)](https://docs.openvinotoolkit.org/2020.1/_docs_install_guides_installing_openvino_linux.html#additional-GPU-steps) or [Steps for Intel® Neural Compute Stick 2](https://docs.openvinotoolkit.org/2020.1/_docs_install_guides_installing_openvino_linux.html#additional-NCS-steps).

The following paragraphs provides description for each demo script.

### Image Classification Demo Script
`demo_squeezenet_download_convert_run` script illustrates the image classification pipeline and does the following: 
1. Downloads a SqueezeNet model. 
2. Runs the Model Optimizer to convert the model to the IR.
3. Builds the Image Classification Sample Async application.
4. Runs the compiled sample with the `car.png` image located in the `demo` directory.

<details>
    <summary><strong>Example of Running the Image Classification Demo Script</strong></summary>

For example, to run the script performing inference on CPU, use the following command:

```sh
./demo_squeezenet_download_convert_run.sh
```
When the verification script completes, you will have the label and confidence for the top-10 categories:

![](image_classification_script_output_lnx.png)
</details>

### Inference Pipeline Demo Script
`demo_security_barrier_camera` script automatically downloads three pre-trained model IRs, builds the Security Barrier Camera Demo application, and runs it with the downloaded models and the `car_1.bmp` image from the `demo` directory to show an inference pipeline. The script uses vehicle recognition in which vehicle attributes build on each other to narrow in on a specific attribute.

First, an object is identified as a vehicle. This identification is used as input to the next model, which identifies specific vehicle attributes, including the license plate. Finally, the attributes identified as the license plate are used as input to the third model, which recognizes specific characters in the license plate.

<details>
    <summary><strong>Example of Running the Pipeline Demo Script</strong></summary>
    
For example, to run the script performing inference on Intel® Processor Graphics, use the following command:

```sh
./demo_security_barrier_camera.sh -d GPU
```

When the verification script completes, you will see an image that displays the resulting frame with detections rendered as bounding boxes, and text:

![](inference_pipeline_script_lnx.png)
</details>

### Benchmark Demo Script
`demo_benchmark_app` script illustrates how to use the Benchmark Application to estimate deep learning inference performance on supported devices. The script does the following: 
1. Downloads a SqueezeNet model.
2. Runs the Model Optimizer to convert the model to the IR.
3. Builds the Inference Engine Benchmark tool.
4. Runs the tool with the `car.png` image located in the `demo` directory.

<details>
    <summary><strong>Example of Running the Benchmark Demo Script</strong></summary>

For example, to run the script performing inference on Intel® Vision Accelerator Design with Intel® Movidius™ VPUs, use the following command:

```sh
./demo_squeezenet_download_convert_run.sh -d HDDL
```
When the verification script completes, you will have performance counters, resulting latency, and throughput values printed.
</details>

## <a name="using-sample-application"></a>Go through the Workflow Using Code Samples and Demo Applications

This section will guide you through a simplified workflow for the Intel® Distribution of OpenVINO™ toolkit using Code Samples and Demo Applications. You will perform the following steps: 

* <a href="#download-models">Download suitable models using Model Downloader</a>.
* <a href="#convert-models">Convert the models using the Model Optimizer.</a> 
* <a href="download-media">Download media files to run inference on.</a>
* <a href="run-">Run the Image Classification Sample and Security Barrier Camera Demo application to run inference and see the results</a>. 

[Code Samples](https://docs.openvinotoolkit.org/latest/_docs_IE_DG_Samples_Overview.html) - Small console applications that show how to utilize specific OpenVINO capabilities within an application, assist developers in executing specific tasks such as loading a model, running inference, querying specific device capabilities and etc.

[Demo Applications](https://docs.openvinotoolkit.org/latest/_demos_README.html) - Console applications that provide robust application templates to support developers in implementing specific deep learning scenarios. They may also involve more complex processing pipelines that gather analysis from several models that run inference simultaneously. For example concurrently detecting a person in a video stream and detecting attributes such as age, gender and/or emotions.
 
Each of the included demos and code samples is a separate application, but there is a common behavior and common components needed to use each of them.

Inputs you'll need to specify:
- **A compiled OpenVINO™ code sample or demo application** from that runs inferencing against a model that has been optimized by the Model Optimizer into an Intermediate Representation (IR), using the other inputs you provide.
- **A model(s)** in the Intermediate Representation format must be provided for the application. Each model is trained for a specific task. Examples include pedestrian detection, face detection, vehicle detection, license plate recognition, head pose, and others. Different models are used for different applications. Models can be chained together to provide multiple features; for example vehicle + make/model + license plate recognition.
- **A media file(s)** to run through the application. For these samples, the media is typically a video file, but can be a still photo.
- **The target device(s)** to run inference on, which can be the CPU, GPU, FPGA, or VPU accelerator.

### Build Code Samples and Demo Applications 

In this guide, to perform sample inference, you will run the Image Classification code sample and Security Barrier Camera Demo application that were automatically compiled when you ran the Image Classification and Inference Pipeline demo scripts. The binary files are located in the `~/inference_engine_cpp_samples_build/intel64/Release` and `~/inference_engine_demos_build/intel64/Release` directories respectively.

If you want to run another sample, you need to build them from the source files delivered with the installation. To learn how to build the sample applications by yourself, see the [Inference Engine Samples Overview](https://docs.openvinotoolkit.org/latest/_docs_IE_DG_Samples_Overview.html#build_samples_linux) section.

### <a name="download-models"></a> Download Models

You need to have a model specific for you inference task. Examples of model types are:
- Classification (AlexNet, GoogleNet, SqueezeNet, etc.)
    - These detect one type of element in a frame.
- Object Detection (SSD, YOLO)
    - These draw bounding boxes around multiple types of objects.
- Custom (Often based on SSD)

There are several ways to find models suitable for use with OpenVINO™ toolkit:

- Download public and Intel's pre-trained models from the [Open Model Zoo](https://github.com/opencv/open_model_zoo) using [Model Downloader tool](https://docs.openvinotoolkit.org/latest/_tools_downloader_README.html#model_downloader_usage).
- Download from GitHub*, Caffe* Zoo, TensorFlow* Zoo, etc.
- Train your own model.
        
This guide describes using Model Downloader to get the pre-trained models.

To list models available in the downloader, enter the following command: 
```sh
cd /opt/intel/openvino/deployment_tools/tools/model_downloader/
```
```sh
python3 info_dumper.py --print_all
```

To list models with a specific name pattern, you can use the `grep` command: 
```sh
python3 info_dumper.py --print_all | grep <model_name>
```

To run the Image Classification Sample and Security Barrier Camera Demo application you need to download the following models:

|Model Name                                     | Code Sample or Demo App                             |
|-----------------------------------------------|-----------------------------------------------------|
|`squeezenet1.1`                                | Image Classification Sample                         |
|`vehicle-license-plate-detection-barrier-0106` | Security Barrier Camera Demo application            |
|`vehicle-attributes-recognition-barrier-0039`  | Security Barrier Camera Demo application            |
|`license-plate-recognition-barrier-0001`       | Security Barrier Camera Demo application            |

To download the models to a models folder (referred to as `<models_dir>` below) with the Model Downloader, run the following command:
```sh
sudo python3 ./downloader.py --name <model_name> --output_dir <models_dir>
```
> **NOTE:** Always run the downloader with `sudo`.

<details>
    <summary><strong>Example of Downloading the SqueezeNet Caffe* Model</strong></summary>

For example, to download the SqueezeNet 1.1 Caffe* model to the `~/models` folder, run:

```sh
sudo python3 ./downloader.py --name squeezenet1.1 --output_dir ~/models
```

When the model files are successfully downloaded, output similar to the following is printed:
```
###############|| Downloading topologies ||###############

========= Downloading /home/username/models/public/squeezenet1.1/squeezenet1.1.prototxt

========= Downloading /home/username/models/public/squeezenet1.1/squeezenet1.1.caffemodel
... 100%, 4834 KB, 3157 KB/s, 1 seconds passed

###############|| Post processing ||###############

========= Changing input dimensions in squeezenet1.1.prototxt =========
```
</details>

### <a name="convert-models-to-intermediate-representation"></a> Convert Models to Intermediate Representation

Once you have the trained model, you should convert it to the Intermediate Representation format using Model Optimizer before loading to Inference Engine. 

Models in the Intermediate Representation format always include an `.xml` and `.bin` files, and may also require other files, like `.json`, `.mapping`, etc.  Verify that you have all of the needed files. They should be in the same directory for the Inference Engine to find them.
- `model_name.xml`
- `model_name.bin`
- **OPTIONAL:** `model_name.json`, `model_name.mapping`, etc.

This guide uses the public SqueezeNet 1.1 Caffe\* model to run the Image Classification Sample. See the Example of Downloading a Model in the <a href="#download-models">Download Models</a> section above to learn how to download this model.

> **NOTE:** The `squeezenet1.1` model is downloaded in the Caffe* format and requires converting to IR.  `vehicle-license-plate-detection-barrier-0106`, `vehicle-attributes-recognition-barrier-0039`, `license-plate-recognition-barrier-0001` models in this guide are downloaded in Intermediate Representation.

1. Create an `<ir_dir>` directory that will contain the Intermediate Representation (IR) of the model. 

2. Inference Engine can perform inference of different precision formats, such as `FP32`, `FP16`, `INT8`. To prepare an IR with specific precision, run the Model Optimizer with the appropriate `--data_type` option.

3. Run the Model Optimizer script:
   ```sh
   cd /opt/intel/openvino/deployment_tools/model_optimizer
   ```
   ```sh  
   python3 ./mo.py --input_model <model_dir>/<model_file> --data_type <model_precision> --output_dir <ir_dir>
   ```
4. After the Model Optimizer script is completed, the produced IR files are in the specified `<ir_dir>` directory.

<details>
    <summary><strong>Example of Converting the SqueezeNet Caffe* Model</strong></summary>

The following command converts the public SqueezeNet 1.1 Caffe\* model that you have downloaded in the previous section to the FP16 IR and save to the `~/models/public/squeezenet1.1/ir` output directory:

```sh
   cd /opt/intel/openvino/deployment_tools/model_optimizer
   ```
   ```sh  
   python3 ./mo.py --input_model ~/models/public/squeezenet1.1/squeezenet1.1.caffemodel --data_type FP16 --output_dir ~/models/public/squeezenet1.1/ir
   ```

After the Model Optimizer script is completed, the produced IR files (`squeezenet1.1.xml`, `squeezenet1.1.bin`) are in the specified `~/models/public/squeezenet1.1/ir` directory.

Copy the `squeezenet1.1.labels` file from the `/opt/intel/openvino/inference-engine/samples/sample_data/` folder to the model IR directory `<ir_dir>`. This file contains the classes that ImageNet uses so that the inference results show text instead of classification numbers:
   ```sh   
   cp /opt/intel/openvino/inference-engine/samples/sample_data/squeezenet1.1.labels <ir_dir>
   ```
</details>

### <a name="download-media"></a> Download Media

There are multiple sources for downloading video media for use with code samples and demo applications. Possibilities include: 
- https://videos.pexels.com
- https://images.google.com

Intel® Distribution of OpenVINO™ toolkit installation includes two sample images that you can use for running code samples and demo applications:
* `/opt/intel/openvino/deployment_tools/demo/car.png`
* `/opt/intel/openvino/deployment_tools/demo/car_1.bmp`

### Run Image Classification Code Sample

To run the **Image Classification** code sample with an input image on the prepared IR: 

1. Setup the OpenVINO environment variables:
   ```sh
   source /opt/intel/openvino/bin/setupvars.sh
   ``` 
2. Go to the code samples build directory:
   ```sh
   cd ~/inference_engine_cpp_samples_build/intel64/Release
   ```
3. Run the code sample executable with specifying the input media file, the IR of your model and a target device to perform inference on:
   ```sh
   classification_sample -i <path_to_media> -m <path_to_model> -d <target_device>
   ```
<details>
    <summary><strong>Examples of Running Image Classification Code Sample on Different Devices</strong></summary>

The following commands run Image Classification Code Sample with the `car.png` file from the `/opt/intel/openvino/deployment_tools/demo/` directory as an input image, the IR of your model from `~/models/public/squeezenet1.1/ir` and on different hardware devices to perform inference on:

**For CPU:**
   ```sh
   ./classification_sample -i /opt/intel/openvino/deployment_tools/demo/car.png -m ~/models/public/squeezenet1.1/ir/squeezenet1.1.xml -d CPU
   ```

   **For GPU:**
   >**NOTE**: Running inference on Intel® Processor Graphics (GPU) requires 
   performing [additional hardware configuration steps](https://docs.openvinotoolkit.org/latest/_docs_install_guides_installing_openvino_linux.html#additional-GPU-steps).
   ```sh
   ./classification_sample -i /opt/intel/openvino/deployment_tools/demo/car.png -m ~/models/public/squeezenet1.1/ir/squeezenet1.1.xml -d GPU
   ```
   
   **For MYRIAD:** 

   >**NOTE**: Running inference on VPU devices (Intel® Movidius™ Neural Compute 
   Stick or Intel® Neural Compute Stick 2) with the MYRIAD plugin requires 
   performing [additional hardware configuration steps](inference-engine/README.md#optional-additional-installation-steps-for-the-intel-movidius-neural-compute-stick-and-neural-compute-stick-2).
   ```sh   
   ./classification_sample -i /opt/intel/openvino/deployment_tools/demo/car.png -m ~/models/public/squeezenet1.1/ir/squeezenet1.1.xml -d MYRIAD
   ```

When the Sample Application completes, you will have the label and confidence for the top-10 categories printed on the screen. Below is a sample output with inference results on CPU:    
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

### Run Security Barrier Camera Demo Application

To run the **Security Barrier Camera Demo Application** with an input image on the prepared IRs:

1. Setup the OpenVINO environment variables:
   ```sh
   source /opt/intel/openvino/bin/setupvars.sh
   ``` 
2. Go to the demo applications build directory:
   ```sh
   cd ~/inference_engine_demos_build/intel64/Release
   ```
3. Run the demo executable with specifying the input media file, list of model IRs and a target device to perform inference on:
   ```sh
   ./security_barrier_camera_demo -i <path_to_media> -m <path_to_model>/vehicle-license-plate-detection-barrier-0106.xml -m_va <path_to_vehicle_attributes model>/vehicle-attributes-recognition-barrier-0039.xml -m_lpr <path_to_license_plate_recognition_model>/license-plate-recognition-barrier-0001.xml -d <target_device>
   ```

<details>
    <summary><strong>Examples of Running Security Barrier Camera Demo Application on Different Devices</strong></summary>


**For CPU:**
```sh
./security_barrier_camera_demo -i /opt/intel/openvino/deployment_tools/demo/car_1.bmp -m <path_to_model>/vehicle-license-plate-detection-barrier-0106.xml -m_va <path_to_model>/vehicle-attributes-recognition-barrier-0039.xml -m_lpr <path_to_model>/license-plate-recognition-barrier-0001.xml -d CPU
```

**For GPU:**
>**NOTE**: Running inference on Intel® Processor Graphics (GPU) requires 
   performing [additional hardware configuration steps](https://docs.openvinotoolkit.org/latest/_docs_install_guides_installing_openvino_linux.html#additional-GPU-steps).
```sh
./security_barrier_camera_demo -i /opt/intel/openvino/deployment_tools/demo/car_1.bmp -m <path_to_model>/vehicle-license-plate-detection-barrier-0106.xml -m_va <path_to_model>/vehicle-attributes-recognition-barrier-0039.xml -m_lpr <path_to_model>/license-plate-recognition-barrier-0001.xml -d GPU
```

**For MYRIAD:** 
   >**NOTE**: Running inference on VPU devices (Intel® Movidius™ Neural Compute 
   Stick or Intel® Neural Compute Stick 2) with the MYRIAD plugin requires 
   performing [additional hardware configuration steps](https://docs.openvinotoolkit.org/latest/_docs_install_guides_installing_openvino_linux.html#additional-NCS-steps).
   ```sh   
   ./classification_sample -i <DLDT_DIR>/inference-engine/samples/sample_data/car.png -m <ir_dir>/squeezenet1.1.xml -d MYRIAD
   ```

</details>

3. Run the sample executable 

   **For CPU:**
   ```sh
   ./classification_sample -i <DLDT_DIR>/inference-engine/samples/sample_data/car.png -m <ir_dir>/squeezenet1.1.xml -d CPU
   ```

   **For GPU:**
   ```sh
   ./classification_sample -i <DLDT_DIR>/inference-engine/samples/sample_data/car.png -m <ir_dir>/squeezenet1.1.xml -d GPU
   ```
   
   **For MYRIAD:** 

   >**NOTE**: Running inference on VPU devices (Intel® Movidius™ Neural Compute 
   Stick or Intel® Neural Compute Stick 2) with the MYRIAD plugin requires 
   performing [additional hardware configuration steps](inference-engine/README.md#optional-additional-installation-steps-for-the-intel-movidius-neural-compute-stick-and-neural-compute-stick-2).
   ```sh   
   ./classification_sample -i <DLDT_DIR>/inference-engine/samples/sample_data/car.png -m <ir_dir>/squeezenet1.1.xml -d MYRIAD
   ``` 

## <a name="basic-guidelines-sample-application"></a>Basic Guidelines for Using Code Samples and Demo Applications

Following are some basic guidelines for executing the OpenVINO™ workflow using the code samples and demo applications:

1. Before using OpenVINO™ samples, always setup the environment: 
```sh
source /opt/intel/openvino/bin/setupvars.sh
``` 
2. Have the directory pathways for the following:
- Code Sample binaries located in `~/inference_engine_cpp_samples_build/intel64/Release`
- Demo Application binaries located in `~/inference_engine_demos_build/intel64/Release`
- Media: Video or image. See <a href="download-media">Download Media</a>.
- Model: Neural Network topology converted with the Model Optimizer to the IR format (.bin and .xml files). See <a href="download-models">Download Models</a> for more information.

## <a name="syntax-examples"></a> Typical Code Sample and Demo Application Syntax Examples

Here is a template for calling a sample code or demo application:

`<path_to_app> -i <path_to_media> -m <path_to_model> -d <target_device>`

With sample information filled in, the command might look like this:

`./object_detection_demo_ssd_async -i ~/Videos/catshow.mp4 \
-m ~/ir/fp32/mobilenet-ssd.xml -d CPU`

## <a name="advanced-samples"></a> Advanced Use of Demos 

Some demo applications allow the use of multiple models for different purposes. Usually for these cases, the output of the first model is used as the input of later models.

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
