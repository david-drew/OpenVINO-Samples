# GStreamer Python bindings

This topic introduces the following terms:

`pygst`: `python-gst`: Python bindings
`gstgva`: A Python library
`gstpy`: not defined
`ctypes`: A Python mechanism. This mechanism implements wrappers in the `gstgva` library


GStreamer provides Python bindings in the module called `python-gst` (`pygst`). Install this module with the apt package manager:
	```
	sudo apt-get install python-gst-1.0
	```

GObject introspection automatically generates Python wrappers, so the GStreamer Python interface uses one-to-one mapping with the C interface. Like C or C++, a Python application can construct pipelines either from a pipeline string description in the `gst-launch` format or by programmatically creating and connecting elements. A Python application can also set pad probe callbacks on the source or sink pad of any element in the pipeline.

See this Python example [draw_face_attributes.py](https://github.com/opencv/gst-video-analytics/blob/master/samples/python/draw_face_attributes/draw_face_attributes.py).

# Video-analytics specific Python bindings

Since the GVA plug-in registers inference-specific metadata, a complimentary Python library named [`gstgva`](https://github.com/opencv/gst-video-analytics/blob/master/python/gstgva) provides Python bindings for GVA-specific types like `GstGVATensorMeta` and `GstGVAJSONMeta`, and access to inference specific fields in `GstVideoRegionOfInterestMeta`.

Unlike `gstpy`, C to Python wrappers in the `gstgva` library are implemented through a Python `ctypes` mechanism. These wrappers provide one-to-one mapping with the GVA C++ interface.

[Python callback function examples](https://github.com/opencv/gst-video-analytics/wiki/Metadata) on the Metadata wiki page demonstrate accessing a list of detected objects and object attributes, similar to the C++ function.
	```
	def pad_probe_callback(pad, info):
		with gstgva.util.GST_PAD_PROBE_INFO_BUFFER(info) as buffer:
			caps = pad.get_current_caps()
			frame = gstgva.VideoFrame(buffer, caps)
			for roi in frame.regions():
				for tensor in roi.tensors():
					print("  Attribute ", tensor.name())
					print("    label=", tensor.label())
					print("    confidence=", tensor.confidence())
		return Gst.PadProbeReturn.OK
	```

# gvapython element

In addition to Python binding for standard GStreamer and GVA plug-in interfaces, you can use the GStreamer element `gvapython` to customize pipelines in Python.

The `gvapython` inserted at any place of pipeline and takes reference to Python file and function/class name as element property, and invokes provided callback function on every frame.

The `gvapython` element could be used for post-processing of metadata generated by inference elements (for example, extract list of detected bounding boxes in case of detection model format not supported by GVA), or perform some analytics based on inference results (for example, compare face embeddings versus gallery of known faces).

The `gvapython` element implemented in C language as normal GStreamer element and invokes Python functions via C interface (`Python.h`).

See samples for gvapython element in [this folder](https://github.com/opencv/gst-video-analytics/blob/master/samples/python/gvapython)

# Performance considerations

Python code performance is usually slower than C or C++ code compiled into native instruction set.
The parallelization is also limited as all Python code executed in single thread.
If Python callback contains compute intensive operations executed every frame, it may impact overall performance and production usage may require migration from Python to C implementation.
