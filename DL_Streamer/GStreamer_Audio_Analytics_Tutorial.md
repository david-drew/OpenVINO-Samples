# GStreamer Audio Analytics Tutorial

This tutorial provides four exercises:

1. Build a video analytics pipeline with the GStreamer* gst-launch tool and GStreamer Video Analytics (GVA) plug-in. 
2. Run an audio classification pipeline. 
3. Publish the inference results to a `.json` file.
3. Put together a custom pipeline using multiple models for a threat detection scenario. 

Before beginning the exercises, you must download the models and videos that the tutorials use. You should also read the information about the elements used in a GAA pipeline to help you understand the elements and options the exercises use.

### Prepare the System (Download Media, Install Requirements)

<details>
	<summary>Prepare the System</summary>
<br>

NOTE: The recommended way to install DL Streamer is usually through OpenVINO.  DL Streamer is included with OpenVINO 2020.r2 and newer.  If you have not installed OpenVINO, refer to the [DL Streamer Install Guide](Install_Guide.md).  For these exercises, the DL Streamer components will be deleted from the OpenVINO install, and the newer github code will be used so that we may use the audio featurees.

#### Setup DL Streamer with Audio Components

Remove OpenVINO DL Streamer Components

```sh
cd /opt/intel/openvino/deployment_tools
```

*NOTE:* Deleting the DL Streamer components from OpenVINO is a temporary step.  We're going to build pre-release DL Streamer code with audio support for these exercises.  In the future, this code will be merged into OpenVINO, and this workaround won't be necessary.
```sh
rm -rf dl_streamer
```

Install DL Streamer with Audio Support	

```sh
cd ~
```
```sh
mkdir gva && cd gva
```
```sh
git clone https://github.com/opencv/gst-video-analytics.git
```
Using the preview/audio-detect branch is required because the changes haven't been merged to master yet.

```sh
cd ~/gva/gst-video-analytics
```

```sh
git checkout preview/audio-detect
```

The DL Streamer Audio directory is now located at:
~/gva/gst-video-analytics/samples/gst_launch/audio_detect/


Now we'll do some additional setup for the samples in this repository.

```sh
cd ~/gva/gst-video-analytics/scripts
```
```sh
sudo ./install_dependencies.sh
```

```sh
cd ~/gva/gst-video-analytics/samples/gst_launch/audio_detect/
```

The OpenVINO environment must be active to use the model download script.
```sh
source /opt/intel/openvino/bin/setupvars.sh 
```

```sh
sudo -EH ./download_audio_models.sh 
```


### Install Requirements
```sh
sudo apt update && sudo apt install -y --no-install-recommends \
	wget cpio cmake lsb-release mesa-utils gdb mc ocl-icd-libopencl1 clinfo vainfo
```

### Get the Audio Files for the Examples<a name="acquire-data-and-sources"></a>

The DL Streamer plug-in uses the OpenVINO Deep Learning [Inference Engine](https://software.intel.com/en-us/articles/OpenVINO-InferEngine) to perform inference. As input, the Inference Engine accepts CNN models that are converted to the Intermediate Representation (IR) format through the OpenVINO toolkit [Model Optimizer](https://docs.openvinotoolkit.org/latest/_docs_MO_DG_Deep_Learning_Model_Optimizer_DevGuide.html). 

You can either train your own CNN models and convert them to the IR format or use free models from the [OpenVINO Model Zoo](https://github.com/opencv/open_model_zoo) that are already in the IR format. A tool named [Model Downloader](https://docs.openvinotoolkit.org/2020.1/_tools_downloader_README.html) gives you a way to easily download models from the Model Zoo. 

The steps below use a quick way to get everything you need to use the sample applications. For instructions to use your own models, download models from the Model Zoo and use the Model Downloader, see LINK TO OPENVINO CONTENT. 

Remember to source your environment:
   ```sh
   source /opt/intel/openvino/bin/setupvars.sh
   source ~/gva/gst-video-analytics/scripts/setup_env.sh
   ```

1. Create directories for the models and videos. The following is an example. If you use a different structure, remember to change the path in the instructions to match your location:
   ```sh
   mkdir -p ~/gva/models
   mkdir -p ~/gva/media
   ```

2.	Set the path to store the models we download.
    ```sh	
    export MODELS_PATH=~/gva/models
    ```
   
3. Download the model:

This is a proprietary model developed at Intel to categorize ambient noise, such as barking, footsteps, etc.

```sh
cd ~/gva/models
```
	
```sh
wget https://download.01.org/opencv/models_contrib/sound_classification/aclnet/pytorch/15062020/aclnet_des_53_fp32.onnx
```

4. The samples require audio files that:

- Are in 16-bit wav format. You can convert other formats to wav with ffmpeg and similar tools.
- Are relatively short (a few minutes long), for convenience. 

```sh
cd ~/gva/media
wget https://www2.cs.uic.edu/~i101/SoundFiles/gettysburg.wav
```
	
5. Download audio and videos

- You can download freely licensed audio from the websites like [FreeSound](https://freesound.org/browse/).
- Put your audio files in `~/gva/media`.


</details>

### Compile the New Audio Inference Libraries
<details>
	<summary>Compile the New Audio Inference Libraries</summary>
		
<br>
Eventually the updated code for audio support will be included in OpenVINO, but for now the DL Streamer libraries must be compiled from the source code in the gva archive (in the preview/audiot-detect branch).

<br>
<br>


* The following instructions are taken from the [DL Streamer Install Guide](https://github.com/opencv/gst-video-analytics/wiki/Install-Guide).
* Make sure that the preview/audio-detect branch is checked out.
* For ease of use, continue to use the repository checked out at `~/gva`.


1. Install dependencies

```sh
sudo apt update && sudo apt install -y --no-install-recommends \
       wget cpio cmake lsb-release gcc g++ libpython3-dev \
       make mesa-utils ocl-icd-libopencl1 clinfo vainfo 
```

2. Check that an OpenCL accelerator is available.

```sh
clinfo
```

    If output says 'Number of platforms: 0', install the Intel Neo GPU driver:
<details>
        <summary>Install Intel Neo GPU Driver</summary>

	```sh
	cd /opt/intel/openvino/install_dependencies/
	```

	 ```sh
	sudo -E ./install_NEO_OCL_driver.sh
	```

	Remember to source the environment after updating the driver.

	```sh
	source /opt/intel/openvino/bin/setupvars.sh
	```
</details>


3. Install Python Requirements

```sh
cd ~/gva/gst-video-analytics
```
```sh
pip3 install -r requirements.txt
```


4. Verify Install Status Again

```sh
clinfo
```
```sh
vainfo
```

If 'clinfo' works but 'vainfo' returns an error message, try rebooting the system.


5. Build the DL Streamer libraries.

```sh
mkdir ~/gva/gst-video-analytics/build
```
```sh
cd ~/gva/gst-video-analytics/build
```
```sh
cmake ..
```
```sh
make -j$(nproc)
```
```sh
sudo make install
```

Export environment variables
```sh
export GST_PLUGIN_PATH=~/gva/gst-video-analytics/build/intel64/Release/lib:$GST_PLUGIN_PATH  # for GVA elements to be available system-wid
```

</details>

### Learn about the Video and Audio Analytics Pipeline and the GAA Elements <a name="gva-pipeline"></a> 

<details>
	<summary>Learn about the Video and Audio Analytics Pipeline and the GAA Elements</summary>

<br>
This section focuses on video, but the process for both video and audio is very similar, the only difference being a few additional audio elements that are detailed in the next section.
<br>
The diagram below shows the data flow of a typical video/audio analytics pipeline.

![Typical GAA pipeline](typical_pipeline.png)

This is what you're seeing in the data flow:

1. Read File with `filesrc` - The `filesrc` element reads data from a file or camera.

2. Decode with `decodebin` -  The `decodebin` element selects the decoder according to the input format. Hardware decoding plug-ins have priority over CPU decoders. Use `decodebin` to insert video processing elements for color conversion or for video-to-system memory copying. <br>
The pipeline moves the uncompressed video from `decodebin` forward in 'video/x-raw' or 'video/x-raw(memory:VASurface)' data type. 'video/x-raw' is a system memory frame and 'video/x-raw(memory:VASurface)' is a video memory handle.

3. Detect Objects with `gvadetect` - The `gvadetect` element runs object detection inference on the decoded frame from `decodebin`. In this process, `gvadetect` uses the `model` parameter to perform inference on the model that you specify. As an option, you can add the `model-proc` parameter to configure the output. A list of regions is returned, known as Region of Interest (ROI), The ROI includes the detected object. 
4. Classify Objects with `gvaclassify` - The `gvaclassify` element performs inference on each video frame ROI. Inference uses the `model` parameter, and returns the result as key=value, such as age=40 or gender=female. Use the `model-proc` parameter with `gvaclassify` to provide a json file for interpreting the results and to configure the output layer name and labels. You can also use 'gvaclassify' to add filters to the inference, but only on by object class, such as a vehicle or pedestrian. 

5. Visualize with `gvawatermark` - The `gvawatermark` element adds the detection and classification results as an overlay on each frame. This element uses a `sync` property. Most samples set this as `sync=false` to disable real-time synchronization to increase the pipeline speed. You can change this to `sync=true` to run pipeline in  real-time speed.

6. Render Video with `xvimagesink` - The `xvimagesink` element renders the video frames. 

You can chain the `gvadetect`, `gvatrack`, `gvaclassify`, and `gvainference` inference elements. For example, you can have the following run in sequence: object detection, object tracking, and object classification. You can also add other GAA elements to use `gvametaconvert` and `gvametapublish` to publish the inference results. 

See [GAA elements](Elements) for more information about the GAA plug-in elements.

The following video shows the result of running a pipeline with:

- gst-launch-1.0 filesrc location=cut.mp4 ! decodebin ! gvadetect model=face-detection-adas-0001.xml ! gvaclassify model=emotions-recognition-retail-0003.xml model-proc=emotions-recognition-retail-0003.json ! gvawatermark ! xvimagesink sync=false

<div align="center"><img src="demo_pipeline.gif" width=900/></div>

The elements in this pipeline are:
* `filesrc` loads a video file named `cut.mp4`.
* `decodebin` decodes the video. 
* `gvadetect` runs inference on the video. The `model` named `face-detection-adas-0001` is used for inference to detect faces. 
* `gvaclassify` uses the result of `gvadetect` on a `model` named `emotions-recognition-retail-0003`, resulting in emotion classifications. 
* `gvawatermark` overlays the detection and classification results on each frame.
* `xvimagesink` renders the video frames.

You are ready to try creating your own pipeline. Continue with the next section to use the first exercise.

</details>


### Exercise 1: Build a Simple Pipeline <a name="simple-pipeline"></a>

<details>
	<summary>Build a Simple Video Pipeline</summary>
<br>

This exercise helps you create a GStreamer pipeline that uses specific models to run detection on an Intermediate Representation (IR) formatted model. In this exercise you run inference to detect people and vehicles in a video.  This exercise will introduce the general concepts and focus on video.  

This exercise introduces you to using the following GAA elements:

- `filesrc`
- `gvadetect` 
- `gvawatermark`
	
1. Set the environment variables if not already set.:

```sh
source /opt/intel/openvino/bin/setupvars.sh
source ~/gva/gst-video-analytics/scripts/setup_env.sh
```

2. Export the `model` and `model_proc` files:

```sh
export DETECTION_MODEL=~/gva/models/intel/person-vehicle-bike-detection-crossroad-0078/FP32/person-vehicle-bike-detection-crossroad-0078.xml
```
```sh
export DETECTION_MODEL_PROC=/opt/intel/openvino/data_processing/dl_streamer/samples/gst_launch/vehicle_pedestrian_tracking/model_proc/person-vehicle-bike-detection-crossroad-0078.json
```

3. Export the video file path:

This example uses ~/gva/video as the video path and <your_video> as the placeholder for a video file name. Change this information to fit your setup.

```sh
export VIDEO_EXAMPLE=~/gva/video/<your_video>
```

4. Create and run the pipeline. As an option, add the `model-proc` parameter to create a `model-proc` JSON file. `model-proc` files describe the model input and output layer format. The `model-proc` file in this exercise describes the output layer name and labels (person, vehicle, and bike) on objects it detects. 

See [model-proc](https://github.com/opencv/gst-video-analytics/blob/master/samples/model_proc/person-vehicle-bike-detection-crossroad-0078.json) for more information.

```sh
gst-launch-1.0 \
	filesrc location=${VIDEO_EXAMPLE} ! decodebin ! video/x-raw ! videoconvert ! \
	gvadetect model=${DETECTION_MODEL} model_proc=${DETECTION_MODEL_PROC} device=CPU ! queue ! \
	gvawatermark ! fpsdisplaysink video-sink=xvimagesink sync=false
```
	
5. Review the output. Persons, vehicles, and bikes are bound by colored boxes, and detection results are displayed as video overlays. The average frame rate of the pipeline are shown as overlays at the bottom of the video.

`gvadetect`:
	* Used the XML model file to find the BIN file for inference.
	* Performed detection on each video frame.
	* Output ROIs with labels according to `model-proc` specifications. 

`gvawatermark` used the output ROIs to visually display the detected objects and their attributes.
	* [`fpsdisplaysink`](https://gstreamer.freedesktop.org/documentation/debugutilsbad/fpsdisplaysink.html?gi-language=c) displayed the average FPS of the pipeline.
	* [`xvimagesink`](https://gstreamer.freedesktop.org/documentation/xvimagesink/index.html?gi-language=c#xvimagesink-page) rendered the video frames.

You're done building and running this pipeline. To expand on this exercise, use one or both add-ons to this exercise to select different video sources. If the add-ons don't suit you, jump ahead to start [Exercise 2](#classification-pipeline)

</details>


### Exercise 2: Run an Audio Classification Pipeline <a name="classification-pipeline"></a>

<details>
	<summary>Run an Classification Pipeline</summary>
<br>	

This exercise uses a script to categorize a variety of environmental sounds.  A 16-bit wav file is required as the input, ffmpeg or similar tools can convert other audio file types.  For this exercise we'll run the script, then open the script in a text editor to note differences.

```sh
cd ~/gva/gst-video-analytics/samples/gst_launch/audio_detect
```

Run the script, pointing it at the gettysburg speech file.
```sh
./audio_event_detection.sh ~/gva/media/gettysburg.wav
```

Note that the output is json, and their a variety of fields for timestamps, lables, and other information.  Also note that 'Speech' is being recognized.

Run the script, pointing it at the animal sounds file.  **DAVID: Share files and change TBD below**
```sh
./audio_event_detection.sh ~/gva/media/TBD.wav
```

Note that different animal types are being classified.

Open the audio_event_detection.sh script in the text editor of your choice:
```sh
gedit audio_event_detection.sh
```

Examine the file, and look at the environment variables that are set, as well as the gst-launch-1.0 command at the end.  If desired, modify the script to output the final gst-launch command.

A few elements new to the audio process:
* audioresample
* audioconvert
* audio/x-raw, channels=1,format=S16LE,rate=16000
* gvaaudiodetect

The gst-launch command may be modified to include other models or elements.

You're done with this pipeline. The next exercise shows you how to publish your results in a json format.
	
</details>

### Exercise 3: Publish Inference Results
<details>
	<summary>Publish Inference Results</summary>
<br>

This exercise extends the pipeline to publish your detection and classification results to a `.json` file from a GStreamer pipeline.

This exercises uses the following additional GAA elements:

- `gvametaconvert`
- `gvametapublish` 

The script for this exercise is in the [`metapublish`](https://github.com/opencv/gst-video-analytics/blob/master/samples/gst_launch/metapublish/) directory where the GAA plug-ins sample scripts are located. The `metapublish` directory also contains scripts to publish results to Kafka and MQTT.

1. Set the OpenVINO environment:

> NOTE: For this exercise, you must reset the env variables as described below.

```sh
source /opt/intel/openvino/data_processing/gstreamer/bin/gstreamer-setupvars.sh
source /opt/intel/openvino/bin/setupvars.sh
```

2. Export the `model` and `model_proc` files:
```sh
export DETECTION_MODEL=~/gva/models/intel/person-vehicle-bike-detection-crossroad-0078/FP32/person-vehicle-bike-detection-crossroad-0078.xml
export DETECTION_MODEL_PROC=/opt/intel/openvino/data_processing/dl_streamer/samples/gst_launch/vehicle_pedestrian_tracking/model_proc/person-vehicle-bike-detection-crossroad-0078.json
export VEHICLE_CLASSIFICATION_MODEL=~/gva/models/intel/vehicle-attributes-recognition-barrier-0039/FP32/vehicle-attributes-recognition-barrier-0039.xml
export VEHICLE_CLASSIFICATION_MODEL_PROC=/opt/intel/openvino/data_processing/dl_streamer/samples/gst_launch/vehicle_pedestrian_tracking/model_proc/vehicle-attributes-recognition-barrier-0039.json
```

3. Export the video file path:

```sh
# # This example uses ~/gva/video as the video path and FILENAME as the placeholder for a video file name. Change this information to fit your setup.
export VIDEO_EXAMPLE=~/gva/video/<your_downloaded_video>
```

4. Export the output file path:

The $OUTFILE target can be any path and name.  We suggest: `~/gva/out.json`

```sh
# This example uses ~/gva/video as the video path and FILENAME as the placeholder for an output file name. Change this information to fit your setup. 
export OUTFILE=<path-to-FILENAME>
```

5. Create and run the pipeline:

```sh
gst-launch-1.0 \
	filesrc location=${VIDEO_EXAMPLE} ! decodebin ! video/x-raw ! videoconvert ! \
	gvadetect model=${DETECTION_MODEL} model_proc=${DETECTION_MODEL_PROC} device=CPU ! queue ! \
	gvaclassify model=${VEHICLE_CLASSIFICATION_MODEL} model-proc=${VEHICLE_CLASSIFICATION_MODEL_PROC} device=CPU object-class=vehicle ! queue ! \
	gvametaconvert format=json ! \
	gvametapublish method=file file-path=${OUTFILE} ! \
	fakesink
```

In this step:
- `gvametaconvert` uses the optional parameter `format=json` to convert inferenced data to `GstGVAJSONMeta`. 
- `GstGVAJSONMeta` is a custom data structure that represents JSON metadata. 
- `gvametapublish` uses the optional parameter `method=file` to publish inference results to a file.
- `filepath=${OUTFILE}` is a JSON file to which the inference results are published.
	
5. Run the pipeline. After the pipeline completes, a JSON file of the inference results is available. 

6. Review the JSON file (defined by $OUTFILE).

```sh
gedit $OUTFILE
```

You have completed this exercise. Continue to Exercise 4, where you will create a custom multi-model pipeline. 

</details>

### Exercise 4: Create a Threat Detection Pipeline <a name="face-detect"></a>
<details>
	<summary>Threat Detection Pipeline</summary>
<br>
For this exercise, we'll eventually need several wav files to create a threat detection pipeline.
	
First, create a pipeline that detects glass breaking and outputs JSON.

	1. Glass breaking

When successful, add support for the following:
	2. Footsteps 
	3. Coughs 
	4. Dog barking

Different thresholds may be set for each audio category if desired.  You can search on Google for wav files (or mp3 files if you convert them before use).

Then run the DL Streamer audio analytics tool using these files.

</details>


