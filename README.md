<div align="center">

![Ppllocr](./ppllocr.svg)

![Logo](https://img.shields.io/badge/Ppllocr-YOLOv11-brightgreen?style=for-the-badge&logo=python)

[![License](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)
[![PyPI](https://img.shields.io/pypi/v/ppllocr)](https://pypi.org/project/ppllocr/)
[![Python](https://img.shields.io/badge/python-3.12-blue.svg)](https://www.python.org/)

![](https://www.luogu.com.cn/lg4/captcha)

**高性能 · 抗干扰 · 纯 ONNX 验证码识别引擎**

Designed by **Liveless** & **Zjyjoe** & **Colin1112** as C2027

</div>

---

## 简介

**Ppllocr** 是一个基于 **YOLOv11** 和 **ONNX Runtime** 构建的轻量级 OCR 库。

它专为解决**高难度验证码**。Ppllocr 在训练阶段引入了“地狱级”对抗样本生成技术（包括弹性形变、混沌轨迹干扰线、靶向切割、伪装笔画等），使其在面对严重扭曲和重度干扰的图像时，依然保持极高的识别精度。

目标上对标 [Ddddocr](https://github.com/sml2h3/ddddocr)，部分情况性能与准确度碾压 [Ddddocr](https://github.com/sml2h3/ddddocr)。

**尽管如此，具体效果看玄学。**

### 目前可以识别字符集

- 数字：`0123456789`
- 大写字母：`ABCDEFGHIJKLMNOPQRSTUVWXYZ`
- 小写字母：`abcdefghijklmnopqrstuvwxyz`
- 部分特殊字符`*/%@#`

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

> 经测试 cpu 推理已经足够快（\<120 ms），故不提供 gpu 推理。

## 快速开始

### 1\. 基础识别 (classification)

最简单的用法，直接获取识别出的字符串。

```python
from ppllocr import OCR

# 初始化 (自动加载内置模型)
ocr = OCR()

# 传入图片二进制数据 (bytes)
with open("captcha_sample.jpg", "rb") as f:
    img_bytes = f.read()

# 直接返回字符串，例如 "2a3B"
text = ocr.classification(img_bytes)

print(f"识别结果: {text}")
```

### 2\. 获取详细信息 (classification\_box)

如果你需要知道字符的位置（坐标）或置信度。

```python
from ppllocr import OCR

ocr = OCR()

with open("captcha_sample.jpg", "rb") as f:
    img_bytes = f.read()

# 返回 文本 和 详细信息列表
text, details = ocr.classification_box(img_bytes)

print(f"识别结果: {text}")

# details 结构示例:
# [
#   {'char': '2', 'conf': 0.98, 'box': [10.5, 5.0, 30.2, 45.1]},
#   {'char': 'a', 'conf': 0.95, 'box': [35.0, 8.0, 55.0, 48.0]},
#   ...
# ]
for char_info in details:
    print(f"字符: {char_info['char']}, 置信度: {char_info['conf']:.2f}, 坐标: {char_info['box']}")
```

### 3\. 网络图片与爬虫

在爬虫中，直接将 `requests` 获取的 `content` 传给 `classification` 即可。

```python
import requests
from ppllocr import OCR

ocr = OCR()

# 模拟从网络获取图片
url = "[https://www.luogu.com.cn/lg4/captcha](https://www.luogu.com.cn/lg4/captcha)"
img_bytes = requests.get(url).content

# 直接传入 bytes
text = ocr.classification(img_bytes)

print(f"验证码是: {text}")
```

### 4\. 动态调参

Ppllocr 允许你在推理时动态调整阈值，以平衡**召回率**与**准确率**：

```python
# conf: 置信度阈值 (默认 0.25)
# iou:  NMS 重叠阈值 (默认 0.45)
text = ocr.classification(open("hard_sample.jpg", "rb").read(), conf=0.25, iou=0.45)
```

## 贡献者

  - **Liveless**: 这个人用暴力数据解决了一切问题。
  - **Zjyjoe**: 感谢提供的 GPU 运行时。
  - **colin1112**: 万恶之源。

## 许可证

本项目采用 **MIT 许可证**。详情请参阅 [LICENSE](https://www.google.com/search?q=LICENSE) 文件。
