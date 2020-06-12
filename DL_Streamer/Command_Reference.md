# DL Streamer Command Reference

| Term | Type | Description |

| `decodebin` | Element | Uses the input format to selects the decoder, giving hardware decoder plug-ins priority. Use `decodebin` to insert video processing elements for color conversion or for video-to-system memory copying. |


| `filesrc` | Element | Reads data from a file or camera. |

| `GstVideoRegionOfInterestMeta` | Element | Specifies crop area.|
| `GstGVATensorMeta` | Element | Outputs results from `gvainference`.|
| `GstGVAJSONMeta` | Element | Outputs results from `gvametaconvert` |

| `gvametaconvert` | Element | Converts `GstVideoRegionOfInterestMeta` to a `.json` format file |
| `gvainference` | Element | Performs generic inference on a model that has an image-compatible input layer and any output layer format |
| `gvadetect` | Element | Runs object detection inference on the decoded frame from `decodebin`. In this process, `gvadetect` uses the `model` parameter to perform inference on the model that you specify. As an option, you can add the `model-proc` parameter to configure the output. A list of regions is returned, known as Region of Interest (ROI), The ROI includes the detected object.|
| `gvaclassify` | Element | performs inference on each video frame ROI. Inference uses the `model` parameter, and returns the result as key=value, such as age=40 or gender=female. You can also use 'gvaclassify' to add filters to the inference, but only on by object class, such as a vehicle or pedestrian. |
| `gvawatermark` | Element | Adds the detection and classification results as an overlay on each frame. This element uses a `sync` property.  |

| `model` | parameter | The file name of the model to use for inference. |
| `model-proc` | parameter | Use with `gvaclassify` to provide a `.json` file for interpreting the results and to configure the output layer name and labels. |


| `sync` | Property | Determines whether to run a pipeline in real-time speed. Most samples use `sync=false` to disable real-time synchronization to increase the pipeline speed. 
| `xvimagesink` | Element | Renders the video frames. |

| 'video/x-raw' | | System memory frame. |
| 'video/x-raw(memory:VASurface)' | | Video memory handle. |
