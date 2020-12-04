## How to Build and Run the Samples

### Step 1: Get the GVA Plugins and Samples

You have two ways to use samples:

- [In a Docker container](Acquire-docker-image-and-run-docker-container). To learn about Docker containers, click [here](https://www.docker.com/resources/what-container).
- [Directly on your host system](Build-on-host-machine).


### Step 2: Run the Samples

The steps below help you quickly run your first sample application. To see the full list of samples, with descriptions and thorough instructions, click [here](https://github.com/opencv/gst-video-analytics/tree/master/samples/shell). The full instructions also show you how to use GVA plugins. 

1. Unless you use Docker to build and run your samples, set up the OpenVINO<sup>&#8482;</sup> toolkit environment variables. For Docker users, Docker takes care of this for you. To source the environment variables:

	```sh
	source /opt/intel/computer_vision_sdk/bin/setupvars.sh
	```

2. Go to the shell samples folder:

	```sh
	cd ~/gst-video-analytics/samples/shell
	```

3. Run the sample with a video of your choice:

	```sh
	./face_detection_and_classification.sh <path_to_video_file>
	```
 
The result is a video with object that shows detection and attributes recognition as boxes around faces on the display with recognized attributes.

