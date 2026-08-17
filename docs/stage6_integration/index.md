# SAUVC 系統指令速查表 (Cheat Sheet)

這份文件為您整理了從**啟動底層環境**、**開啟模擬**、**解鎖與派發任務**，一直到**中止與重置任務**的所有關鍵指令。

!!! tip "新生實作任務"
    準備好挑戰了嗎？請前往查看並實作：[**👉 資格賽任務 —— 提示說明書**](newbie_task.md)
!!! note "注意事項"
    所有的 `make` 與 `docker compose` 指令，預設都需在專案的根目錄下執行（即 `SAUVC/` 目錄）。

---

## 一、環境建置與容器啟動

在完全乾淨的環境下，首次啟動所需執行的步驟：

1. **建立 Autonomy 自駕容器映像檔**（當遠端下載失敗時的本機建置替代方案）：
   ```bash
   TERM=xterm ./SAUVC-JETSON/isaac_ros_common/scripts/orca_registry.sh build
   ```
   *(註：此步驟僅首次或更動底層相依套件時需要執行，編譯時間較長)*

2. **啟動所有基礎容器與編譯 ROS 工作區**：
   ```bash
   make up && make build
   ```
   *(此指令會啟動 `control` 與 `autonomy` 等核心容器，並利用 colcon 編譯最新程式碼)*

---

## 二、啟動 Gazebo 模擬環境

當容器都上線後，接著是開啟虛擬水池。

- **啟動模擬（無頭模式）**：
  - **若要執行資格賽任務 (Qualification Mission)**：
    ```bash
    cd /home/yoei/workspace/SAUVC && make sim ARENA=qualification SEED=2 TREE=QualificationMission HEADLESS=true
    ```
  - **若要執行決賽任務 (Finals Mission)**：
    ```bash
    cd /home/yoei/workspace/SAUVC && make sim ARENA=finals SEED=2 HEADLESS=true
    ```
  *(註：`ARENA` 代表場地，`SEED` 為隨機亂數種子。加上 `HEADLESS=true` 可避免 X11 視窗權限問題，對於純背景運算或依賴 Web GUI 的情況十分合適)*

---

## 三、解鎖載具與啟動自動任務

模擬器就緒後，必須發送特定訊號讓載具進入自動導航模式。

1. **解鎖推進器並切換至全自動模式 (Arming)**：
   利用 ROS 2 的 Service 呼叫，依序啟動「定深模式 (Depth Hold)」與「全自動模式 (Autonomous)」：
   ```bash
   docker compose exec control bash -lc 'source /opt/ros/humble/setup.bash && source /root/rpi_ros2_ws/install/setup.bash && ros2 service call /orca_auv/system_manager/set_mode/depth_hold std_srvs/srv/Trigger && ros2 service call /orca_auv/system_manager/set_mode/autonomous std_srvs/srv/Trigger'
   ```

2. **發送「開始任務」訊號 (Start Mission)**：
   向行為樹 (Behavior Tree) 發佈啟動旗標 (`data: true`)：
   ```bash
   docker compose exec autonomy bash -lc 'source /opt/ros/humble/setup.bash && source /workspaces/isaac_ros-dev/install/setup.bash && ros2 topic pub --once /orca/decision/start_mission std_msgs/msg/Bool "{data: true}"'
   ```

---

## 四、中止任務與環境重置

當需要暫停載具動作，或是整個模擬需要重來時：

1. **中止進行中的自動任務**：
   發佈停止旗標 (`data: false`) 給行為樹，載具將會停止正在進行的搜尋或導航動作：
   ```bash
   docker compose exec autonomy bash -lc 'source /opt/ros/humble/setup.bash && source /workspaces/isaac_ros-dev/install/setup.bash && ros2 topic pub --once /orca/decision/start_mission std_msgs/msg/Bool "{data: false}"'
   ```

2. **強制重置所有 ROS 節點與模擬環境**：
   如果您希望潛艇回到最初始的原點並重新載入所有的感測器狀態：
   ```bash
   make stop
   ```
   執行完 `make stop` 後，只要重新依序執行 **步驟二** (啟動 Gazebo) 與 **步驟三** (解鎖與啟動)，即可開啟全新的一輪測試。
