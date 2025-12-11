<div align="center">

![Ppllocr](./ppllocr.svg)

![Logo](https://img.shields.io/badge/Ppllocr-YOLOv11-brightgreen?style=for-the-badge&logo=python)

[![License](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)
[![PyPI](https://img.shields.io/pypi/v/ppllocr)](https://pypi.org/project/ppllocr/)
[![Python](https://img.shields.io/badge/python-3.12-blue.svg)](https://www.python.org/)

**高性能 · 抗干扰 · 纯 ONNX 验证码识别引擎**

Designed by **Liveless** & **Zjyjoe** & **Colin1112** as C2027

</div>

---

## 简介

**Ppllocr** 是一个基于 **YOLOv11** 和 **ONNX Runtime** 构建的轻量级 OCR 库。

它专为解决**高难度验证码**。Ppllocr 在训练阶段引入了“地狱级”对抗样本生成技术（包括弹性形变、混沌轨迹干扰线、靶向切割、伪装笔画等），使其在面对严重扭曲和重度干扰的图像时，依然保持极高的识别精度。

目标上对标 [Ddddocr](https://github.com/sml2h3/ddddocr)。

**尽管如此，具体效果看玄学。**

## 核心特性

- **极致轻量**：移除 PyTorch 笨重依赖，核心仅依赖 `onnxruntime` 和 `numpy`，速度毫秒级。
- **抗干扰强**：对抗网格遮罩、随机贝塞尔干扰线、鱼眼/波浪扭曲（尤其针对洛谷）。
- **开箱即用**：内置训练好的高性能 ONNX 模型，安装即用，无需额外下载权重。
- **Web 友好**：原生支持 `bytes` 流输入，完美适配爬虫。

## 安装

通过 PyPI 直接安装（推荐）：

```bash
pip install ppllocr
````

或者从源码安装：

```bash
git clone [https://github.com/gitpetyr/ppllocr.git](https://github.com/gitpetyr/ppllocr.git)
cd ppllocr
pip install .
```

> **注意**：默认安装的是 CPU 版 `onnxruntime`。如果你拥有 NVIDIA 显卡并希望获得更快的推理速度，请手动安装 GPU 版：
> `pip install onnxruntime-gpu`

## 快速开始

```python
from ppllocr import OCR

# 初始化 (自动加载内置模型)
ocr = OCR()

# 传入图片路径
text, details = ocr.predict(open("captcha_sample.jpg", "rb").read())

print(f"识别结果: {text}")
# details 包含每个字符的置信度和坐标
# print(details) 
```

在爬虫或 Web API 场景中，你通常直接持有图片的二进制数据 (`bytes`)，无需落地存文件。

```python
import requests
from ppllocr import OCR

ocr = OCR()

# 模拟从网络获取图片
url = "https://www.luogu.com.cn/lg4/captcha"
img_bytes = requests.get(url).content

# 直接传入 bytes
text, _ = ocr.predict(img_bytes)

print(f"验证码是: {text}")
```

Ppllocr 允许你在推理时动态调整阈值，以平衡**召回率**与**准确率**：

```python
# conf: 置信度阈值 (默认 0.25)
# iou:  NMS 重叠阈值 (默认 0.45)
text, _ = ocr.predict(open("hard_sample.jpg", "rb").read(), conf=0.25, iou=0.45)
```

## 贡献者

  - **Liveless**: 这个入用暴力数据解决了一切问题。
  - **Zjyjoe**: 感谢提供的 GPU 运行时。
  - **Colin1112**: 万恶之源。

## 许可证

本项目采用 **MIT 许可证**。详情请参阅 [LICENSE](LICENSE) 文件。