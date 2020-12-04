## Pre- and Post-Processing Operations <a name="openvino-format"></a>

Before using this guide, be familiar with the information in ADD OPENVINO LINK

GStreamer inference plug-ins can perform some pre- and post-processing operations before or after inference.

Supported pre-processing operations:
	- Color conversion in the model input layer format (typically RGB or BGR)
	- Image alignment, specifically face alignment according to face landmarks points

Supported post-processing operations for:
	- object detection models: extract bounding box and confidence data from the model output layer and attach the data to GstBuffer as [GstVideoRegionOfInterestMeta](https://github.com/GStreamer/gst-plugins-base/blob/master/gst-libs/gst/video/gstvideometa.h#L275). Supported object detection models:
		* SSD-like models
		* Yolo v2
		* Yolo v3
	- object classification models: extract classification confidence data from the model output layer, find labels with the highest confidence (such as "cat" or "dog") or extract classification result by a specified rule, and attach the result to object's GstVideoRegionOfInterestMeta
	- other models: attach tensor data from all output layers in raw binary format and optionally tag the data format. For example "format=landmark_points" or "format=cosine_distance", so downstream elements can find and interpret inference results.

	Post-processing is optional and enabled if either:
		* the GStreamer element automatically detected the model output format for SSD-like object detection models
		* the property 'model_proc' is explicitly set in the inference element with a reference to manually created .json file

Look [here](https://github.com/opencv/gst-video-analytics/tree/master/samples/model_proc) for examples of .json files for models from the [OpenVINO toolkit Model Zoo](https://github.com/opencv/open_model_zoo) and some public models.

Since most computer vision models are trained in BGR input format, this is the default format if you skip the pre-processing section the .json file or if you don't specify a .json file.

## Specifying model files in GStreamer elements

The IR format of the model consists of a `.bin` and a `.xml` file. The GStreamer elements expect the IR model files to have the same base file name. Only the file extension is different, such as `xyz.bin` and `xyz.xml`. You must specify the path for the .xml file in the 'model' property of the GStreamer inference elements `gvainference`, `gvadetect`, and `gvaclassify`.

For example, an object detection (`gvadetect`) and object classification (`gvaclassify`) pipeline looks like this:
	```sh
	gvadetect model=MODEL1_FILE_PATH.xml ! gvaclassify model=MODEL1_FILE_PATH.xml
	```

A similar example that includes pre-processing/post-processing rules looks like this:
	```sh
	gvadetect model=MODEL1_FILE_PATH.xml model-proc=MODEL1_FILE_PATH.json ! gvaclassify model=MODEL2_FILE_PATH.xml model-proc=MODEL2_FILE_PATH.json
	```


