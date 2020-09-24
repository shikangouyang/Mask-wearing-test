## 人脸口罩佩戴检测

## 1 克隆或下载YOLOv4

1）YOLOv4网址：https://github.com/AlexeyAB/darknet

如果安装了Git软件，可直接克隆项目到本地：

```
git clone https://github.com/AlexeyAB/darknet
```

2）权重文件在百度网盘里面：链接: https://pan.baidu.com/s/1LK2tohp5qKNPsmExzBcbBw  密码: nr24



## 2 修改配置文件

### 1）新建data\voc.names文件

可以复制data\voc.names再根据自己情况的修改；可以重新命名如：data\voc-mask.names 

### 2）新建 data\voc.data文件

可以复制data\voc.data再根据自己情况的修改；可以重新命名如：data\voc-mask.data

### 3）新建cfg\yolov4-mask.cfg

可以复制cfg\yolov4-custom.cfg再根据自己情况的修改；可以重新命名cfg\yolov4-mask.cfg：

batch=16
subdivisions=8 （如果显存溢出改为16）

max_batches = 10000

steps=8000, 9000

<img src="https://s1.ax1x.com/2020/05/18/YfTgPA.jpg" style="zoom: 67%;" />

在cfg\yolov4-mask.cfg文件中，三个yolo层和各自前面的convolutional层的参数需要修改：

**三个yolo层都要改**：yolo层中的classes为类别数，每一个yolo层前的convolutional层中的filters =（类别+5）* 3

yolo层 classes=2， convolutional层 filters=21





## 3 测试训练出的网络模型

###    1）创建测试cfg文件

训练好后可以在backup目录下看到权重文件。

尝试test前要修改cfg文件，切换到test模式。可以重新建立一个测试cfg文件, 如yolov4-mask-test.cfg

设置：

batch=1
subdivisions=1

<img src="https://s1.ax1x.com/2020/05/18/YfbFu6.jpg" style="zoom:67%;" />

### 2）测试图片

```
darknet.exe detector test data\voc-mask.data cfg\yolov4-mask-test.cfg backup\yolov4-mask_final.weights testfiles\img1.jpg
```
### 3）测试视频

```
darknet.exe detector demo data\voc-mask.data cfg\yolov4-mask-test.cfg backup\yolov4-mask_final.weights testfiles\kouzhao1.mp4
```

## 4 性能统计

###  统计mAP 

统计 mAP@IoU=0.50: 

```
darknet.exe detector map data\voc-mask.data cfg\yolov4-mask-test.cfg backup\yolov4-mask_final.weights
```

统计 mAP@IoU=0.75: 

```
darknet.exe detector map data\voc-mask.data cfg\yolov4-mask-test.cfg backup\yolov4-mask_final.weights  -iou_thresh 0.75
```

## 5 先验框聚类分析与修改

1) 使用k-means++聚类获得自己数据集的先验框大小

```
darknet.exe detector calc_anchors data\voc-mask.data -num_of_clusters 9 -width 608 -height 608
```

D:\darknet\build\darknet\x64目录下生成anchors.txt文件

2) 修改cfg文件中的先验框大小

3) 重新训练和测试

## 6 改变NMS方法的性能提升

NMS(Non Maximum Suppression),非极大值抑制,是目标检测框架中的后处理模块,主要用于删除冗余的bbox。

修改cfg\yolov4-mask.cfg和cfg\yolov4-mask-test.cfg

```
nms_kind=diounms
```

