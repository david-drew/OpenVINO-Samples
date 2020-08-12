
# Get Started with OpenVINO™ Deep Learning Workbench™ and Samples for Linux*

For an overview and details about the Intel® Distribution of OpenVINO™ toolkit, see the [OpenVINO™ Overview](OpenVINO_Overview.md)

We recommend you read the above OpenVINO™ Overview before starting this tutorial.   

You should complete the [OpenVINO™ Get Started Guide](https://docs.openvinotoolkit.org/latest/_docs_get_started_get_started_linux.html) before proceeding with this document.

<br>

NOTE: When using OpenVINO™ from the command line, you must setup your environment whenever you change users or launch a new terminal.

    source /opt/intel/openvino/bin/setupvars.sh

## <a name="Setup"></a> Setup
For using the Model Optimizer from the command line, only OpenVINO is required.  For using the Deep Learning Workbench, there is an additional requirement for docker and an initial configuration step for the Workbench.


### Build Samples
If samples have been manually built, skip this section.  This step will take about 5-10 minutes, depending on your system.

Build OpenVINO Demos

```
cd /opt/intel/openvino/inference_engine/demos
./build_demos.sh
```
Build OpenVINO Samples
```
cd /opt/intel/openvino/inference_engine/samples/cpp
./build_samples.sh
```

# Convert the Model with Model Optimizer

In this step, your trained models are ready to run through the Model Optimizer to convert them to the Intermediate Representation format. This is required before using the Inference Engine with the model.

Models in the IR format always include an .xml and .bin file, and may also include other files, like .json, .mapping, or others. Make sure you have these files in the same directory for the Inference Engine to find them.

REQUIRED: model_name.xml
REQUIRED: model_name.bin
OPTIONAL: model_name.json, model_name.mapping, etc.

This guide uses the public SqueezeNet 1.1 Caffe* model to run the Image Classification Sample. See the example to download a model in the Download Models section to learn how to download this model.

The squeezenet1.1 model is downloaded in the Caffe* format. You must use the Model Optimizer to convert the model to the IR. The vehicle-license-plate-detection-barrier-0106, vehicle-attributes-recognition-barrier-0039, license-plate-recognition-barrier-0001 models are downloaded in the Intermediate Representation format. You don't need to use the Model Optimizer to covert these models.

Create an <ir_dir> directory to contain the model's Intermediate Representation (IR).

```
mkdir ~/ir
```

The Inference Engine can perform inference on different precision formats, such as FP32, FP16, INT8. To prepare an IR with specific precision, run the Model Optimizer with the appropriate --data_type option.

Run the Model Optimizer script:

```
cd /opt/intel/openvino/deployment_tools/model_optimizer
```

python3 ./mo.py --input_model <model_dir>/<model_file> --data_type <model_precision> --output_dir <ir_dir>
The produced IR files are in the <ir_dir> directory.

The actual command:
```
python3 ./mo.py --input_model ~/openvino_models/models/public/squeezenet1.1/squeezenet1.1.caffemodel --data_type FP32 --output_dir ~/ir
```

## <a name="Exercises"></a> Exercises

The following series of exercises guide you through using samples of increasing complexity. As you move through each exercise you will get a sense of how to use OpenVINO™ in more sophisticated use cases. 

In these exercises, you will:

1. Convert and optimize a neural network model to work on Intel® hardware.
	
2. Run computer vision applications using optimized models and appropriate media.

    - During optimization with the DL Workbench™, a subset of ImageNet* and VOC* images are used.
    - When running samples, we'll use either an image or video file located on this system.

**NOTE:** Before starting these sample exercises, change directories into the samples directory:

`cd ~/omz_demos_build/intel64/Release`

**NOTE:** During this exercise you will move to multiple directories and occasionally copy files so that you don't have to specify full paths in commands.  You are welcome to set up environment variables to make these tasks easier, but we leave that to you.

<details>
   <summary>Exercise 1 - Run A Sample Application</summary>
    
### <a name="run-sample-application"></a> Exercise 1 - Run A Sample Application 

Convert a model using the Model Optimizer then use a sample application to load the model and run inference.

In this section, you will convert an FP32 model suitable for running on a CPU.

**Prepare the Software Environment**

1. Set up the environment variables when logging in, changing users, or launching a new terminal. (Detail above.)

2. Make a destination directory for the FP32 SqueezeNet* Model:

    `mkdir ~/squeezenet1.1_FP32`
    
    `cd ~/squeezenet1.1_FP32`
    
**Convert and Optimize a Neural Network Model from Caffe* **

Use the Model Optimizer to convert an FP32 SqueezeNet* Caffe* model into an optimized Intermediate Representation (IR):

```
python3 /opt/intel/openvino/deployment_tools/model_optimizer/mo.py --input_model ~/openvino_models/models/public/squeezenet1.1/squeezenet1.1.caffemodel --data_type FP32 --output_dir .
```

**Prepare the Data (Media) or Dataset**

NOTE: In this case, it's a single image.

1.  Copy the labels file to the same location as the IR model.

    `cp ~/openvino_models/ir/public/squeezenet1.1/FP16/squeezenet1.1.labels .`
    
    - Tip: The labels file contains the classes used by this SqueezeNet* model. 
    - If it's is in the same directory as the model, the inference results will show text in addition to confidence percentages.

2.  Copy a sample image to the release directory. You will use this with your optimized model:

    `sudo cp /opt/intel/openvino/deployment_tools/demo/car.png  .`

**Run the Sample Application**

1. Once your setup is complete, you're ready to run a sample application:

     `~/inference_engine_samples_build/intel64/Release/classification_sample_async -i car.png -m ~/squeezenet1.1_FP32/squeezenet1.1.xml -d CPU`

2. **Note:** You can usually see an application's help information (parameters, etc.) by using `-h`.

    `~/inference_engine_samples_build/intel64/Release/classification_sample_async -h`

</details>

## Additional Samples for Linux* using Model Optimizer Command Line

NOTE: When using OpenVINO™ from the command line, you must set up your environment whenever you change users or launch a new terminal.

    source /opt/intel/openvino/bin/setupvars.sh

<details>
    <summary>Exercise 2: Human Pose Estimation</summary>

This demo detects people and draws a stick figure to show limb positions. This model has already been converted for use with the Intel® Distribution of OpenVINO™ toolkit.

- Requires downloading the human-pose-estimation-0001 (ICV) Model.
- Requires video or camera input.

Example Syntax:

- human_pose_estimation_demo -i path/to/video -m path/to/model/human-pose-estimation-0001.xml -d CPU

**Steps to Run the Human Pose Demo:**

1. Set up the environment variables:

    `source /opt/intel/openvino/bin/setupvars.sh`

2. Move to the Model Downloader Directory:

    `cd /opt/intel/openvino/deployment_tools/tools/model_downloader/`

3. Find a suitable model:

    `python3 info_dumper.py --print_all |grep pose`

**Note:** `info_dumper.py` is a script that can list details about every model available in the Intel® Model Zoo. Models can also be manually downloaded from the Open Model Zoo GitHub page.

4. Download the model:

    `sudo ./downloader.py --name human-pose*`

5. Move the model to a more convenient location:

    `mkdir ~/ir`

    `cp /opt/intel/openvino/deployment_tools/tools/model_downloader/intel/human-pose-estimation-0001/FP32/human-pose-estimation-0001* ~/ir/`

6. Download an appropriate video:

    Open a web browser to the following URL and download the video: 
https://www.pexels.com/video/couple-dancing-on-sunset-background-2035509/

    Rename the video for convenience:

    `mv ~/Downloads/Pexels\ Videos\ 2035509.mp4 ~/Videos/humpose.mp4`

7. Run the sample:

    `cd ~/omz_demos_build/intel64/Release/`

    `./human_pose_estimation_demo -i ~/Videos/humpose.mp4 -m ~/ir/human-pose-estimation-0001.xml -d CPU`

</details>

<details>
    <summary>Exercise 3: Interactive Face Detection</summary>

The face detection demo draws bounding boxes around faces, and optionally feeds the output of the primary model to additional models. This model has already been converted for use with OpenVINO™.

The Face Detection Demo supports face detection, plus optional functions:

- Age-gender recognition
- Emotion recognition
- Head pose
- Facial landmark display

Example Syntax:
- interactive_face_detection_demo -i path/to/video -m path/to/face/model -d CPU

Steps:

1.	Find and download an appropriate face detection model.  There are several available in the Intel® Model Zoo.
    - You can access the [Pretrained Models](https://software.intel.com/en-us/openvino-toolkit/documentation/pretrained-models) page from the OpenVINO™ documentation to review model options.
    - You may need to try out different models to find one that works, or that works best for your scenario.
2.	Find and download a video that features faces.
3.	Run the demo with just the face detection model.
4.	**OPTIONAL:** Run the demo using additional models (age-gender, emotion recognition, head pose, etc.).
    Note that when you use multiple models, there is always a primary model that is used followed by a number of optional models that use the output from the initial model.

</details>

<details>
    <summary>Exercise 4: Brain Tumor Segmentation</summary>

For this next exercise, we'll redirect you to another page.  Please return here after completion.

[Brain Tumor Segmentation Sample](https://github.com/david-drew/OpenVINO-Samples/tree/master/brain_tumor_segmentation/README.md)

</details>

<details>
	<summary>Exercise 5: Speech Recognition</summary>
For this exercise, we'll redirect you to the 

For more information about how OpenVINO works with audio, review [this page](https://docs.openvinotoolkit.org/latest/openvino_inference_engine_samples_speech_libs_and_demos_Speech_libs_and_demos.html).
Then read through the documentation for the [Speech Recognition Demo](https://docs.openvinotoolkit.org/latest/openvino_inference_engine_samples_speech_libs_and_demos_Live_speech_recognition_demo.html).

To run the demo:
1. Download the gettysburg address from [here](https://www2.cs.uic.edu/~i101/SoundFiles/gettysburg.wav).
2. Launch the demo, point it at the wav file, and execute it. 


</details>

<details>
	<summary>Exercise 6: DL Streamer</summary>
	
The DL Streamer is a command-line tool and API for integrating OpenVINO into a media analytics pipeline.  It supports OpenVINO, GStreamer, Mosquitto, Kafka, and a variety of other technologies.

Follow the link below, read through the documentation, then do the tutorial.
	
[DL Streamer Documentation and Tutorial](DL_Streamer/README.md)
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

