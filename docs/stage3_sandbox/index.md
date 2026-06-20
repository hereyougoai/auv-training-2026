# 階段三：載具溝通語言與沙盒（Python 與 Docker） 🐍📦

結合 Python 基礎語法與 Docker 的隔離環境，讓學員理解如何避免開發過程中常遇到的「在我電腦上可以跑，但在別人的電腦/實機上就爆炸」的環境配置地獄。

---

## 📖 核心學習內容

### 1. Python 與物件導向 (OOP)
在 AUV 開發中，許多高階節點（如決策、狀態估測、視覺辨識）都是以 Python 編寫。學員須掌握以下核心基礎：

#### 🐍 Python 基礎語法核心

##### 核心一：跟電腦說話與「記憶盒子」（輸出與變數）

寫程式的第一步，就是學會請電腦顯示文字，並讓電腦把資料「記下來」。

* **`print()` (印出)**：請電腦說話。
* **變數 (Variable)**：就像是在箱子上貼標籤，把資料裝進去。
* **`input()` (輸入)**：讓電腦問問題，並等待使用者打字回答。

```python
# 把使用者輸入的名字，裝進叫做 name 的箱子裡
name = input("請問你叫什麼名字？ ")

# 使用 f-string (在字串前加 f)，可以輕鬆把箱子裡的東西拿出來用
print(f"你好，{name}！歡迎來到 Python 的世界。")
```

---

##### 核心二：讓程式學會看情況辦事（條件判斷）

人生充滿選擇，程式也是。我們用 `if` (如果)、`elif` (不然如果)、`else` (否則) 來幫程式建立「岔路」。

* **關鍵概念**：程式會從上往下檢查，只要條件符合（True），就會執行那條路裡的動作，後面的就不管了。

```python
age = int(input("你今年幾歲？ "))  # int() 是把輸入的文字變成整數

if age >= 18:
    print("你已經成年，可以考駕照了！")
elif age >= 13:
    print("你是個青少年！")
else:
    print("你還是個小學生呢！")
```

> **排版魔法：** 注意到了嗎？`if` 下方的程式碼有**縮排（留白）**。Python 是靠縮排來判斷這段動作是不是屬於這個條件的，按一下 `Tab` 鍵就能縮排。

---

##### 核心三：偷懶的藝術（迴圈）

電腦最擅長做重複的苦差事。當你想把同一件事做很多次，或是檢查一整排資料時，就需要用到迴圈。

* **`for` 迴圈**：明確知道要跑幾次時使用。

```python
# range(3) 代表跑 3 次 (0, 1, 2)
for i in range(3):
    print("重要的事情說三遍！")
```

* **`while` 迴圈**：只要條件成立，就會一直跑下去（適合用在不知道何時會結束的狀況，例如：猜密碼）。

```python
password = ""
while password != "1234":
    password = input("請輸入密碼解鎖：")
print("解鎖成功！")
```

---

##### 核心四：資料的百寶袋（List 與 Dict）

當資料變多時，我們不可能為每個東西都準備一個獨立的箱子（變數），我們需要更大的容器。

* **串列 `list`**：就像是一排連號的置物櫃，用 `[]` 把東西包起來，按照順序排好。

```python
# 電腦數數是從 0 開始的！
fruits = ["蘋果", "香蕉", "橘子"]
print(fruits[0])  # 會印出 "蘋果"
```

* **字典 `dict`**：就像是通訊錄，有「標籤（Key）」和「內容（Value）」，用 `{}` 包起來。

```python
player = {
    "name": "勇者",
    "hp": 100
}
print(f"{player['name']} 剩下 {player['hp']} 滴血")
```

---

##### 核心五：程式的魔術道具（函式 Function）

當某段程式碼會重複被使用，我們可以用 `def` 把牠包裝成一個「函式」，就像是一個魔術道具，丟進原料（參數），就會吐出成品（回傳值）。

```python
# 定義一個限制推進器安全 PWM 的函式
def limit_pwm(pwm):
    if pwm > 1900:
        return 1900
    elif pwm < 1100:
        return 1100
    return pwm

# 使用它
safe_pwm = limit_pwm(2000)
print(f"安全限制後的 PWM 訊號為: {safe_pwm} us") # 會印出 1900
```

---

#### 💡 物件導向程式設計 (OOP) 進階觀念

物件導向程式設計是專案規模變大時最關鍵的代碼管理方式：
* **類別 (Class) 與 物件 (Object)**：類別是藍圖，物件是實例。例如，將 AUV 的「深度計」、「推進器」各自封裝成類別。
* **建構子 (`__init__`) 與 `self`**：建構子用於初始化物件狀態；`self` 代表物件實例本身，用於在類別內存取屬性與方法。
* **繼承 (Inheritance)**：子類別可繼承父類別的特徵並進行擴充，避免重複撰寫代碼。

#### 💡 AUV 物件導向 (OOP) 實用範例：感測器類別設計
請細讀以下程式碼，理解如何透過類別繼承來設計 AUV 的感測器系統：

```python
# 父類別：通用感測器
class Sensor:
    def __init__(self, name, interface="I2C"):
        self.name = name
        self.interface = interface
        self.is_connected = False

    def connect(self):
        self.is_connected = True
        print(f"📡 [{self.name}] 已透過 {self.interface} 介面成功連線！")

    def read_raw(self):
        # 定義介面，子類別必須實作
        raise NotImplementedError("子類別必須實作 read_raw 方法！")

# 子類別：壓力深度計，繼承自 Sensor
class PressureDepthSensor(Sensor):
    def __init__(self, name, interface="I2C", density=1025):
        # 呼叫父類別的建構子初始化 name 與 interface
        super().__init__(name, interface)
        self.water_density = density # 海水密度大約 1025 kg/m³

    # 實作父類別要求的 read_raw 方法
    def read_raw(self):
        # 模擬讀取到感測器的物理壓強 (單位: Pascal)
        return 109800 

    def get_depth(self):
        if not self.is_connected:
            print("❌ 錯誤：感測器尚未連線！")
            return None
        
        pressure = self.read_raw()
        g = 9.81
        # 簡易水深計算公式：深度 = 壓強差 / (密度 * g)
        # (這裡假設大氣壓為 101325 Pa)
        depth = (pressure - 101325) / (self.water_density * g)
        return round(depth, 2)

# 執行測試
if __name__ == "__main__":
    # 建立深度感測器物件
    depth_meter = PressureDepthSensor(name="Bar30 深度計", density=1025)
    
    # 進行連線與讀取
    depth_meter.connect()
    current_depth = depth_meter.get_depth()
    print(f"🌊 [{depth_meter.name}] 當前水深讀數為: {current_depth} 公尺")
```

---

### 2. Docker 基礎與沙盒隔離
* **為什麼 AUV 需要 Docker？**
  * AUV 依賴的軟體庫極其複雜（如 ROS 2、OpenCV、NVIDIA CUDA、各式特規感測器驅動）。
  * 透過 Docker，我們能將所有依賴與執行環境打包進 **Image (映像檔)**，並在任意電腦（不論是開發主機還是 AUV 實機）上開啟 **Container (容器)** 運行，徹底解決「環境不一致」的問題。
* **核心術語**：
  * **Image (映像檔)**：唯讀的範本，包含運行程式所需的所有檔案與設定。
  * **Container (容器)**：映像檔的運行實例，是一個相互隔離、可讀寫的安全沙盒。
  * **Dockerfile**：描述如何自動建立 Image 的文字配方表。
  * **Volume (資料卷映射)**：將本機的資料夾掛載到容器內部，方便直接修改代碼而不需重新打包。

#### 🐳 Docker 核心指令防呆對照表
| 指令 | 功能描述 | 常用範例 | 💡 實用提示 |
| :--- | :--- | :--- | :--- |
| **`docker build`** | 將 Dockerfile 打包成 Image | `docker build -t auv-app:v1.0 .` | `-t` 用於指定名稱與版本標籤（tag），結尾的 `.` 代表以當前目錄為上下文環境。 |
| **`docker run`** | 建立並啟動一個 Container | `docker run -it --rm auv-app:v1.0` | `-it` 啟用互動式終端機，`--rm` 在容器退出後自動刪除容器以釋放硬碟空間。 |
| **`docker ps`** | 列出當前**正在運行**的容器 | `docker ps -a` | 加上 `-a` 可以列出包含已停止的所有歷史容器。 |
| **`docker images`** | 列出本地所有的 Image | `docker images` | 可以看到映像檔名稱、Tag、ID 與佔用空間大小。 |
| **`docker rm [ID/Name]`**| 刪除已停止的容器 | `docker rm my-container` | 必須先停止運行的容器才能進行刪除。 |
| **`docker rmi [ID]`** | 刪除映像檔 | `docker rmi auv-app:v1.0` | 如果該 Image 有對應的容器存在（不論是否運行），須先刪除容器才能刪除 Image。 |

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
在與 `thruster_calc.py` 同一目錄下建立一個名為 `Dockerfile` 的檔案（注意：**無副檔名**）：
```dockerfile
# 1. 使用輕量級的 Python 官方映像檔作為基礎環境
FROM python:3.12-slim

# 2. 設定容器內的工作目錄為 /app (後續指令都會在此執行)
WORKDIR /app

# 3. 將本機目前的原始碼複製到容器的 /app 目錄中
COPY thruster_calc.py .

# 4. 設定容器啟動時的預設命令
CMD ["python", "thruster_calc.py"]
```

#### 📝 Dockerfile 語法詳細拆解
| 指令 | 作用說明 | 💡 防呆小提示 |
| :--- | :--- | :--- |
| **`FROM`** | 載入基礎環境映像檔 | AUV 開發中多採用官方輕量版（如 `-slim`、`-alpine`）或 ROS 官方映像檔，可大幅減少打包後體積。 |
| **`WORKDIR`**| 設定容器內部的預設目錄 | 相當於 Linux 的 `cd` 指令，後續的 `COPY`、`CMD` 都會在該目錄下進行。 |
| **`COPY`** | 複製檔案至容器內 | 格式為 `COPY <本地來源路徑> <容器目的路徑>`。推薦使用相對路徑。 |
| **`CMD`** | 容器啟動時執行的命令 | 格式通常為 JSON 陣列，例如 `["python", "script.py"]`。一個 Dockerfile 中只有最後一條 `CMD` 會生效。 |

---

### 步驟三：打包與運行 Docker 映像檔
開啟終端機，執行以下指令：

```bash
# 1. 打包 Docker Image (注意後面的句點 "." 不能漏掉，代表當前目錄)
docker build -t auv-thruster-calc:v1.0 .

# 2. 啟動並運行 Container (加上 -it 才能進行互動式輸入，--rm 則是在結束時自動刪除容器)
docker run -it --rm auv-thruster-calc:v1.0
```
成功執行後，您會看到程式要求您輸入水深與速度，並給出計算結果。這代表您的 Python 程式已經在與外面系統完全隔離的「沙盒」中成功運作了！

---

### ⚡ 進階技巧：使用 Volume 實現即時開發 (Hot-reload)
在實務開發 AUV 程式時，如果每次修改了代碼（例如調整 `thruster_calc.py` 的計算公式），都需要重新執行 `docker build` 打包映像檔，那開發效率會非常低下。

這時，我們可以使用 **Volume（資料卷映射）** 功能，將本機的資料夾直接掛載到容器中。這樣一來，我們在編輯器中修改代碼並儲存，容器內部的代碼也會隨之即時更新，省去重複 build 的時間！

請確保終端機路徑在專案目錄下，並執行以下命令：

**Windows (PowerShell) 系統：**
```powershell
docker run -it --rm -v ${PWD}:/app auv-thruster-calc:v1.0
```

**Linux / macOS 系統：**
```bash
docker run -it --rm -v $(pwd):/app auv-thruster-calc:v1.0
```

> **🔍 指令解析：**
> * `-v ${PWD}:/app`：代表將本機的當前路徑映射到容器內的 `/app` 目錄。
> * 現在，您可以嘗試在編輯器中修改本機 `thruster_calc.py` 的文字輸出（例如將 `--- AUV 推進器推力計算系統 ---` 改成別的字眼）並存檔，接著再次直接運行上面的指令，您會發現容器輸出的文字已經同步改變，完全不需要重新打包！這就是 Volume 映射的強大之處。
