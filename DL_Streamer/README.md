## DL Streamer Documentation Guide

This guide provides information about the Intel DL Streamer tool, which provides high-level functionality to easily create pipelines for GStreamer and OpenVINO applications.

The DL Streamer plug-in contains multiple GStreamer elements to construct pipelines. Some elements run inference operations such as detection, classification, and tracking, and other elements output inference results to the display screen, file, or publish the results to the Kafka or MQTT message brokers.

The following diagram shows the interactions between the Intel OpenVINO toolkit and the DL Streamer:
<br>

![GStreamer - OpenVINO Developer Workflow](gst_ov_flow.png)

<br>

### GStreamer Documentation
To familiarize yourself with DL Streamer, read through the following files:

NOTE: No actions need to be taken with these guides, they are provided as reference material.

- [DL Streamer Q&A](https://software.intel.com/en-us/forums/intel-distribution-of-openvino-toolkit/topic/852193)
- [Install Guide](Install_Guide.md)
- [Draw Face Attributes Sample](Draw_Face_Attributes_Sample.md)
- [GStreamer Python Bindings](GStreamer_Python_Bindings.md)
- [Supported Models](Supported_Models.md)
- [Model Preparation](Model_Preparation.md)
- [DL Streamer Command Reference](Command_Reference.md)


### Tutorial
Do the exercises in the tutorial below.

- [GStreamer Video Analytics Tutorial](GStreamer_Video_Analytics_Tutorial.md)
