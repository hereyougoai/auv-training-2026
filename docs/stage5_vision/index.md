# 階段五：載具之眼（視覺辨識模型訓練基礎） 👁️📷

水下環境極其複雜，水質混濁、折射與光線衰減都會影響影像。要讓 AUV 能夠在水下自主穿過閘門、撞擊標靶，視覺辨識是關鍵。本階段的重點在於建立**「資料收集 -> 標註 -> 訓練 -> 推論」**的完整 AI Pipeline 概念。

---

## 📖 核心學習內容

### 1. 資料集 (Dataset) 與標註 (Annotation)
* **垃圾進，垃圾出 (Garbage in, Garbage out)**：高品質的影像資料集是訓練成功的關鍵。
* **資料標註**：透過框選圖片中的目標物並標註類別（例如 `gate`, `buoy`）。
* **常用標註工具**：[CVAT](https://www.cvat.ai/) 或 [Roboflow](https://roboflow.com/)。我們通常導出為 **YOLO 格式**（包含圖片與對應的 `.txt` 座標標籤檔案）。

---

### 2. 物件偵測模型（以 YOLOv8 為例）與遷移學習
* **YOLO (You Only Look Once)**：目前最主流、速度最快的實時物件偵測模型，非常適合運算資源有限的 AUV 樹莓派端。
* **遷移學習 (Transfer Learning)**：我們不需要從頭訓練一個模型。我們可以使用 NVIDIA/COCO 資料集預訓練好的權重（Weight），只需餵入少量水下圖片，即可微調 (Fine-tune) 出能辨識水下物件的模型。

---

### 3. 模型推論 (Inference) 與效能評估
* **Inference**：指模型讀取未見過的圖片並預測其邊界框 (Bounding Box) 的過程。
* **評估指標**：
  * **mAP (Mean Average Precision)**：辨識精準度。
  * **FPS (Frames Per Second)**：每秒處理影格數。水下即時控制要求 FPS 至少要達到 `15 ~ 30` 以上，以免決策產生嚴重延遲。

---

## 🛠️ 實作小專案：【水下任務目標辨識】

利用階段一安裝的 **Docker + NVIDIA Container Toolkit** 環境，微調一個 YOLO 模型並用您的視訊鏡頭進行即時測試。

### 步驟一：準備開發容器
我們使用包含 GPU 支援的 Docker 來執行 YOLO 訓練，避免本地 Python 環境因為 CUDA 版本的複雜依賴而損壞。
```bash
# 啟動包含 GPU 與 X11 (視窗顯示) 支持的 PyTorch 容器
docker run -it --gpus all \
  -e DISPLAY=$DISPLAY \
  -v /tmp/.X11-unix:/tmp/.X11-unix \
  -v ~/auv_vision:/workspace \
  ultralytics/ultralytics:latest
```

---

### 步驟二：編寫訓練腳本 (`train.py`)
在專案中建立訓練程式：
```python
from ultralytics import YOLO

# 1. 載入預先訓練好的 YOLOv8 奈米版模型（輕量化，適合 AUV）
model = YOLO('yolov8n.pt')

# 2. 開始訓練 (指定您的 dataset 設定檔，例如從 Roboflow 下載的 data.yaml)
results = model.train(
    data='/workspace/dataset/data.yaml', 
    epochs=20,          # 訓練輪數
    imgsz=640,          # 影像大小
    device=0            # 使用第 0 號 GPU
)
```

---

### 步驟三：即時相機推論測試
訓練完成後，我們寫一個腳本讀取筆電視訊鏡頭，並使用剛剛訓練出來的權重檔 `best.pt` 進行即時框選：

```python
import cv2
from ultralytics import YOLO

# 載入自己訓練出的最佳模型
model = YOLO('runs/detect/train/weights/best.pt')

# 開啟實體視訊鏡頭 (0 代表預設鏡頭)
cap = cv2.VideoCapture(0)

while cap.isOpened():
    success, frame = cap.read()
    if not success:
        break

    # 進行模型推論
    results = model(frame)

    # 繪製辨識框並顯示在畫面上
    annotated_frame = results[0].plot()
    cv2.imshow("AUV Vision - Realtime Inference", annotated_frame)

    # 按下 'q' 鍵退出
    if cv2.waitKey(1) & 0xFF == ord('q'):
        break

cap.release()
cv2.destroyAllWindows()
```
拿著與訓練資料集中顏色形狀相同的物體（例如：紅色的水底標籤或可樂罐代替紅浮標）放在鏡頭前，觀察模型是否能夠精準框選！
