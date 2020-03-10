
# Get Started with OpenVINO™ Deep Learning Workbench™ and Samples for Linux*

For an overview and details about the Intel® Distribution of OpenVINO™ toolkit, see the [OpenVINO™ Overview](OpenVINO_Overview.md)

We recommend you read the above OpenVINO™ Overview before starting this tutorial.   

<br>

NOTE: When using OpenVINO™ from the command line, you must setup your environment whenever you change users or launch a new terminal.

    source /opt/intel/openvino/bin/setupvars.sh

## <a name="Setup"></a> Setup
For using the Model Optimizer from the command line, only OpenVINO is required.  For using the Deep Learning Workbench, there is an additional requirement for docker and an initial configuration step for the Workbench.


### Build Samples
If samples have been manually build, skip this section.  This step will take about 5 - 10 minutes, depending on your system.

Build OpenVINO Demos

```
cd /opt/intel/openvino/inference_engine/demos
./build_demos.sh
```
Build OpenVINO Samples
```
cd /opt/intel/openvino/inference_engine/demos
./build_samples.sh
```


### Setup the DL Workbench
This step will take about 15-20 minutes to complete.<br><br>

Move to the workbench directory.<br>
`cd /opt/intel/openvino/deployment_tools/tools/workbench`

Example Use:<br>
run_workbench -PACKAGE_PATH \<openvino archive path\>

Use:<br>
`./run_workbench -PACKAGE_PATH ~/Downloads/l_openvino_toolkit_2020.tar.gz`

After setup is complete, use a web browser to access the workbench:<br>
https://127.0.0.1:5665

After the first time setup, future execution will bypass setup and immediately launch the DL workbench.


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

1. Setup the environment variables when logging in, changing users, or launching a new terminal. (Detail above.)

2. Make a destination directory for the FP32 SqueezeNet* Model:

    `mkdir ~/squeezenet1.1_FP32`
    
    `cd ~/squeezenet1.1_FP32`
    
**Convert and Optimize a Neural Network Model from Caffe* **

Use the Model Optimizer to convert an FP32 SqueezeNet* Caffe* model into an optimized Intermediate Representation (IR):

    `python3 /opt/intel/openvino/deployment_tools/model_optimizer/mo.py --input_model ~/Desktop/Data/models/Caffe/squeezenet1.1/squeezenet1.1.caffemodel --data_type FP32 --output_dir .`    

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

    `cd  ~/inference_engine_samples_build/intel64/Release`

    `./classification_sample_async -i car.png -m ~/squeezenet1.1_FP32/squeezenet1.1.xml -d CPU`

2. **Note:** You can usually see an application's help information (parameters, etc.) by using `-h`.

    `./classification_sample -h`

</details>


<details>
   <summary>Exercise 2 - Using the DL Workbench</summary>
    
### <a name="run-sample-application"></a> Exercise 2 - Run A Sample Application with the DL Workbench™

Convert a model using the DL Workbench™, then use a sample application to load the model and run inference.

**Note:** If you are running inference only on a CPU, you already have the required FP32 model. If you want to run inference on any hardware, you'll need an FP16 version of the model.

In this section, you will (again) convert an FP32 model for running on a CPU.

1.  Return to the previously indicated FP32 SqueezeNet* directory:

    `cd ~/squeezenet1.1_FP32`

2.  Use the DL Workbench to convert the FP32 SqueezeNet* Caffe* model into an FP16 optimized Intermediate Representation (IR).

    1. In a web browser, launch the DL Workbench™:
        - http://127.0.0.1:5665/
    
    2. Select **Get Started** to show the setup interface.
    
    3. The setup interface is divided into two sections.
        - The top section is for loading models.
        - The bottom section is for loading data sets (for example, images that will be used by the model during inference).
        
     4. Select the **Original Model** tab. The default tab is for downloading models from the OpenVINO™ Model Zoo.
     
     5. Select **Framework** -> **Caffe**
     
     6. Choose the prototxt file with the first **Choose File** button.
        - ~/Desktop/Data/models/Caffe/squeezenet1.1/squeezenet1.1.protoxt
        
     7. Choose the caffemodel file with the second **Choose File** button.
        - ~/Desktop/Data/models/Caffe/squeezenet1.1/squeezenet1.1.caffemodel
        
     8. The **Model Name** will be automatically populated, but change it if desired.  
        - This can be useful when tracking multiple loaded models
        
     9. Select **Import Model**.  
        - This will create and FP16 model by default, though configuration settings may be edited later.
        
    10. In some cases this will work and you will return to the setup interface.  If the DL Workbench™ is unable to determine required information, it will display a configuration screen and specify the information required.
        - If you built the model, you will know all of its details, but if you downloaded the model from a third party, you may have to do some searching for the required information.
        
     11. The **Import Model** screen is being displayed, because additional information must be provided by the user.
     
     12. The requirements are specified in the grey box on the right.
     
     13. In this example, the only missing information is the colorspace.  Click on the box next to **Original Color Space** and select **BGR**.
         - BGR is often used when training models, rather than RGB and other color formats.
         
     14. Note that the grey box no longer contains any red warning text.
     
     15. Select **Convert** and you will return to the setup interface.  Model conversion may take several minutes.

3. Import a Dataset into the DL Workbench™:
    1. We could autogenerate a set of simulated images, but for this task we'll select a subset of the ImageNet* dataset.
        - **Note:** results will often be better using real images.
    2. Select **Import Local Dataset**
    3. Select **Choose File** and browse to "~/Desktop/Workbench/Data/Imagenet_200_224x224.zip"
    4. Select **Import Dataset**
        - **Note:** The height and width of images in the dataset must satisfy the requirements of the target model.
        
4. Run Inference with the DL Workbench:
    1. Select the **model** from the top of the interface.
    2. Select the **dataset** from the bottom of the interface.
    3. Select the **Environment** (target hardware).
    4. All items in the status box next to the **Environment** box should now have green checks.
    5. Select **Go**.
        - This will take a few minutes

5. Normally, we would encourage analysis and optimization of the model, but for this exercise, we'll just export the model from the DL Workbench to our system.  
    1. Take a few minutes to look at the output if desired.
    2. Find the **download** icon at the right, in the top squeezenet model bar.
    3. Select **Download**.  This will drop a `tar.gz` archive in your default downloads directly (`~/Downloads`).
    4. `cd ~/Downloads`
    5. `mkdir squeezenet`
    6. `mv squeeze*gz squeezenet`
    7. `cd squeezenet`
    8. `tar zxvf squeezenet1.1.tar.gz`

6.  The `squeezenet1.1.labels` file contains the classes that ImageNet* uses. This file is included so that the inference results show text as well as classification percentages. Copy `squeezenet1.1.labels` to your optimized model location:

    `cp ~/openvino_models/ir/public/squeezenet1.1/FP16/squeezenet1.1.labels .`

7.  Copy a sample image to the release directory. You will use this with your optimized model:

    `cd  ~/inference_engine_samples_build/intel64/Release`

    `sudo cp /opt/intel/openvino/deployment_tools/demo/car.png  .`

8. Once your setup is complete, you're ready to run a sample application:

    `./classification_sample_async -i car.png -m ~/Downloads/squeezenet1.1/squeezenet1.1.xml -d CPU`

9. **Note:** You can usually see an application's help information (parameters, etc.) by using `-h`.

    `./classification_sample_async -h`

</details>


## Additional Samples for Linux* using Model Optimizer Command-Line

NOTE: When using OpenVINO™ from the command line, you must setup your environment whenever you change users or launch a new terminal.

    source /opt/intel/openvino/bin/setupvars.sh

<details>
    <summary>Exercise 3: Human Pose Estimation</summary>

**Exercise 3: Human Pose Estimation**

This demo detects people and draws a stick figure to show limb positions. This model has already been converted for use with the Intel® Distribution of OpenVINO™ toolkit.

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

**Note:** `list_topologies.yml` is a text (YAML) file with a list and details about every model available in the Intel® Model Zoo.  Models can also be manually downloaded from the github page.

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
    <summary>Exercise 4: Interactive Face Detection</summary>

**Exercise 4: Interactive Face Detection**

The face detection demo draws bounding boxes around faces, and optionally feeds the output of the primary model to additional models. This model has already been converted for use with OpenVINO™.

The Face Detection Demo supports face detection, plus optional functions:

- Age-gender recognition
- Emotion recognition
- Head pose
- Facial landmark display

Syntax:

`./interactive_face_detection -i path/to/video -m path/to/face/model -d CPU`

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
    <summary>Exercise 5: Object Detection</summary>

**Exercise 5: Object Detection (Vehicles with multiple models)**

As mentioned in the previous exercise, it's possible to chain models together and use the output of an initial object detection model as input for additional models.

The following exercise uses the `security_barrier_camera_demo`.

Steps:
1. Find and download a model that supports vehicle detection.
2. Find and download a video that contains cars to be detected.
3. Run the `security_barrier_camera_demo` and check that vehicles are detected.
4. Choose at least one additional model (such as attribute recognition), and run the application, now using the initial model plus the new one.
5. **OPTIONAL:** Run the demo with vehicle detection as the primary, plus attribute recognition and license plate recognition.

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
