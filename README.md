# R-C3D: Region Convolutional 3D Network for Temporal Activity Detection

By Huijuan Xu, Abir Das and Kate Saenko (Boston University).

### Introduction

We propose a fast end-to-end Region Convolutional 3D Network (R-C3D) for activity detection in continuous video streams. The network encodes the frames with fully-convolutional 3D filters, proposes activity segments, then classifies and refines them based on pooled features within their boundaries.

### License

R-C3D is released under the MIT License (refer to the LICENSE file for details).

### Citing R-C3D

If you find R-C3D useful in your research, please consider citing:

    @inproceedings{Xu2017iccv,
        title = {R-C3D: Region Convolutional 3D Network for Temporal Activity Detection},
        author = {Huijuan Xu and Abir Das and Kate Saenko},
        booktitle = {Proceedings of the International Conference on Computer Vision (ICCV)},
        year = {2017}
    }

We build this repo based on Fater R-CNN, C3D and ActivityNet dataset. Please cite the following papers as well:

Ren, Shaoqing, Kaiming He, Ross Girshick, and Jian Sun. "Faster R-CNN: Towards real-time object detection with region proposal networks." In Advances in neural information processing systems, pp. 91-99. 2015.

Tran, Du, Lubomir Bourdev, Rob Fergus, Lorenzo Torresani, and Manohar Paluri. "Learning spatiotemporal features with 3d convolutional networks." In Proceedings of the IEEE international conference on computer vision, pp. 4489-4497. 2015. 

Caba Heilbron, Fabian, Victor Escorcia, Bernard Ghanem, and Juan Carlos Niebles. "Activitynet: A large-scale video benchmark for human activity understanding." In Proceedings of the IEEE Conference on Computer Vision and Pattern Recognition, pp. 961-970. 2015.

### Contents
1. [Installation](#installation)
2. [Preparation](#preparation)
3. [Training](#training)
4. [Testing](#testing)

### 安装 Installation:

1. 复制仓库 Clone the R-C3D repository.
  	```Shell
  	git clone --recursive git@github.com:VisionLearningGroup/R-C3D.git
  	```
  
2. Build `Caffe3d` with `pycaffe` (see: [Caffe installation instructions](http://caffe.berkeleyvision.org/installation.html)).

	**Note:** Caffe must be built with Python support!
  
	```Shell
	cd ./caffe3d
	# 需要修改一些 编译配置
	# If have all of the requirements installed and your Makefile.config in place, then simply do:
	make -j8 && make pycaffe
	```
[Makefile.config 参考修改](https://github.com/Ewenwan/MVision/blob/master/darknect/caffe/caffe_src_change/Makefile.config)

[由于cudnn版本较新，编译可能出错，参考cudnn.hpp修改](https://github.com/Ewenwan/MVision/blob/master/darknect/caffe/caffe_src_change/cudnn.hpp)


3. Build R-C3D lib folder.

	```Shell
	cd ./lib    
	make
	```

	可能还会由于python版本问题编译出错
	将系统python默认版本切换到 python2.7

	sudo rm -rf /usr/bin/python
	sudo ln -s /usr/bin/python2.7 /usr/bin/python


### 准备数据集 Preparation:

1. Download the ground truth annatations and videos in ActivityNet dataset.

	```Shell
	cd ./preprocess/activityNet/
	
	# Download the groud truth annotations in ActivityNet dataset 标注文件.
	wget http://ec2-52-11-11-89.us-west-2.compute.amazonaws.com/files/activity_net.v1-3.min.json
	
	# Download the videos in ActivityNet dataset into ./preprocess/activityNet/videos 视频数据集.
	python download_video.py
	```

2. Extract frames from downloaded videos in 25 fps 处理视频.

	```Shell
	# training video frames are saved in ./preprocess/activityNet/frames/training/
	# validation video frames are saved in ./preprocess/activityNet/frames/validation/ 
	python generate_frames.py
	```

3. Generate the pickle data for training and testing R-C3D model 生成训练和验证素材.

	```Shell
  	# generate training data
	python generate_roidb_training.py
  	# generate validation data
	python generate_roidb_validation.py
  	```

### 训练 Training:
	
1. 下载预训练模型 Download C3D classification pretrain model to ./pretrain/ .

   The C3D model weight pretrained on Sports1M and finetuned on ActivityNet dataset is provided in: [caffemodel .](https://drive.google.com/file/d/131Cpuq1FndydeHzu38TY0baiS-uyN71w/view)

2. 运行训练脚本 In R-C3D root folder, run:
	```Shell
	./experiments/activitynet/script_train.sh
  	```

### 测试 Testing:

1. Download one sample R-C3D model to ./snapshot/ .

   One R-C3D model on ActivityNet dataset is provided in: [caffemodel .](https://drive.google.com/file/d/1wkDwwdqEt6S0xduR4PWalGZaXpxjsX_j/view)

   The provided R-C3D model has the Average-mAP 14.4% on the validation set.
   
   
2. 预测 In R-C3D root folder, generate the prediction log file on the validation set.
	```Shell
	./experiments/activitynet/test/script_test.sh
  	```
	
3. Generate the results.json file from the prediction log file.
	```Shell
	cd ./experiments/activitynet/test
	python activitynet_log_analysis.py test_log_<iters>.txt.*
  	```

4. Get the detection evaluation result.
	```Shell
	cd ./experiments/activitynet/test/Evaluation
	python get_detection_performance.py data/activity_net.v1-3.min.json ../results.json
  	```
	
### Notes: 
### The codes for THUMOS'14 dataset and Charades dataset are uploaded to the corresponding folders.


