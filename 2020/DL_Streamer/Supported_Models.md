# Models Supported by the DL Streamer

This file lists the pre-trained models from the [Open Model Zoo](https://github.com/opencv/open_model_zoo). You can use any of these with the GStreamer Video Analytics plug-ins. Model Proc is a `.json` file that describes model input and output layers, and the format and rules for data pre-processing and post-processing.

## Object Detection Models (Open Model Zoo)

| Model Name                                                                                                                                                                          | Model Proc |
|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |--------------------- |
| [face-detection-adas-0001](https://docs.openvinotoolkit.org/R5/_docs_Transportation_object_detection_face_pruned_mobilenet_reduced_ssd_shared_weights_caffe_desc_face_detection_adas_0001.html)                               | -                |
| [face-detection-retail-0004](https://docs.openvinotoolkit.org/R5/_docs_Retail_object_detection_face_sqnet10modif_ssd_0004_caffe_desc_face_detection_retail_0004.html)                                                        | [face-detection-retail-0004](../blob/master/samples/model_proc/face-detection-retail-0004.json)                |
| [face-person-detection-retail-0002](https://docs.openvinotoolkit.org/R5/_docs_Retail_object_detection_face_pedestrian_rmnet_ssssd_2heads_0002_caffe_desc_face_person_detection_retail_0002.html)                              | -                |
| [person-detection-retail-0013](https://docs.openvinotoolkit.org/R5/_docs_Retail_object_detection_pedestrian_rmnet_ssd_0013_caffe_desc_person_detection_retail_0013.html)                                                      | -                |
| [pedestrian-detection-adas-0002](https://docs.openvinotoolkit.org/R5/_docs_Transportation_object_detection_pedestrian_mobilenet_reduced_ssd_caffe_desc_pedestrian_detection_adas_0002.html)                                   | -                |
| [pedestrian-and-vehicle-detector-adas-0001](https://docs.openvinotoolkit.org/R5/_docs_Transportation_object_detection_pedestrian_and_vehicle_mobilenet_reduced_ssd_caffe_desc_pe1dd10d582cd5f94db32d33a85c5fd402.html) | -                |
| [vehicle-detection-adas-0002](https://docs.openvinotoolkit.org/R5/_docs_Transportation_object_detection_vehicle_mobilenet_reduced_ssd_caffe_desc_vehicle_detection_adas_0002.html)                                            | -                |
| [person-vehicle-bike-detection-crossroad-0078](https://docs.openvinotoolkit.org/R5/_docs_Security_object_detection_crossroad_0078_caffe_desc_person_vehicle_bike_detection_crossroad_0078.html)                               | [person-vehicle-bike-detection-crossroad-0078.json](../blob/master/samples/model_proc/person-vehicle-bike-detection-crossroad-0078.json)                |
| [vehicle-license-plate-detection-barrier-0106](https://docs.openvinotoolkit.org/R5/_docs_Security_object_detection_barrier_0106_tf_desc_vehicle_license_plate_detection_barrier_0106.html)                                    | [vehicle-license-plate-detection-barrier-0106.json](../blob/master/samples/model_proc/vehicle-license-plate-detection-barrier-0106.json)                |

## Object Detection Models (Public)

| Model Name                                                                                                                                                                          | Model Proc |
|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |--------------------- |
| ssd300 | [pascal.json](../blob/master/samples/model_proc/pascal.json)                |
| yolov3 | [coco.json](../blob/master/samples/model_proc/coco.json)

## Object Recognition Models (Open Model Zoo)

| Model Name                                                                                                                                                                                      | Model Proc  |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------- |
| [age-gender-recognition-retail-0013](https://docs.openvinotoolkit.org/R5/_docs_Retail_object_attributes_age_gender_caffe_desc_age_gender_recognition_retail_0013.html)                                                                    | [age-gender-recognition-retail-0013.json](../blob/master/samples/model_proc/age-gender-recognition-retail-0013.json)                |
| [head-pose-estimation-adas-0001](https://docs.openvinotoolkit.org/R5/_docs_Transportation_object_attributes_headpose_vanilla_cnn_desc_head_pose_estimation_adas_0001.html)                                                                | -                |
| [license-plate-recognition-barrier-0001](https://docs.openvinotoolkit.org/R5/_docs_Security_optical_character_recognition_license_plate_caffe_desc_license_plate_recognition_barrier_0001.html)                                           | [license-plate-recognition-barrier-0001.json](../blob/master/samples/model_proc/license-plate-recognition-barrier-0001.json)                |
| [vehicle-attributes-recognition-barrier-0039](https://docs.openvinotoolkit.org/R5/_docs_Security_object_attributes_vehicle_resnet10_update_1_caffe_desc_vehicle_attributes_recognition_barrier_0039.html)                                 | [vehicle-attributes-recognition-barrier-0039.json](../blob/master/samples/model_proc/vehicle-attributes-recognition-barrier-0039.json)                |
| [emotions-recognition-retail-0003](https://docs.openvinotoolkit.org/R5/_docs_Retail_object_attributes_emotions_recognition_0003_caffe_desc_emotions_recognition_retail_0003.html)                                                         | [emotions-recognition-retail-0003.json](../blob/master/samples/model_proc/emotions-recognition-retail-0003.json)                |
| [landmarks-regression-retail-0009](https://docs.openvinotoolkit.org/R5/_docs_Retail_object_attributes_landmarks_regression_0009_onnx_desc_landmarks_regression_retail_0009.html)                                                          | [landmarks-regression-retail-0009.json](../blob/master/samples/model_proc/landmarks-regression-retail-0009.json)                |
| [facial-landmarks-35-adas-0001](https://docs.openvinotoolkit.org/R5/_docs_Transportation_object_attributes_facial_landmarks_custom_35_facial_landmarks_caffe_desc_facial_landmarks_35_adas_0001.html)                                     | -                |
| [person-attributes-recognition-crossroad-0200](https://docs.openvinotoolkit.org/R5/_docs_Security_object_attributes_pedestrian_person_attributes_recognition_crossroad_0200_onnx_de02598a069f6e7bd67a8d352df43eaca0.html)  | [person-attributes-recognition-crossroad-0200.json](../blob/master/samples/model_proc/person-attributes-recognition-crossroad-0200.json)                |

## Reidentification Models (Open Model Zoo)

| Model Name                                                                                                                                              | Model Proc  |
|---------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------- |
| [face-reidentification-retail-0095](https://docs.openvinotoolkit.org/R5/_docs_Retail_object_reidentification_face_mobilenet_based_onnx_0095_desc_face_reidentification_retail_0095.html)          | [face-reidentification-retail-0095.json](../blob/master/samples/model_proc/face-reidentification-retail-0095.json)                |
