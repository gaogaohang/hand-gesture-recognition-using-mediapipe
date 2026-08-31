# 搭建指南（Windows + Python 3.10/3.11）

## 1. 创建虚拟环境

```powershell
git clone -b air-canvas https://github.com/gaogaohang/hand-gesture-recognition-using-mediapipe.git
cd hand-gesture-recognition-using-mediapipe

py -3.11 -m venv .venv
.venv\Scripts\activate

python -m pip install --upgrade pip
pip install -r requirements.txt
```

说明：新版 mediapipe（0.10.x）仍保留本仓库使用的 `mp.solutions.hands` 接口，代码无需改动。
只跑推理的话 tensorflow 可以暂不装，MLP 分类器走的是 TFLite，体积很小，CPU 足够。

## 2. 跑通原版

```powershell
python app.py
```

默认手势集（无需训练即可识别）：

| 手势 | 标签 | 我们的用途 |
|------|------|-----------|
| 张开手掌 | Open | 待定 |
| 握拳 | Close | 待定 |
| 食指伸出 | Pointer | 待定 |
| OK 手势 | OK | 待定 |

窗口左上角显示 FPS，食指伸出时（Pointer）会显示指尖轨迹圆圈。

## 3. 按键说明

| 按键 | 作用 |
|------|------|
| ESC | 退出 |
| n | 普通模式（不采集数据） |
| k | 采集静态手势关键点数据（配合数字键 0-9 指定类别，追加到 keypoint.csv） |
| h | 采集手指轨迹数据（配合数字键 0-9，追加到 point_history.csv） |

## 4. 训练自定义手势流程

1. `python app.py`，按 `k` 进入采集模式，按住数字键（如 4）比划对应手势，采集几百帧
2. 在 `model/keypoint_classifier/keypoint_classifier_label.csv` 里加一行标签名
3. 跑 `keypoint_classification.ipynb` 训练，生成新的 TFLite 模型
4. 重启 `app.py` 即可识别新手势

## 5. 性能说明

本项目推理全程 CPU 即可流畅运行（MediaPipe 手部追踪 + 两个微型 MLP），
3050 暂时用不上。后续叠加 YOLO 人检测或 3D 重建时再启用 GPU。
