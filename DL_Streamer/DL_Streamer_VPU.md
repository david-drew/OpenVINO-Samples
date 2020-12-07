

### Start the HDDLUnite Service

1. `cd /opt/intel/nvr_demo/hddlunite`
2. `source ./env_host.sh`
3. `bin/hddl_scheduler_service &`
4. `bin/SetHDDLMode -m s`
 

### Open a New Terminal and Run the Demo

1. `cd /opt/intel/nvr_demo/streaming/`
2. `source ./env_host_streamingmode.sh`
3. `cd kmb_ia_sample/`
4. `./msdkplayer -c ../config_streamingmode_fullpipeline_8.json`
