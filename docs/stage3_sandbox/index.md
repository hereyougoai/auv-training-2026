# 階段三：Python 與 Docker 

結合 Python 基礎語法與 Docker 的隔離環境，讓學員理解如何避免開發過程中常遇到的「在我電腦上可以跑，但在別人的電腦/實機上就爆炸」的環境配置問題。

---

## 📦 Python 開發環境與虛擬環境 (venv) 建立指南

以下是從零開始的完整流程，我將它分為**準備期**與**實戰期**，只要照著順序做，就能順利建立標準的 Python 開發環境。

### 階段零：準備工作（只需做一次）

要使用 `venv`（Python 內建的虛擬環境模組），你唯一需要安裝的就是 **Python 本身**。

#### 1. 檢查是否已經安裝 Python

打開你的終端機（Windows 的命令提示字元 CMD / Mac 的 Terminal），輸入以下指令：

```bash
python --version
```

*(Mac 或 Linux 用戶如果出現錯誤，請改輸入 `python3 --version`)*

* **如果有跑出版本號且為 Python 3.12.10（或相容的 3.12.x 版本）：** 恭喜你，廚房已經蓋好了，請直接跳到「階段一」。
* **如果不符合版本、顯示「找不到指令」或錯誤：** 代表你需要安裝指定版本的 Python。

#### 2. 安裝 Python 3.12.10 (若尚未安裝或版本不符)

* 請至 [Python 3.12.10 官方下載頁面](https://www.python.org/downloads/release/python-31210/)，依據您的作業系統下載對應的安裝程式（例如 Windows 用戶下載 `Windows installer (64-bit)`）。
* **⚠️ Windows 用戶的生死關鍵：** 點開安裝檔後，在第一個畫面**務必勾選「Add Python to PATH」**（將 Python 加入環境變數），然後才點擊 Install Now。如果沒勾這個，你的電腦會找不到 Python，後續指令都會失敗！

---

### 階段一：建立專案與虛擬環境（每次開新專案都要做）

準備好 Python 後，我們就可以開始建立獨立的專案了。

#### 1. 建立專案資料夾

在你的電腦裡開一個新的資料夾，用來放這次專案的所有程式碼。在終端機中，切換到那個資料夾裡面：

```bash
# 建立資料夾（假設叫做 my_project）
mkdir my_project

# 進入該資料夾
cd my_project
```

#### 2. 建立虛擬環境 (便當盒)

在專案資料夾內，輸入以下指令來建立虛擬環境。我們習慣將這個環境資料夾命名為 `venv`：

```bash
# Windows
python -m venv venv

# Mac / Linux
python3 -m venv venv
```

*(執行後，你會看到資料夾裡多了一個叫 `venv` 的子資料夾，裡面裝了獨立的 Python 執行檔和套件庫。)*

---

### 階段二：啟動與開發（每次寫程式前都要做）

#### 1. 啟動虛擬環境

這是新手最常忘記的步驟。不啟動的話，你安裝的套件還是會跑到全域環境（大鍋子）裡。

* **Windows (CMD):**
```cmd
venv\Scripts\activate.bat
```

* **Mac / Linux:**
```bash
source venv/bin/activate
```

**💡 成功標誌：** 啟動後，你終端機輸入指令的地方，最前面會多出一個 `(venv)`，代表你已經成功進入隔離空間！

#### 2. 安裝需要的套件

現在你可以盡情安裝專案需要的工具，它們都只會存在這個 `venv` 裡面。例如：

```bash
pip install requests pandas
```

#### 3. 開始寫程式

你可以打開 VS Code、PyCharm 或任何編輯器，開始撰寫你的 `.py` 檔案並執行。

---

### 階段三：收尾與備份（專案告一段落時做）

#### 1. 記錄套件清單（產生菜單）

當你的程式寫得差不多，準備傳給別人或推送到 GitHub 前，請匯出你這個環境裝了哪些套件：

```bash
pip freeze > requirements.txt
```

*(這會在資料夾產生一個 `requirements.txt` 文字檔，裡面記錄了所有套件與精確的版本號。)*

#### 2. 關閉虛擬環境

今天工作結束，或者你要切換到其他專案時，只要下達這個指令就能退出隔離環境：

```bash
deactivate
```

*(前面的 `(venv)` 會消失，你又回到了電腦的正常狀態。)*

---

**總結日常開發的循環：**
進入資料夾 -> `啟動虛擬環境` -> 寫程式/裝套件 -> `deactivate 離開`。

---

## 📖 核心學習內容

### 一、 Python 與物件導向 (OOP)
在 AUV 開發中，許多高階節點（如決策、狀態估測、視覺辨識）都是以 Python 編寫。學員須掌握以下核心基礎：

####  Python 基礎語法核心

##### 核心一：輸出與輸入變數

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

##### 核心二：條件判斷

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

##### 核心三：迴圈

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

##### 核心四：List 與 Dict

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

##### 核心五：函式 Function

當某段程式碼會重複被使用，我們可以用 `def` 把牠包裝成一個「函式」，就像是一個魔術道具，丟進原料（參數），就會輸出成品（回傳值）。

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

##### 💡 Python 簡短與高效語法 (If / For 篇)

在實際的開發或軟體企業中，常用以下幾種簡短且高讀寫效率的寫法，來簡化控制流程（特別是 `if` 和 `for`）：

* **1. 三元運算子 (Ternary Operator) —— 單行 `if-else`**
  * **寫法**：`值 = A if 條件 else B`
  * **範例**：
    ```python
    status = "normal"
    # 傳統寫法需要 4 行，簡短寫法只需 1 行：
    pwm = 1500 if status == "normal" else 1100
    ```
  * **實務應用**：常用於為感測器狀態快速設定「安全上限」或「預設回傳值」。

* **2. 列表生成式 (List Comprehension) —— 單行過濾與轉換**
  * **寫法**：`新清單 = [對每個元素的操作 for 元素 in 舊清單 if 條件]`
  * **範例**：
    ```python
    # 過濾出所有大於 0 的有效感測數據
    raw_data = [1.2, -0.5, 3.4, -9.9]
    valid_data = [d for d in raw_data if d > 0] # 得到 [1.2, 3.4]
    ```
  * **實務應用**：在 AUV 的聲納或壓力數據處理中，快速過濾噪訊或錯誤數值。

* **3. `enumerate()` 函數 —— 同時遍歷索引與元素**
  * **範例**：
    ```python
    thrusters = ["左推進器", "右推進器", "垂直推進器"]
    # i 會自動計數 (從 0 開始)，不需手動建立 i = i + 1
    for i, name in enumerate(thrusters):
        print(f"推進器編號 {i} 號: {name}")
    ```
  * **實務應用**：需要同時知道感測器的「物理排列順序」與「感測器名稱」時必備。

* **4. `zip()` 函數 —— 同步遍歷多個清單**
  * **範例**：
    ```python
    sensors = ["深度計", "聲納", "陀螺儀"]
    status = ["正常", "異常", "正常"]
    # 同時配對兩個清單中對應位置的元素
    for name, stat in zip(sensors, status):
        print(f"{name} 當前狀態: {stat}")
    ```
  * **實務應用**：將感測器的硬體清單與其實時檢測狀態配對輸出。

---

#### 物件導向程式設計 (OOP) 

物件導向程式設計是專案規模變大時最關鍵的代碼管理方式。以下是為新手準備的核心觀念與實例：

##### 1. 為什麼需要物件導向 (OOP)？
**OOP 的功能在於「打包」**：它允許我們把「資料（狀態）」和「功能（動作）」打包在一起，做成一個專屬的「模具」。

---

##### 2. 核心一：模具 (Class) 與 實體 (Object)
* **類別 `Class` (模具)**：這是一張藍圖或模具，它定義了東西「應該長怎樣」，但它只是概念，不能直接拿來用。
* **物件 `Object` (實體)**：透過藍圖實際打造出來的東西。

> 💡 **比喻：** `Class` 是「汽車設計圖」，`Object` 是馬路上真正在跑的「Toyota」和「Honda」。

---

##### 3. 核心二：什麼是建構子 (Constructor)？為什麼要有 `__init__`？
這是所有新手第一個面臨的大魔王！請把它想成是「出生證明與出廠設定」。

當我們用模具把物件生出來的那一「瞬間」，電腦需要知道：這隻新怪物叫什麼名字？牠一出生有多少血量？
在 Python 裡，負責處理「出生那一瞬間」的特殊機制，就叫做**建構子 (Constructor)**，寫法固定為 `__init__` (左右各兩條底線，是 initialization 初始化的縮寫)。

##### 那個無所不在的 `self` 是什麼？
`self` 的字面意思就是「我自己」。
同一個模具會產出成千上萬個物件，當程式在執行時，它必須知道現在是在幫「哪一個」物件設定血量。`self` 就是用來指名道姓說：「把名字設定給**現在正在出生的這個物件自己**」。

##### 程式碼實戰：
讓我們直接看Code，請試著一行一行閱讀註解：

```python
# 1. 建立一個叫做 Monster (怪物) 的模具
class Monster:

    # 2. 建構子 (Constructor)：怪物出生的那一瞬間會自動執行這裡
    def __init__(self, given_name, given_element):
        # self.XXX 就是把資料綁定在「這隻怪物自己」身上
        self.name = given_name       # 把傳進來的名字，綁定給自己
        self.element = given_element # 把傳進來的屬性 (火、水等)，綁定給自己
        self.hp = 100                # 出廠預設值：每隻怪物剛出生都是 100 滴血

    # 3. 方法 (Method)：這隻怪物可以做什麼動作 (動詞)
    def cry(self):
        # 使用自己的名字來打招呼
        print(f"吼！我是 {self.name}，我是 {self.element} 屬性的怪物！")

    def take_damage(self, damage):
        self.hp = self.hp - damage
        print(f"{self.name} 受到了 {damage} 點傷害，剩下 {self.hp} 滴血。")
```

---

##### 4. 核心三：開始量產！(使用模具)
模具寫好後，我們就可以在工廠裡製造怪物了。

```python
# 創造第一隻怪物 (這時會偷偷呼叫 __init__，把 "小火龍" 和 "火" 傳進去)
monster1 = Monster("小火龍", "火")

# 創造第二隻怪物
monster2 = Monster("傑尼龜", "水")

# 讓怪物做出動作 (呼叫方法)
monster1.cry()
# 輸出: 吼！我是 小火龍，我是 火 屬性的怪物！

monster2.cry()
# 輸出: 吼！我是 傑尼龜，我是 水 屬性的怪物！

# 讓小火龍扣血
monster1.take_damage(20)
# 輸出: 小火龍 受到了 20 點傷害，剩下 80 滴血。
```

> **新手常見誤區釐清：**
> 你會發現，我們在呼叫 `monster1.cry()` 的時候，括號裡面是空的，**不需要把 `self` 傳進去**。因為 Python 非常聰明，當你寫 `monster1.cry()` 時，它會自動把 `monster1` 當作 `self` 偷偷塞進去執行！

---

##### 5. 繼承 (Inheritance) 
當新手理解了「模具（Class）」的概念後，很快就會遇到一個問題：**如果我想做一個「進階版」的模具，難道要把基礎模具的程式碼全部重抄一遍嗎？** 這時就要用到「繼承」了！

##### 為什麼需要「繼承」？
工程師最大的美德就是「懶惰」—— 我們不喜歡寫重複的程式碼。

想像一下，在我們的怪獸工廠裡，除了普通怪獸，我們現在要生產一隻「魔王 (Boss)」。
魔王也是怪獸，所以牠也有名字、血量，也會受傷；但魔王有普通怪獸沒有的特權，例如牠有「護盾值」，還會放「大絕招」。

與其從頭為魔王寫一個全新的模具，我們不如**讓魔王「繼承」普通怪獸的所有設計，然後只加上牠專屬的特殊能力就好。**

---

##### 6. 核心一：父類別 (爸爸) 與 子類別 (兒子)
* **父類別 (Parent Class)**：提供基礎設計的模具（例如：`Monster` 普通怪獸）。
* **子類別 (Child Class)**：繼承了基礎設計，並加上自己新功能的進階模具（例如：`Boss` 魔王怪獸）。

> 💡 **語法秘訣：** 在定義子類別時，只要在名字後面加上括號，把父類別寫進去 `class Boss(Monster):`，電腦就會知道：「喔！這個 Boss 是 Monster 的進階版！」

---

##### 7. 核心二：那個神秘的 `super()` 是什麼？
在寫繼承時，新手常會遇到一個名叫 `super()` 的新朋友。
`super()` 代表的就是「父類別 (老爸)」。

當魔王出生（執行 `__init__`）的時候，我們不需要親自幫牠設定名字和基礎血量，我們可以直接大喊：**「老爸！你當初怎麼幫普通怪物設定名字和血量的，幫我照做一次！」** 這就是 `super().__init__(...)` 的功用。

##### 程式碼實戰：打造魔王專屬模具
請接著我們上一階段的 `Monster` 模具往下看：

```python
# 這是我們原本的「普通怪獸」模具 (父類別)
class Monster:
    def __init__(self, name, hp):
        self.name = name
        self.hp = hp

    def attack(self):
        print(f"{self.name} 發動了普通攻擊！")

# ==========================================

# 這是新的「魔王」模具 (子類別)，括號裡寫上 Monster 代表繼承
class Boss(Monster):
    
    # 魔王出生的設定 (建構子)
    def __init__(self, name, hp, shield):
        # 1. 呼叫老爸 (super)，把名字 and 血量的設定交給老爸處理
        super().__init__(name, hp)
        
        # 2. 設定魔王自己專屬的新屬性：護盾值
        self.shield = shield

    # 魔王專屬的新動作：大絕招！(普通怪獸不會這招)
    def ultimate_attack(self):
        print(f"⚠️ 警告！魔王 {self.name} 釋放了毀滅死光！")

    # 魔王專屬的扣血機制 (改寫覆蓋掉老爸原本的設定)
    def take_damage(self, damage):
        if self.shield > 0:
            self.shield = self.shield - damage
            print(f"{self.name} 的護盾擋下了攻擊！剩下 {self.shield} 點護盾。")
        else:
            self.hp = self.hp - damage
            print(f"護盾破裂！{self.name} 受到 {damage} 點傷害，剩下 {self.hp} 滴血。")
```

---

##### 8. 核心三：測試繼承的威力
我們來測試看看，子類別是不是真的繼承了父類別的能力，又擁有了自己的新招式：

```python
# 創造一隻普通怪獸
slime = Monster("史萊姆", 50)
slime.attack() 
# 輸出: 史萊姆 發動了普通攻擊！

print("---")

# 創造一隻魔王 (名字, 血量, 護盾值)
dragon = Boss("黑龍王", 500, 100)

# 1. 魔王可以直接使用老爸的「普通攻擊」方法！(這就是繼承的好處，不用重寫)
dragon.attack()
# 輸出: 黑龍王 發動了普通攻擊！

# 2. 魔王可以使用自己專屬的「大絕招」
dragon.ultimate_attack()
# 輸出: ⚠️ 警告！魔王 黑龍王 釋放了毀滅死光！

# 3. 測試魔王專屬的護盾扣血機制
dragon.take_damage(30)
# 輸出: 黑龍王 的護盾擋下了攻擊！剩下 70 點護盾。
```

到這裡，這位新手已經掌握了 Python 語法基礎，以及 OOP 最核心的「類別、物件、建構子、繼承」四大觀念了！

---

### 二、 Docker 基礎與沙盒隔離
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

---

### ⚡ AUV 實戰必學：Docker 進階執行參數

在開發真實的 AUV 系統時，容器不僅僅是隔離的沙盒，還必須與外部的實體硬體、GPU 加速卡以及主機網路通訊。以下是三個機器人與 AUV 開發中極為關鍵的進階 Docker 執行參數：

#### 1. 實體硬體裝置掛載 (`--device`)

Docker 的沙盒機制預設會將硬體完全隔離。但 AUV 內部的微控制器（例如控制推進器的 Arduino、回傳姿態的 IMU 感測器）通常是透過 USB 或序列埠與主機連接的。我們必須顯式地為容器「開門」。

* **核心概念**：使用 `--device` 參數，將主機端的硬體路徑映射到容器內。
* **實戰指令**：
```bash
docker run -it --rm --device /dev/ttyUSB0 auv-thruster-calc:v1.0
```
* **應用場景**：讓容器內的程式碼能夠順利透過 `/dev/ttyUSB0` 發送 PWM 訊號給推進器驅動板，或讀取感測器資料。

#### 2. GPU 運算資源穿透 (`--gpus all`)

水下影像辨識、物件追蹤（例如執行 SAM 3 等深度學習模型）或使用 NVIDIA Isaac ROS 框架時，極度依賴 GPU 加速。預設的容器是抓不到顯示卡的。

* **核心概念**：在主機安裝好 NVIDIA Container Toolkit 後，透過 `--gpus all` 指令將主機的 GPU 運算能力「穿透」進容器。
* **實戰指令**：
```bash
docker run -it --rm --gpus all auv-vision-app:v1.0
```
* **應用場景**：讓配備硬體加速卡（例如筆電上的 RTX 4050）的設備，能夠在容器內部滿血運行 CUDA 程式，大幅提升幀率（FPS）並降低延遲。

#### 3. 網路模式配置 (`--network host`)

機器人系統通常採用分散式架構（如 ROS 2）。Docker 預設的 `bridge` 網路會給予容器獨立的虛擬 IP，這會導致 ROS 2 節點之間（透過 DDS 協定）互相找不到對方，發生通訊中斷。

* **核心概念**：將容器的網路模式設定為 `host`，讓容器直接與主機共用同一個網路介面，解除沙盒的網路隔離。
* **實戰指令**：
```bash
docker run -it --rm --network host auv-ros-node:v1.0
```
* **應用場景**：確保不同容器內的 ROS 2 節點，或是容器與主機本機端的節點，能夠無縫訂閱與發布（Publish/Subscribe）Topic 資料。
