# rk3588部署yolov5模型转换pt-onnx-rknn

## rk3588部署yolov5模型，需要对训练好的pt模型进行两步转换为rknn **第一步pt->onnx**只需要yolov5环境在linux和window环境下都可以进行 **第二步onnx->rknn**只能在linux系统下进行

**参考文档：[瑞芯微rk3588部署yolov5模型实战](https://www.ebaina.com/articles/140000017170)**

## yolov5环境配置(pt->onnx)

**参考文档：[Ubuntu20.04系统配置Pytorch环境(GPU版)](https://blog.csdn.net/m0_55127902/article/details/135677560)**

yolov5版本非常重要，一定要调用这个版本的**yolov5[yolov5](https://github.com/airockchip/yolov5)**

```bash
git clone https://github.com/airockchip/yolov5.git  # clone
cd yolov5
pip install -r requirements.txt  # install
```

将pt模型和export.py放在统一目录下

```bash
# windows
python export.py --rknpu --weight yolov5s.pt

# ubuntu
python3 export.py --rknpu --weight yolov5s.pt
```

rk_platform 支持 rk1808、rv1109、rv1126、rk3399pro、rk3566、rk3562、rk3568、rk3588、rv1103、rv1106。（实际上，无论平台如何，导出的模型都是相同的）
’yolov5s.pt’ 可以替换为您的模型路径.
将生成一个文件名“RK_anchors.txt”，可以在外部执行 post_process 时使用它。
注意：请使用–rknpu参数调用，不要更改export.py中的默认rknpu值。

## rknn-toolkit2-2.3.2环境(onnx->rknn)

**rknn-toolkit2-2.3.2**安装

```bash
git clone https://github.com/airockchip/rknn-toolkit2.git

cd /home/xxx/rk3588/rknn-toolkit2-2.3.2/rknn-toolkit2/packages/x86_64 
# 根据系统版本选择
pip install rknn_toolkit2-2.3.2-cp38-cp38-manylinux_2_17_x86_64.manylinux2014_x86_64.whl
```

**onnx->rknn**

```bash
cd /home/xxx/rk3588/rknn-toolkit2-2.3.2/rknn-toolkit2/examples/onnx/yolov5



test.py

# 模型和图片位置
ONNX_MODEL = 'yolov5s.onnx'
RKNN_MODEL = 'yolov5s.rknn'
IMG_PATH = './bus.jpg'
DATASET = './dataset.txt'

#是否int8轻量化
QUANTIZE_ON = True

OBJ_THRESH = 0.25
NMS_THRESH = 0.45
IMG_SIZE = 640

# 标定class
# CLASSES = ("0",)  # 修正为元组
CLASSES = ("person", "bicycle", "car", "motorbike ", "aeroplane ", "bus ", "train", "truck ", "boat", "traffic light",
           "fire hydrant", "stop sign ", "parking meter", "bench", "bird", "cat", "dog ", "horse ", "sheep", "cow", "elephant",
           "bear", "zebra ", "giraffe", "backpack", "umbrella", "handbag", "tie", "suitcase", "frisbee", "skis", "snowboard", "sports ball", "kite",
           "baseball bat", "baseball glove", "skateboard", "surfboard", "tennis racket", "bottle", "wine glass", "cup", "fork", "knife ",
           "spoon", "bowl", "banana", "apple", "sandwich", "orange", "broccoli", "carrot", "hot dog", "pizza ", "donut", "cake", "chair", "sofa",
           "pottedplant", "bed", "diningtable", "toilet ", "tvmonitor", "laptop	", "mouse	", "remote ", "keyboard ", "cell phone", "microwave ",
           "oven ", "toaster", "sink", "refrigerator ", "book", "clock", "vase", "scissors ", "teddy bear ", "hair drier", "toothbrush ")




python3 test.py
```

rknn-toolkit2-2.3.2,rknn-toolkit2-1.5.0,rknn_model_zoo都可以进行转换

## 实验室服务器进行yolov5 pt->rknn步骤

### pt->onnx

这一步在windows和ubuntu系统中都可以进行，两个系统都使用conda配置好了环境

**winodw**

开启Anaconda Prompt命令行

```bash
conda activate rk3588-yolov5

D:

cd rk3588-yolov5/yolov5-6.2

python export.py --rknpu --weight yolov5s.pt
```

**ubuntu**

```bash
conda activate rk3588-yolov5 # 可能差不多就是这个

cd rk3588/yolov5-6.2

python3 export.py --rknpu --weight yolov5s.pt
```

### onnx->rknn

```bash
cd /home/xxx/rk3588/rknn-toolkit2-2.3.2/rknn-toolkit2/examples/onnx/yolov5


test.py

# 模型和图片位置
ONNX_MODEL = 'yolov5s.onnx'
RKNN_MODEL = 'yolov5s.rknn'
IMG_PATH = './bus.jpg'
DATASET = './dataset.txt'

#是否int8轻量化
QUANTIZE_ON = True

OBJ_THRESH = 0.25
NMS_THRESH = 0.45
IMG_SIZE = 640

# 标定class
# CLASSES = ("0",)  # 修正为元组
CLASSES = ("person", "bicycle", "car", "motorbike ", "aeroplane ", "bus ", "train", "truck ", "boat", "traffic light",
           "fire hydrant", "stop sign ", "parking meter", "bench", "bird", "cat", "dog ", "horse ", "sheep", "cow", "elephant",
           "bear", "zebra ", "giraffe", "backpack", "umbrella", "handbag", "tie", "suitcase", "frisbee", "skis", "snowboard", "sports ball", "kite",
           "baseball bat", "baseball glove", "skateboard", "surfboard", "tennis racket", "bottle", "wine glass", "cup", "fork", "knife ",
           "spoon", "bowl", "banana", "apple", "sandwich", "orange", "broccoli", "carrot", "hot dog", "pizza ", "donut", "cake", "chair", "sofa",
           "pottedplant", "bed", "diningtable", "toilet ", "tvmonitor", "laptop	", "mouse	", "remote ", "keyboard ", "cell phone", "microwave ",
           "oven ", "toaster", "sink", "refrigerator ", "book", "clock", "vase", "scissors ", "teddy bear ", "hair drier", "toothbrush ")




python3 test.py
```