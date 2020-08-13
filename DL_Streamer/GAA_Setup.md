# Gstreamer Audio Analytics Setup


Read the Notes and Workarounds in this doc, and use them when following the install guide. 

Follow the [Video Analytics Install Guide](https://github.com/opencv/gst-video-analytics/wiki/Install-Guide)


## Notes and Workarounds (for the install guide)

- Use either Docker or Host install option
	- Host is easier for one-time installs
	- Docker will be easier for redoing installs 
		- The Docker option may not be updated to include the audio plugins

- Near the beginning, the guide says to clone the gst-video-analytics repo.  After doing this, make sure to change branches before proceeding:
	- `git checkout preview/audio-detect`


- The "Install message brokers [OPTIONAL]" section requires a script that needs fixes.  Ignore this step.

- The "Build GVA plugin" section
	- The posted code is essentially a bash script
	- Copy all of it, and paste into a new file
	- Add this at the top of the new file, with a newline or two at the end:
			`#!/bin/bash`

- OpenVINO doesn't yet support audio analytics (which is why we have to use the install guide and compile our own version)
- Remove (or move) the DL Streamer bits from the OpenVINO Installation
	- `sudo rm -rf /opt/intel/openvino/data_processing/dl_streamer`



