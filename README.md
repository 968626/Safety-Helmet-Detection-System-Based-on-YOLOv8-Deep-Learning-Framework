# 安全帽检测系统 - 详细说明文档
# 源码获取：https://mbd.pub/o/bread/YZWXmZdwZA==
## 📋 项目概述

基于YOLOv8深度学习框架的安全帽检测系统，专为工业安全监控设计。该系统能够实时检测图片、视频和摄像头流中的安全帽佩戴情况，提供准确的统计分析和可视化结果。

## 🎯 核心功能

### 1. 多模式检测功能
- **图片检测**: 支持JPG、JPEG、PNG、BMP格式图片文件检测
- **视频检测**: 支持MP4、AVI等视频文件逐帧分析
- **实时摄像头**: 调用本地摄像头进行实时流检测
- **批量处理**: 支持多文件批量检测处理

### 2. 智能统计分析
- **实时计数**: 动态统计总检测数量和各类别数量
- **分类统计**: 分别统计"未戴安全帽"和"已戴安全帽"数量
- **历史记录**: 保存检测历史记录，支持数据导出
- **性能监控**: 实时显示处理速度和资源占用情况

### 3. 结果管理功能
- **结果保存**: 自动保存标注后的检测结果图片
- **导出功能**: 支持检测统计数据导出为CSV格式
- **历史查看**: 提供检测历史记录查看功能
- **模型管理**: 支持自定义模型加载和切换

## 🏗️ 系统架构

### 技术架构图
```
┌─────────────────────────────────────────────────┐
│                 应用层 (Application)             │
├─────────────────────────────────────────────────┤
│ 图形界面 (Tkinter)   │  业务逻辑   │  数据管理  │
└─────────────────────────────────────────────────┘
┌─────────────────────────────────────────────────┐
│                服务层 (Service)                  │
├─────────────────────────────────────────────────┤
│  检测服务   │  统计服务   │  文件服务   │ 模型服务 │
└─────────────────────────────────────────────────┘
┌─────────────────────────────────────────────────┐
│                核心层 (Core)                    │
├─────────────────────────────────────────────────┤
│        YOLOv8深度学习引擎 (Ultralytics)         │
└─────────────────────────────────────────────────┘
┌─────────────────────────────────────────────────┐
│                数据层 (Data)                    │
├─────────────────────────────────────────────────┤
│   模型文件   │  配置文件   │  数据集   │ 结果文件  │
└─────────────────────────────────────────────────┘
```

### 文件结构详解

```
ultralytics-main-master/
├── 📁 核心程序文件
│   ├── Main.py              # 主程序 - 图形界面检测系统 (350+行)
│   ├── test.py              # 测试程序 - 基础检测功能
│   ├── train.py             # 训练程序 - 模型训练
│   └── safehat.yaml         # 数据集配置文件
│
├── 📁 模型文件
│   ├── best.pt             # 训练好的最佳模型权重
│   └── yolov8n.pt          # YOLOv8基础预训练模型
│
├── 📁 数据集
│   └── css-data/
│       ├── train/          # 训练集 (1200+张图片)
│       │   ├── images/     # 训练图片
│       │   └── labels/     # YOLO格式标注文件
│       ├── valid/          # 验证集 (300+张图片)
│       │   ├── images/     # 验证图片
│       │   └── labels/     # 验证标注
│       └── test/           # 测试集 (300+张图片)
│           ├── images/     # 测试图片
│           └── labels/     # 测试标注
│
├── 📁 运行结果
│   └── runs/
│       └── detect/
│           ├── train3/     # 第三次训练结果
│           └── train32/    # 第32次训练结果
│
├── 📁 依赖环境
│   └── venv/               # Python虚拟环境
│       ├── Scripts/        # 可执行文件
│       ├── Lib/            # 库文件
│       └── pyvenv.cfg     # 环境配置
│
└── 📁 文档资源
    ├── README.md           # 项目说明文档
    ├── 项目详细说明文档.md  # 本详细文档
    └── 截图/              # 系统截图
        ├── 1.png
        ├── 2.png
        └── 3.png
```

## 🔧 技术实现细节

### 1. 深度学习模型

**模型架构**: YOLOv8 (You Only Look Once version 8)
- **主干网络**: CSPDarknet53
- **颈部网络**: PANet (Path Aggregation Network)
- **检测头**: Decoupled Head
- **激活函数**: SiLU (Sigmoid Linear Unit)

**模型参数**:
- 输入尺寸: 640x640 pixels
- 类别数: 2 (No_SafetyHat, SafetyHat)
- 锚点框: 自动学习
- 优化器: AdamW

### 2. 图形界面设计

**界面框架**: Tkinter
- **主窗口**: 800x600 分辨率
- **布局管理**: Grid 网格布局
- **组件类型**: 
  - Frame 框架容器
  - Button 功能按钮
  - Label 信息标签
  - Canvas 图像显示
  - Menu 菜单栏

**界面分区**:
1. **顶部功能区**: 检测模式选择按钮
2. **中部显示区**: 图像/视频显示画布
3. **右侧控制区**: 参数设置和模型管理
4. **底部状态区**: 统计信息和系统状态

### 3. 核心算法流程

```python
# 检测流程伪代码
def detection_pipeline(input_source):
    # 1. 预处理
    image = preprocess(input_source)
    
    # 2. 模型推理
    results = model.predict(image)
    
    # 3. 后处理
    detections = postprocess(results)
    
    # 4. 可视化标注
    annotated_image = visualize(detections, image)
    
    # 5. 统计更新
    update_statistics(detections)
    
    # 6. 结果显示
    display_results(annotated_image)
    
    return annotated_image, detections
```

## 📊 性能指标

### 检测性能
| 指标 | 数值 | 说明 |
|------|------|------|
| 推理速度 | 54.5ms/帧 | GPU加速下的单帧处理时间 |
| 预处理 | 2.0ms | 图像标准化和缩放 |
| 后处理 | 1.0ms | 结果解析和非极大抑制 |
| 准确率 | >95% | 在测试集上的mAP值 |
| 召回率 | >93% | 目标检测的召回性能 |

### 系统资源
| 资源类型 | 占用情况 | 优化建议 |
|----------|----------|----------|
| GPU内存 | ~2GB | 适合大多数消费级显卡 |
| CPU占用 | 15-25% | 多线程优化良好 |
| 内存占用 | 1-2GB | 动态内存管理 |
| 磁盘空间 | 500MB+ | 主要为模型文件占用 |

## 🚀 安装部署指南

### 环境要求
- **操作系统**: Windows 10/11, Linux, macOS
- **Python版本**: 3.8, 3.9, 3.10, 3.11
- **硬件要求**: 
  - CPU: 4核心以上
  - 内存: 8GB+ 
  - GPU: NVIDIA显卡 (可选，推荐)
  - 存储: 1GB可用空间

### 依赖安装

**核心依赖包**:
```bash
# 深度学习框架
pip install ultralytics

# 图形界面
pip install tkinter

# 图像处理
pip install opencv-python
pip install pillow

# 数据处理
pip install numpy
pip install pandas

# 其他工具
pip install matplotlib
pip install seaborn
```

**完整requirements.txt**:
```
ultralytics==8.0.0
opencv-python==4.5.4.60
pillow==9.0.0
numpy==1.21.0
pandas==1.3.0
torch==1.13.0
torchvision==0.14.0
matplotlib==3.5.0
seaborn==0.11.0
```

### 部署步骤

1. **环境准备**
```bash
# 创建虚拟环境
python -m venv venv

# 激活环境 (Windows)
venv\Scripts\activate

# 激活环境 (Linux/Mac)
source venv/bin/activate
```

2. **安装依赖**
```bash
# 安装核心依赖
pip install -r requirements.txt

# 或者逐个安装
pip install ultralytics opencv-python pillow
```

3. **模型准备**
```bash
# 确保best.pt模型文件存在
# 如果不存在，可以运行训练程序
python train.py
```

4. **运行系统**
```bash
# 启动图形界面
python Main.py
```

5. **验证安装**
```bash
# 运行测试程序
python test.py
```

## 📖 使用教程

### 基本操作流程

1. **启动程序**
   - 双击运行 `Main.py` 或命令行执行 `python Main.py`
   - 系统加载模型文件 `best.pt`
   - 初始化图形界面组件

2. **选择检测模式**
   - 点击"图片检测": 选择单张图片文件
   - 点击"视频检测": 选择视频文件
   - 点击"摄像头检测": 启动摄像头实时检测

3. **查看检测结果**
   - 主画布显示标注后的图像
   - 安全帽用绿色框标注
   - 未戴安全帽用红色框标注
   - 显示置信度分数

4. **保存结果**
   - 点击"保存结果"按钮
   - 选择保存路径和文件名
   - 自动保存标注图片和统计信息

### 高级功能使用

**自定义模型加载**:
```python
# 在Main.py中修改模型路径
model = YOLO("custom_model.pt")  # 替换为自定义模型
```

**参数调整**:
```python
# 调整检测置信度阈值
model.conf = 0.6  # 默认0.5，提高可减少误检

# 调整IoU阈值
model.iou = 0.5   # 默认0.45，提高可减少重叠检测
```

**批量处理**:
```python
# 批量处理图片文件夹
import os
folder_path = "path/to/images"
for img_file in os.listdir(folder_path):
    if img_file.endswith(('.jpg', '.png')):
        results = model(os.path.join(folder_path, img_file))
```

## 🔍 代码详解

### Main.py 核心代码解析

#### 1. 主程序结构
```python
class SafetyHatDetector:
    def __init__(self):
        # 初始化图形界面
        self.root = tk.Tk()
        self.setup_ui()
        
        # 初始化YOLO模型
        self.model = YOLO("best.pt")
        
        # 初始化统计变量
        self.detection_results = []
        self.total_detections = 0
        self.class_counts = {'No_SafetyHat': 0, 'SafetyHat': 0}
```

#### 2. 界面初始化
```python
def setup_ui(self):
    # 创建主框架
    main_frame = tk.Frame(self.root)
    main_frame.pack(fill=tk.BOTH, expand=True)
    
    # 顶部按钮区域
    button_frame = tk.Frame(main_frame)
    button_frame.pack(pady=10)
    
    # 检测功能按钮
    self.btn_image = tk.Button(button_frame, text="图片检测", command=self.detect_image)
    self.btn_video = tk.Button(button_frame, text="视频检测", command=self.detect_video)
    self.btn_camera = tk.Button(button_frame, text="摄像头检测", command=self.detect_camera)
    
    # 图像显示区域
    self.canvas = tk.Canvas(main_frame, bg='white')
    self.canvas.pack(fill=tk.BOTH, expand=True, padx=10, pady=10)
    
    # 状态信息区域
    status_frame = tk.Frame(main_frame)
    status_frame.pack(fill=tk.X, padx=10, pady=5)
    
    self.status_label = tk.Label(status_frame, text="就绪")
    self.statistics_label = tk.Label(status_frame, text="总检测数: 0")
```

#### 3. 检测功能实现
```python
def detect_image(self):
    """图片检测功能"""
    file_path = filedialog.askopenfilename(
        filetypes=[("图片文件", "*.jpg *.jpeg *.png *.bmp")]
    )
    
    if file_path:
        # 执行YOLO检测
        results = self.model(file_path)
        
        # 获取标注图像
        annotated_frame = results[0].plot()
        
        # 更新统计信息
        self.update_statistics(results)
        
        # 显示结果
        self.display_image(annotated_frame)
```

#### 4. 统计信息更新
```python
def update_statistics(self, result):
    """更新检测统计信息"""
    total_detections = 0
    class_counts = {'No_SafetyHat': 0, 'SafetyHat': 0}
    
    # 处理检测结果
    if isinstance(result, list):  # 多帧结果
        for frame in result:
            if hasattr(frame, 'boxes') and frame.boxes is not None:
                for box in frame.boxes:
                    class_id = int(box.cls[0])
                    class_name = self.model.names[class_id]
                    class_counts[class_name] += 1
                    total_detections += 1
    else:  # 单帧结果
        if hasattr(result, 'boxes') and result.boxes is not None:
            for box in result.boxes:
                class_id = int(box.cls[0])
                class_name = self.model.names[class_id]
                class_counts[class_name] += 1
                total_detections += 1
    
    # 更新全局统计
    self.total_detections += total_detections
    for cls, count in class_counts.items():
        self.class_counts[cls] += count
    
    # 更新界面显示
    self.update_status_display()
```

