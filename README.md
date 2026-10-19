工业安全帽检测数据集，工地安全帽检测，电力安全帽检测，YOLO数据集，目标检测数据集，标签已转换为YOLO格式，标签由'helmet', 'head_with_helmet', 'head', 'person_with_helmet', 'face', 'person_no_helmet', 'person'组成，一共5000张图片

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/158919e92fa44453bc4ece976f7adb0f.png)


---

### **工业安全帽检测数据集**

| **项目** | **详情** |
| :--- | :--- |
| **数据集名称** | 工业安全帽检测数据集 |
| **任务类型** | 目标检测（多类别） |
| **图像总数** | 5,000 张 |
| **标注格式** | YOLO 格式（`.txt` 标签文件） |
| **应用场景** | 工地安全监控、电力设施巡检、人员防护装备检查等 |
| **类别数量** | 7 类 |
| **类别名称** | 
  - helmet（安全帽）
  - head_with_helmet（佩戴安全帽的头部）
  - head（未佩戴安全帽的头部）
  - person_with_helmet（佩戴安全帽的人）
  - face（面部）
  - person_no_helmet（未佩戴安全帽的人）
  - person（人，不区分是否佩戴安全帽） |
| **适用模型** | YOLOv5, YOLOv8, Faster R-CNN 等目标检测模型 |
| **数据特点** | 高质量标注，适用于多种场景下的安全帽检测与人员识别 |

---
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/55f377ec3d374cfcbe90f31fb80a69e9.png)

### **类别列表（Class Names）**

| 类别索引 | 类别名称（中文） | 类别名称（英文） |
| :---: | :--- | :--- |
| 0 | 安全帽 | helmet |
| 1 | 佩戴安全帽的头部 | head_with_helmet |
| 2 | 未佩戴安全帽的头部 | head |
| 3 | 佩戴安全帽的人 | person_with_helmet |
| 4 | 面部 | face |
| 5 | 未佩戴安全帽的人 | person_no_helmet |
| 6 | 人（不区分是否佩戴安全帽） | person |

---

### **数据集目录结构建议**

请确保您的数据集按如下结构组织：

```
safety_helmet_dataset/
├── images/
│   ├── train/      # 训练集图片
│   ├── val/        # 验证集图片
│   └── test/       # 测试集图片（可选）
├── labels/
│   ├── train/      # 对应的 YOLO .txt 标注文件
│   ├── val/
│   └── test/
└── data.yaml       # 数据集配置文件
```

---![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/2b3687fb7c45463095479ffcf65334a0.png)


### **创建 `data.yaml` 配置文件**

在 `safety_helmet_dataset/` 目录下创建 `data.yaml` 文件：

```yaml
# data.yaml

# 类别数量
nc: 7

# 类别名称（按索引顺序）
names:
  - helmet
  - head_with_helmet
  - head
  - person_with_helmet
  - face
  - person_no_helmet
  - person

# 数据集路径（相对于运行脚本的路径）
train: ./safety_helmet_dataset/images/train
val: ./safety_helmet_dataset/images/val
test: ./safety_helmet_dataset/images/test  # 可选
```

---

✅ **总结**：  
该数据集专注于工业环境中的安全帽检测任务，包含多种细分类别，可用于构建工地或电力设施的安全监控系统。所有图像均已标注为 YOLO 格式，可以直接用于模型训练和验证。

![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/d0dbc8126c7b4e9e840a93824809fd2b.png)
1
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/c2660a5f8b1a47828b5a4d7c583b9d49.png)
使用 **YOLOv8** 进行训练的完整详细代码（基于 [Ultralytics YOLOv8](https://github.com/ultralytics/ultralytics) 框架），支持 7 类目标检测任务

---

## ✅ 一、环境安装

```bash
# 安装 Ultralytics YOLOv8
pip install ultralytics
```

> 官方文档：[https://docs.ultralytics.com](https://docs.ultralytics.com)

---

## ✅ 二、数据集目录结构

同学，请确保您的数据集按如下结构组织：

```
safety_helmet_dataset/
├── images/
│   ├── train/      # 训练图像（如 4000 张）
│   ├── val/        # 验证图像（如 500 张）
│   └── test/       # 测试图像（如 500 张）
├── labels/
│   ├── train/      # 对应的 YOLO .txt 标注文件
│   ├── val/
│   └── test/
└── data.yaml       # 数据集配置文件
```

---

## ✅ 三、创建 `data.yaml` 配置文件

在 `safety_helmet_dataset/` 目录下创建 `data.yaml` 文件：

```yaml
# data.yaml

# 类别数量
nc: 7

# 类别名称（按索引顺序）
names:
  - helmet
  - head_with_helmet
  - head
  - person_with_helmet
  - face
  - person_no_helmet
  - person

# 数据路径（相对或绝对路径）
train: ./safety_helmet_dataset/images/train
val: ./safety_helmet_dataset/images/val
test: ./safety_helmet_dataset/images/test  # 可选，用于最终评估
```

---

## ✅ 四、YOLOv8 完整训练代码（Python 脚本）

```python
from ultralytics import YOLO
import os

# ================== 1. 加载预训练模型 ==================
# 推荐使用轻量级模型用于边缘部署（如工地监控摄像头）
model = YOLO('yolov8s.pt')  # 可替换为 yolov8n.pt, yolov8m.pt 等

# ================== 2. 开始训练 ==================
print("🚀 开始训练工业安全帽检测模型...")

results = model.train(
    data='safety_helmet_dataset/data.yaml',  # 数据集配置文件路径
    epochs=100,                               # 训练轮数
    imgsz=640,                                # 输入图像大小
    batch=16,                                 # 批次大小（根据 GPU 显存调整，8~32）
    device=0,                                 # 使用 GPU 0，CPU 使用 None
    workers=4,                                # 数据加载线程数
    name='safety_helmet_yolov8s',             # 实验名称
    project='runs/train',                     # 结果保存路径
    exist_ok=False,                           # 禁止覆盖同名实验
    optimizer='AdamW',                        # 优化器选择
    lr0=0.01,                                 # 初始学习率
    momentum=0.937,
    weight_decay=0.0005,
    patience=15,                              # 早停耐心值
    amp=True,                                 # 自动混合精度（节省显存，加速训练）
    
    # 数据增强（适用于复杂工地场景）
    hsv_h=0.015,    # 色调增强
    hsv_s=0.7,      # 饱和度增强
    hsv_v=0.4,      # 明度增强
    degrees=10.0,   # 随机旋转
    translate=0.2,  # 平移
    scale=0.5,      # 缩放
    shear=2.0,      # 剪切
    flipud=0.0,     # 上下翻转（通常不需要）
    fliplr=0.5,     # 左右翻转（推荐开启）
    mosaic=1.0,     # Mosaic 增强
    mixup=0.1,      # MixUp 增强
)

print("✅ 训练完成！")
```

---

## ✅ 五、验证与测试

### 1. 在验证集上评估

```python
# 加载最佳模型
model = YOLO('runs/train/safety_helmet_yolov8s/weights/best.pt')

# 验证
metrics = model.val()
print(f"📊 mAP@0.5:         {metrics.box.map50:.3f}")
print(f"📊 mAP@0.5:0.95:    {metrics.box.map:.3f}")
print(f"📊 Precision:       {metrics.box.precision:.3f}")
print(f"📊 Recall:          {metrics.box.recall:.3f}")
```

### 2. 在测试集上评估（可选）

```python
# 使用 test 集进行最终评估
metrics_test = model.val(
    split='test',
    data='safety_helmet_dataset/data.yaml',
    imgsz=640,
    batch=16,
)
print(f"🧪 测试集 mAP@0.5: {metrics_test.box.map50:.3f}")
```

---

## ✅ 六、推理示例

### 单图推理

```python
results = model('test_image.jpg', conf=0.5)
results[0].show()  # 显示结果
results[0].save(filename='result.jpg')
```

### 视频或摄像头实时检测

```python
# 摄像头
results = model.predict(source=0, show=True, conf=0.5)

# 视频文件
results = model.predict(source='construction_site.mp4', save=True, conf=0.5)
```

---

## ✅ 七、模型导出（用于部署）

```python
# 导出为 ONNX（通用部署）
model.export(format='onnx', dynamic=True, opset=13)

# 导出为 TensorRT（NVIDIA GPU 高性能）
model.export(format='engine', half=True)  # FP16 推理

# 导出为 CoreML（iOS）
model.export(format='coreml')

# 导出为 TFLite（Android）
model.export(format='tflite')
```

---


