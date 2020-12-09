# DL Streamer VPU Streaming Sample Guide

This guide provides a quick overview to running DL Streamer and offloading the inference to a 3rd Generation Movidius VPU.

(The hardware and software are in beta, so please be patient.)

### The New Element for the 3rd Generation VPU 
 <summary>New Element</summary>

```
 remoteoffloadbin.( 
    vaapih265dec
    gvadetect model=remotefilesystem:${DETECT_MODEL} device=KMB nireq=4 model-proc=remotefilesystem:${DETECT_PROC} inference-interval=2 threshold=0.5 ie-config=VPU_KMB_PREPROCESSING_SHAVES=4,VPU_KMB_PREPROCESSING_LPI=8
    pre-process-backend=ie
    queue
    sublaunch launch-string=\"vasobjecttracker tracking-type=SHORT_TERM_IMAGELESS device=VPU\"
    queue
    gvaclassify model=remotefilesystem:${CLASSIFY_MODEL} device=KMB nireq=4 model-proc=remotefilesystem:${CLASSIFY_PROC} ie-config=VPU_KMB_PREPROCESSING_SHAVES=4,VPU_KMB_PREPROCESSING_LPI=8 pre-process-backend=ie reclassify-interval=100
    videoroimetadetach
)
```



### Start the HDDLUnite Service

The HDDLUnite service provides configuration, management, and communication functionality between the host and VPU device.  This must be running before attempting to use inference or video acceleration on the VPU.

1. `cd /opt/intel/nvr_demo/hddlunite`
2. `source ./env_host.sh`
3. `bin/hddl_scheduler_service &`
4. `bin/SetHDDLMode -m s`
 

### Open a New Terminal and Run the Demo

This example runs a pre-built sample GUI, with multiple channels (which could be multiple videos, cameras, etc.).

1. `cd /opt/intel/nvr_demo/streaming/`
2. `source ./env_host_streamingmode.sh`
3. `cd kmb_ia_sample/`
4. `./msdkplayer -c ../config_streamingmode_fullpipeline_8.json`

### Questions About the Demo

Open up the json configuration file in a text editor.  Feel free to copy and "clean up" in a separate temporary text file if desired.

* How would you go about creating your own configuration file?  What are some important elements?
