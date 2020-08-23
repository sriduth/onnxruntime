# TensorRT Performance Test Script
This script fetches models from ONNX Model Zoo and runs Onnxruntime inference using TensorRT and CUDA as execution provider to get TensorRT peformance gain compared with CUDA, as well as metrics such as percentage of model operators that run on TensorRT.

## Usage
You can use following command to run benchmark and validate prediction results:
```
./perf.sh
```
If you only want to run benchmark or use randomly generated input data instead of input data from ONNX model zoo, please use following command:
```
python3 benchmark.py -r benchmark -i random -t 100
```
### Options
- **-r, --running_mode**: (*defaul: benchmark*) There are two types of running mode, *validate* and *benchmark*. For validation, this test script records any runtime error as well as validates the accuracy of prediction result using *np.testing.assert_almost_equal()* and exposes result that doesn't meet accuracy requirement. For benchmark, it simply runs model inference assuming model is correct and get the performance metrics. (Note: If you run validation first and then benchmark, test script knows which model has issue and will skip benchmarking of this particular model.)
- **-m, --model_zoo**: (*default: onnx*) The source of the models. It has two selection *onnx* and *cvs*. Test script uses models from ONNX model zoo as default. But it can also run models from somewhere else, ex: it currently supports models from custom vision servce in Microsoft.
- **-i, --input_data**: (*default: random*) Where is the input data coming from. The value are *zoo* or *random*. The input data can be from ONNX model zoo or it can be randomly generated by test script.
- **-t, --test_times**: (*default: 1*) Number of inference run when in 'benchmark' running mode.
- **--fp16**: (*default: True*) Enable TensorRT/CUDA FP16 and include the performance of this floating point optimization.
### Results
After running validation and benchmark. The metrics are written into five different csv files in 'result' directory.
- **benchmark_fail_xxxx.csv**: Lists all the models that fail to be inferenced by TensorRT/CUDA.
- **benchmark_success_xxxx.csv**: Lists all the models that can be successfully inferenced by TensorRT/CUDA, as well as other related metrics.
- **benchmark_latency_xxxx.csv**: Lists all the models with inference latecy of TensorRT/CUDA and TensorRT Float32/Float16 performance gain compared with CUDA.
- **benchmark_ratio_xxxx.csv**: List how much and percentage of model operators are run by TensorRT and what percentage of execution time is running on TensorRT.
- **benchmark_system_info_xxxx.csv**: includes CUDA version, TensorRT version, CPU information and Onnxruntime version.

Thoese metrics will be shown on the standard output as well.

The output of running validation:
```
Total time for running/profiling all models: 0:20:30.761618
['bert-squad', 'faster-rcnn', 'mask-rcnn', 'ssd', 'tiny-yolov2', 'resnet152v1']

Total models: 6
Fail models: 2
Models FAIL/SUCCESS: 2/4

============================================
========== Failing Models/EPs ==============
============================================
{'faster-rcnn': ['CUDAExecutionProvider_fp16'], 'mask-rcnn': ['CUDAExecutionProvider_fp16']}

========================================
========== TRT detail metrics ==========
========================================
{   'BERT-Squad': {   'ratio_of_execution_time_in_trt': 0.9980344366695495,
                      'ratio_of_ops_in_trt': 0.9989451476793249,
                      'total_execution_time': 12719,
                      'total_ops': 948,
                      'total_ops_in_trt': 947,
                      'total_trt_execution_time': 12694},
    'BERT-Squad (FP16)': {   'ratio_of_execution_time_in_trt': 0.9948146725561744,
                             'ratio_of_ops_in_trt': 0.9989451476793249,
                             'total_execution_time': 5207,
                             'total_ops': 948,
                             'total_ops_in_trt': 947,
                             'total_trt_execution_time': 5180},
    'FasterRCNN-10': {   'ratio_of_execution_time_in_trt': 0.881433685003768,
                         'ratio_of_ops_in_trt': 0.8637346791636625,
                         'total_execution_time': 106160,
                         'total_ops': 2774,
                         'total_ops_in_trt': 2396,
                         'total_trt_execution_time': 93573},
    'FasterRCNN-10 (FP16)': {   'ratio_of_execution_time_in_trt': 0.8391227836682785,
                                'total_execution_time': 67623,
                                'total_trt_execution_time': 56744},
    'MaskRCNN-10': {   'ratio_of_execution_time_in_trt': 0.9084868640292711,
                       'ratio_of_ops_in_trt': 0.8557567917205692,
                       'total_execution_time': 147039,
                       'total_ops': 3092,
                       'total_ops_in_trt': 2646,
                       'total_trt_execution_time': 133583},
    'MaskRCNN-10 (FP16)': {   'ratio_of_execution_time_in_trt': 0.8537288833951381,
                              'total_execution_time': 87372,
                              'total_trt_execution_time': 74592},
    'Resnet-152-v1': {   'ratio_of_execution_time_in_trt': 1.0,
                         'ratio_of_ops_in_trt': 1.0,
                         'total_execution_time': 12330,
                         'total_ops': 360,
                         'total_ops_in_trt': 360,
                         'total_trt_execution_time': 12330},
    'Resnet-152-v1 (FP16)': {   'ratio_of_execution_time_in_trt': 1.0,
                                'ratio_of_ops_in_trt': 1.0,
                                'total_execution_time': 3201,
                                'total_ops': 360,
                                'total_ops_in_trt': 360,
                                'total_trt_execution_time': 3201},
    'SSD': {   'ratio_of_execution_time_in_trt': 0.6751571867232051,
               'ratio_of_ops_in_trt': 0.9905660377358491,
               'total_execution_time': 102585,
               'total_ops': 212,
               'total_ops_in_trt': 210,
               'total_trt_execution_time': 69261},
    'SSD (FP16)': {   'ratio_of_execution_time_in_trt': 0.38334507797420264,
                      'ratio_of_ops_in_trt': 0.9905660377358491,
                      'total_execution_time': 32639,
                      'total_ops': 212,
                      'total_ops_in_trt': 210,
                      'total_trt_execution_time': 12512},
    'tiny_yolov2': {   'ratio_of_execution_time_in_trt': 1.0,
                       'ratio_of_ops_in_trt': 1.0,
                       'total_execution_time': 3003,
                       'total_ops': 33,
                       'total_ops_in_trt': 33,
                       'total_trt_execution_time': 3003},
    'tiny_yolov2 (FP16)': {   'ratio_of_execution_time_in_trt': 1.0,
                              'ratio_of_ops_in_trt': 1.0,
                              'total_execution_time': 864,
                              'total_ops': 33,
                              'total_ops_in_trt': 33,
                              'total_trt_execution_time': 864}}

```

The output of running benchmark:
```

=========================================
=========== CUDA/TRT latency  ===========
=========================================
{   'BERT-Squad': {   'CUDAExecutionProvider': '28.88',
                      'CUDAExecutionProvider_fp16': '18.08',
                      'TensorrtExecutionProvider': '15.55',
                      'TensorrtExecutionProvider_fp16': '5.00',
                      'Tensorrt_fp16_gain(%)': '72.35 %',
                      'Tensorrt_gain(%)': '46.16 %'},
    'FasterRCNN-10': {   'CUDAExecutionProvider': '161.40',
                         'TensorrtExecutionProvider': '109.24',
                         'TensorrtExecutionProvider_fp16': '66.68',
                         'Tensorrt_gain(%)': '32.32 %'},
    'MaskRCNN-10': {   'CUDAExecutionProvider': '221.93',
                       'TensorrtExecutionProvider': '154.04',
                       'TensorrtExecutionProvider_fp16': '83.78',
                       'Tensorrt_gain(%)': '30.59 %'},
    'Resnet-152-v1': {   'CUDAExecutionProvider': '22.55',
                         'CUDAExecutionProvider_fp16': '24.59',
                         'TensorrtExecutionProvider': '9.82',
                         'TensorrtExecutionProvider_fp16': '3.22',
                         'Tensorrt_fp16_gain(%)': '86.91 %',
                         'Tensorrt_gain(%)': '56.45 %'},
    'SSD': {   'CUDAExecutionProvider': '176.23',
               'CUDAExecutionProvider_fp16': '82.34',
               'TensorrtExecutionProvider': '109.34',
               'TensorrtExecutionProvider_fp16': '40.73',
               'Tensorrt_fp16_gain(%)': '50.53 %',
               'Tensorrt_gain(%)': '37.96 %'},
    'tiny_yolov2': {   'CUDAExecutionProvider': '6.99',
                       'CUDAExecutionProvider_fp16': '5.50',
                       'TensorrtExecutionProvider': '3.15',
                       'TensorrtExecutionProvider_fp16': '1.39',
                       'Tensorrt_fp16_gain(%)': '74.73 %',
                       'Tensorrt_gain(%)': '54.94 %'}}

```
## Dependencies
- This test script uses following script to infer shape in the model for TensorRT execution provider. 
https://github.com/microsoft/onnxruntime/blob/master/onnxruntime/core/providers/nuphar/scripts/symbolic_shape_infer.py
- When inferencing model using CUDA float16, this script following script to convert nodes in model graph from float32 to float16. It also modifies the converting script a little bit to better cover more model graph conversion.
https://github.com/microsoft/onnxconverter-common/blob/master/onnxconverter_common/float16.py
