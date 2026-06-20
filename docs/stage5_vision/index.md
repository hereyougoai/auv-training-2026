# 階段五：視覺辨識模型訓練基礎

水下環境極其複雜，水質混濁、折射與光線衰減都會影響影像。要讓 AUV 能夠在水下自主穿過閘門、撞擊標靶，視覺辨識是關鍵。本階段的重點在於建立**「資料收集 -> 標註 -> 訓練 -> 推論」**的完整 AI Pipeline 概念。

---

### 🎥 影像辨識基礎課前學習

在正式進入實作之前，建議學員先觀看以下 3Blue1Brown 的經典科普影片，理解神經網路 (Neural Network) 與深度學習的核心運作原理，這對於理解模型如何透過特徵圖譜來「認出」水下目標物非常有幫助：

* **推薦影片**：[But what is a neural network? | Chapter 1, Deep learning](http://www.youtube.com/watch?v=aircAruvnKk) (由 3Blue1Brown 提供，附有中文字幕)
* **核心學習要點**：
  - 了解什麼是神經元 (Neuron) 以及它們如何被層層堆疊。
  - 理解權重 (Weight) 與偏差 (Bias) 的基本概念。
  - 明白輸入一張影像（例如 28x28 像素的影像）是如何轉化成數值在神經網路中傳遞，進而輸出預測結果。

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

