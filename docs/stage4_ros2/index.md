# 階段四：系統神經網路（ROS 2 架構與通訊） 🤖📡

這是 AUV 開發的核心。Ubuntu 24.04 原生對應的是 **ROS 2 Jazzy Jalisco**。學員將在此階段學會如何建立載具的「神經系統」，讓不同的感測器（如 IMU、深度計）與大腦（決策節點）進行資料交換。

---

## 📖 核心學習內容

### 1. ROS 2 Workspace 與 Colcon 編譯系統
* **Workspace (工作空間)**：開發 ROS 2 的目錄結構。通常包含 `src/`（原始碼放置處）。
* **Colcon**：ROS 2 的專用編譯工具。
```bash
# 建立工作空間並編譯的標準動作
mkdir -p ~/auv_ws/src
cd ~/auv_ws
colcon build
source install/setup.bash
```

---

### 2. Package (套件) 與 Node (節點)
* **Package**：ROS 2 程式碼的基本組織單位，可包含多個節點。
* **Node**：一個獨立運行的執行檔（進程），負責單一功能（例如：讀取鏡頭、發送電機指令）。
```bash
# 建立一個 Python 類型的 ROS 2 套件
ros2 pkg create --build-type ament_python auv_monitor --dependencies rclpy std_msgs
```

---

### 3. Topic (主題) 通訊機制
Topic 是 ROS 2 最常用的一對多單向非同步通訊方式（發布/訂閱模式）：
* **Publisher (發布者)**：往某個 Topic 管道不斷灌入資料。
* **Subscriber (訂閱者)**：監聽該 Topic，一旦有新資料就觸發回呼函式 (Callback) 處理。

---

### 4. 終極多容器部署：Docker Compose

在 AUV 的真實開發中，我們可能需要同時啟動許多個服務，例如「推進控制節點」、「相機影像辨識節點」與「任務決策節點」。如果每次啟動都得手動開啟多個終端機、輸入長長的 `docker run` 指令，不僅容易打錯，也極難維護。

Docker Compose 就是為了解決這個問題而誕生的工具。它允許我們使用一份 `docker-compose.yml`（YAML 格式的純文字檔）來定義整個 AUV 系統的所有服務，然後**一鍵啟動**所有容器。

#### 📝 Docker Compose 與 docker run 的防呆對照表

| 需求情境 | docker run (手動終端機指令) | docker-compose.yml (自動化設定) |
| :--- | :--- | :--- |
| **映像檔** | `auv-thruster-calc:v1.0` | `image: auv-thruster-calc:v1.0` |
| **網路設定** | `--network host` | `network_mode: "host"` |
| **硬體掛載** | `--device /dev/ttyUSB0` | `devices:`<br>&nbsp;&nbsp;`- "/dev/ttyUSB0:/dev/ttyUSB0"` |
| **目錄掛載** | `-v $(pwd):/app` | `volumes:`<br>&nbsp;&nbsp;`- ./:/app` |

#### 🚀 Docker Compose 核心起手式指令

寫好設定檔後，所有的繁瑣啟動指令都化繁為簡：
* **一鍵背景啟動所有服務**：
  ```bash
  docker compose up -d
  ```
  > 💡 **提示**：`-d` 代表在背景執行（Detached mode），您的終端機不會被卡住，可以繼續輸入其他指令。
* **查看所有服務的運行狀態**：
  ```bash
  docker compose ps
  ```
* **即時查看特定服務的輸出日誌 (Log)**：
  ```bash
  # 即時查看影像辨識節點的輸出情況
  docker compose logs -f vision_node
  ```
* **一鍵停止並刪除所有服務**：
  ```bash
  docker compose down
  ```

---

## 🛠️ 實作小專案：【深度監控警報系統】

編寫兩個 ROS 2 節點：**Node A** 負責模擬深度計發送數據，**Node B** 負責接收數據並在水深過深時發出警告。

### 步驟一：撰寫 Node A (Sensor Mock - 發布者)
在 `auv_monitor/auv_monitor/sensor_mock.py` 寫入：
```python
import rclpy
from rclpy.node import Node
from std_msgs.msg import Float32
import random

class SensorMockNode(Node):
    def __init__(self):
        super().__init__('sensor_mock')
        # 建立一個發布者，發布至 /auv/depth 主題，訊息類型為 Float32，佇列大小為 10
        self.publisher_ = self.create_publisher(Float32, '/auv/depth', 10)
        
        # 每秒觸發一次定時器發布數據
        self.timer = self.create_timer(1.0, self.publish_depth)
        self.current_depth = 1.0

    def publish_depth(self):
        msg = Float32()
        # 模擬深度在 0.5 ~ 4.0 米之間隨機波動與遞增
        self.current_depth += random.uniform(-0.2, 0.5)
        msg.data = max(0.0, self.current_depth)
        
        self.publisher_.publish(msg)
        self.get_logger().info(f'發布當前深度數據: {msg.data:.2f} m')

def main(args=None):
    rclpy.init(args=args)
    node = SensorMockNode()
    rclpy.spin(node)
    node.destroy_node()
    rclpy.shutdown()
```

---

### 步驟二：撰寫 Node B (Safety Monitor - 訂閱者)
在 `auv_monitor/auv_monitor/safety_monitor.py` 寫入：
```python
import rclpy
from rclpy.node import Node
from std_msgs.msg import Float32

class SafetyMonitorNode(Node):
    def __init__(self):
        super().__init__('safety_monitor')
        # 建立訂閱者，監聽 /auv/depth 主題
        self.subscription = self.create_subscription(
            Float32,
            '/auv/depth',
            self.depth_callback,
            10
        )
        # 設定安全警戒深度為 3.0 米
        self.SAFE_LIMIT = 3.0

    def depth_callback(self, msg):
        current_depth = msg.data
        if current_depth > self.SAFE_LIMIT:
            self.get_logger().warn(
                f'⚠️ [🚨 警報] 偵測到危險水深: {current_depth:.2f} m (安全上限: {self.SAFE_LIMIT} m)！'
            )
        else:
            self.get_logger().info(
                f'正常運作中。深度: {current_depth:.2f} m'
            )

def main(args=None):
    rclpy.init(args=args)
    node = SafetyMonitorNode()
    rclpy.spin(node)
    node.destroy_node()
    rclpy.shutdown()
```

---

### 步驟三：撰寫專案的 Dockerfile

為了將我們的 ROS 2 程式包裝為可移植的映像檔，我們需要在工作空間根目錄下建立一個 `Dockerfile`：

```dockerfile
# 1. 使用官方 ROS 2 Jazzy 基礎映像檔
FROM osrf/ros:jazzy-desktop

# 2. 設定容器內的工作目錄
WORKDIR /auv_ws

# 3. 將本地的 src 原始碼目錄複製到容器中
COPY ./src ./src

# 4. 編譯 ROS 2 工作空間
RUN . /opt/ros/jazzy/setup.sh && colcon build

# 5. 設定 Entrypoint，啟動時自動加載環境變數
ENTRYPOINT ["/bin/bash", "-c", "source /opt/ros/jazzy/setup.bash && source /auv_ws/install/setup.bash && exec \"$@\""]
```

---

### 步驟四：撰寫 docker-compose.yml 啟動檔

使用 Docker Compose 來一鍵啟動這兩個相互通訊的節點。在工作空間根目錄下建立一個名為 `docker-compose.yml` 的檔案：

```yaml
services:
  # 服務一：模擬深度發布節點
  sensor_mock:
    image: auv-monitor:v1.0
    build: .
    container_name: auv_sensor_mock
    network_mode: "host" # 使用 host 網路模式，確保 ROS 2 DDS 能與其他節點正常通訊
    command: ros2 run auv_monitor sensor_mock

  # 服務二：安全監控接收節點
  safety_monitor:
    image: auv-monitor:v1.0
    build: .
    container_name: auv_safety_monitor
    network_mode: "host"
    command: ros2 run auv_monitor safety_monitor
```

---

### 步驟五：一鍵啟動與驗證

1. 請在終端機中確認路徑在專案目錄下，執行以下指令建立並啟動服務：
   ```bash
   docker compose up -d
   ```
2. 查看服務是否已經成功啟動並在背景運行：
   ```bash
   docker compose ps
   ```
3. 即時監看 **Safety Monitor** 節點的輸出日誌，確認是否有收到深度數據：
   ```bash
   docker compose logs -f safety_monitor
   ```
   *(當深度計模擬的值超過 `3.0` 時，您應該會看到畫面上噴出黃色的 `⚠️ [🚨 警報] 偵測到危險水深...` 訊息)*
4. 驗證完成後，一鍵停止並清理所有的容器：
   ```bash
   docker compose down
   ```
