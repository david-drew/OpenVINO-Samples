
# Get Started with OpenVINO™ Samples for Linux*

For an overview and details about OpenVINO, see the [OpenVINO Overview](OpenVINO_Overview.md)

Please refer to the above guide as needed.   

<br>

NOTE: When using OpenVINO from the command line, you must setup your environment whenever you change users or launch a new terminal.

    source /opt/intel/openvino/bin/setupvars.sh

<details>
    <summary>Run a Sample Application</summary>
    
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

</details>

## <a name="Exercises"></a> Exercises

The following series of exercises guide you through using samples of increasing complexity. As you move through each exercise you will get a sense of how to use OpenVINO in more sophisticated use cases. 

**NOTE:** Before starting these sample exercises, change directories into the samples directory:

`cd ~/omz_demos_build/intel64/Release`

**NOTE:** During this exercise you will be asked to move to multiple different directories, and occasionally to copy files (so that you don't have to specify full paths in commands).  You are welcome to set up environment variables to make these tasks easier, but we leave that to you.


<details>
    <summary>Exercise 1: Human Pose Estimation</summary>

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

</details>

<details>
    <summary>Exercise 2: Interactive Face Detection</summary>

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

</details>

<details>
    <summary>Exercise 3: Object Detection</summary>

**Exercise 3: Object Detection (Vehicles with multiple models)**

As mentioned in the previous exercise, it's possible to chain models together and use the output of an initial object detection model as input of additional models.

The following exercise expects the use of the `security_barrier_camera_demo`.

Steps to complete:
1. Find and download a model that supports vehicle detection.
2. Find and download a video that contains cars to be detected.
3. Run the `security_barrier_camera_demo` and check that vehicles are detected.
4. Choose at least one additional model (such as attribute recognition), and run the application, now using the initial model plus the new one.
5. OPTIONAL: Run the demo with vehicle detection (primary), plus attribute recognition and license plate recognition.

</details>

<details>
    <summary>Exercise 4: Object Detection (YOLO v3)</summary>

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
