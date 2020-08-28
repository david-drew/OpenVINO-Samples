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

### Quick Introduction to DL Streamer

#### DL Streamer Fundamentals

The `gst-launch-1.0` command is used to create and run the pipeline.  
The model-proc JSON files describes model input and output layers, and provides labels for a model's output.
Various 'elements' are chained together using the pipe symbol `|`.   
Elements are parameters that call DL Streamer subsystems.
Some elements are just toggles (on or off), others require additional settings.

See the [DL Streamer documentation](https://github.com/david-drew/OpenVINO-Samples/blob/master/DL_Streamer/README.md) for more details.

Example DL Streamer Command (do not run):
```
gst-launch-1.0 \
	filesrc location=${VIDEO_EXAMPLE} ! decodebin ! video/x-raw ! videoconvert ! \
	gvadetect model=${DETECTION_MODEL} model_proc=${DETECTION_MODEL_PROC} device=CPU ! queue ! \
	gvawatermark ! fpsdisplaysink video-sink=xvimagesink sync=false
```

More complex versions of the above, using multiple models, can be seen in the scripts used in Exercise 1 and 2.

#### Do the Exercise

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


### Exercise 1: Run Two Sample Pipelines <a name="simple-pipeline"></a>

<details>
	<summary>Run Two Sample Pipelines</summary>
<br>

This exercise has you run two GStreamer pipelines that use specific models to run detection on an Intermediate Representation (IR) formatted model. In this exercise you run inference to detect faces, then people and vehicles in a video.  This exercise introduce the general concepts and focuses on video.  

Remember to set the environment variables if not already set.:

```sh
source /opt/intel/openvino/bin/setupvars.sh
source ~/gva/gst-video-analytics/scripts/setup_env.sh
export MODELS_PATH=~/gva/models
```

The included DL Streamer scripts use $MODELS_PATH as both a download target and to use models for inference.  You'll need to download at least one face video and one mixed vehicle, pedestrian, and bicycle video.  Any source is fine, but [Pexels](https://videos.pexels.com) is suggested.

Now run the following two sample tutorials.

1. [DL Streamer Face Detection](https://github.com/opencv/gst-video-analytics/tree/master/samples/gst_launch/face_detection_and_classification)
2. [DL Streamer Vehicle Pedestrian Tracking](https://github.com/opencv/gst-video-analytics/tree/master/samples/gst_launch/vehicle_pedestrian_tracking)


Open the scripts in a text editor, and note the gst-launch-1.0 commands near the end of each script which do the actual work. 

Feel free to try different videos, or make modifications to the scripts.

</details>


### Exercise 2: Run an Audio Classification Pipeline <a name="classification-pipeline"></a>

<details>
	<summary>Run a Classification Pipeline</summary>
<br>	

This exercise uses a script to categorize a variety of environmental sounds.  A 16-bit wav file is required as the input, ffmpeg or similar tools can convert other audio file types.  For this exercise we'll run the script, then open the script in a text editor to note differences.

```sh
cd ~/gva/gst-video-analytics/samples/gst_launch/audio_detect
```

Run the script, pointing it at the gettysburg speech file.
```sh
./audio_event_detection.sh ~/gva/media/gettysburg.wav
```

Note that the output is printed to the terminal window as JSON, and there are a variety of fields for timestamps, lables, and other information.  Also note that 'Speech' is being recognized.

Run the script, pointing it at the animal sounds file. 
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

### Exercise 3: Create a Custom Threat Detection Pipeline <a name="face-detect"></a>
<details>
	<summary>Threat Detection Pipeline</summary>
<br>
For this exercise, we'll need several wav files to create a threat detection pipeline.  The goal is to put together a custom pipeline, using what you've learned in the previous exercises.  There are wav files in /tmp/sound.
	
First, create a pipeline that detects glass breaking and outputs JSON.

1. Glass breaking

When your pipeline is detecting breaking glass, continue to add additional wav files:

2. Footsteps 
3. Coughs 
4. Dog barking

The goal is to incorporate one (or more) of each type of wav file into the pipeline, but it may be easier to add one at a time.

Different thresholds may be set for each audio category if desired.  You can search on Google for wav files if needed (or mp3 files if you convert them before use).

</details>


