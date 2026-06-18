# 階段三：載具溝通語言與沙盒（Python 與 Docker） 🐍📦

結合 Python 基礎語法與 Docker 的隔離環境，讓學員理解如何避免開發過程中常遇到的「在我電腦上可以跑，但在別人的電腦/實機上就爆炸」的環境配置地獄。

---

## 📖 核心學習內容

### 1. Python 與物件導向 (OOP)
在 AUV 開發中，許多高階節點（如決策、視覺）都是以 Python 編寫。學員須掌握：
* **基本控制流**：變數、`if-else` 判斷、`for`/`while` 迴圈。
* **資料結構**：`list`, `dict`, `tuple` 的靈活運用。
* **物件導向程式設計 (OOP)**：
  * **類別 (Class)** 與 **物件 (Object)** 的概念（例如：將 AUV 本體封裝成一個類別，包含深度、航向等屬性與前進、下潛等方法）。

---

### 2. Docker 基礎與沙盒隔離
* **為什麼 AUV 需要 Docker？**
  * AUV 依賴的軟體庫非常複雜（ROS 2、OpenCV、NVIDIA CUDA、各種感測器驅動）。
  * 透過 Docker，我們可以將所有依賴打包進 **Image (映像檔)**，並在任意電腦上開啟 **Container (容器)** 運行，確保運作環境完全一致。
* **核心術語**：
  * **Image (映像檔)**：如同安裝光碟（唯讀）。
  * **Container (容器)**：如同安裝好正在運行的系統（可讀寫、互相隔離）。
  * **Dockerfile**：用來描述如何自動打包 Image 的配方表。
  * **Volume (資料卷映射)**：將實體主機的資料夾掛載到容器內，方便直接修改代碼而不需重新打包。

---

## 🛠️ 實作小專案：【水下推進器推力計算機（容器版）】

編寫一個 Python 程式，模擬計算 AUV 推進器的 PWM 輸出值，並將其打包在 Docker 容器內運行。

### 步驟一：撰寫 Python 程式碼
建立一個 `thruster_calc.py` 檔案：
```python
import sys

class ThrusterCalculator:
    def __init__(self, efficiency=0.85):
        self.efficiency = efficiency
        # 假設 PWM 基準為 1500us (停止)，前進上限為 1900us，後退下限為 1100us
        self.NEUTRAL_PWM = 1500

    def calculate_pwm(self, depth, target_speed):
        """
        簡易推力模擬公式：依據水深與目標速度計算推進器 PWM 訊號
        """
        # 隨深度增加，阻力變大，需要更多補償
        depth_drag_compensation = depth * 1.5
        
        # 推力與速度成正比
        raw_force = (target_speed * 100) + depth_drag_compensation
        pwm_offset = raw_force / self.efficiency
        
        target_pwm = self.NEUTRAL_PWM + pwm_offset
        # 限制 PWM 的物理安全區間
        target_pwm = max(1100, min(1900, target_pwm))
        return round(target_pwm)

if __name__ == "__main__":
    print("--- AUV 推進器推力計算系統 ---")
    try:
        depth = float(input("請輸入當前水深 (meters): "))
        speed = float(input("請輸入目標前進速度 (m/s): "))
    except ValueError:
        print("錯誤：請輸入有效的數字！")
        sys.exit(1)

    calc = ThrusterCalculator()
    pwm = calc.calculate_pwm(depth, speed)
    print(f"=> 輸出至推進器的模擬 PWM 訊號值為: {pwm} us")
```

---

### 步驟二：撰寫 Dockerfile
在同一目錄下建立一個名為 `Dockerfile` 的檔案（無副檔名）：
```dockerfile
# 1. 使用輕量級的 Python 映像檔作為基礎
FROM python:3.12-slim

# 2. 設定容器內的工作目錄
WORKDIR /app

# 3. 將本地的 python 檔案複製到容器內
COPY thruster_calc.py .

# 4. 設定容器啟動時預設執行的指令
CMD ["python", "thruster_calc.py"]
```

---

### 步驟三：打包與運行 Docker 映像檔
開啟終端機，執行以下指令：

```bash
# 1. 打包 Docker Image
docker build -t auv-thruster-calc:v1.0 .

# 2. 啟動並運行 Container (加上 -it 才能進行互動式輸入)
docker run -it --rm auv-thruster-calc:v1.0
```
成功執行後，您會看到程式要求您輸入水深與速度，並給出計算結果。這代表您的 Python 程式已經在與外面系統完全隔離的「沙盒」中成功運作了！
