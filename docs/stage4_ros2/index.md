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

### 步驟三：測試與驗證

1. 在 `setup.py` 中設定 Entry Points，以便可以使用 `ros2 run` 啟動節點。
2. 開啟第一個終端機，啟動 **Sensor Mock** 節點：
   ```bash
   ros2 run auv_monitor sensor_mock
   ```
3. 開啟第二個終端機，啟動 **Safety Monitor** 節點：
   ```bash
   ros2 run auv_monitor safety_monitor
   ```
4. 觀察當第一個終端機發送的值超過 `3.0` 時，第二個終端機是否會噴出警告訊息！這就完成了 AUV 內部最基礎的通訊與安全保護機制。
