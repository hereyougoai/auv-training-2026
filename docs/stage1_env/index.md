# 階段一：第一堂課 - 環境建置 🛠️

本階段的核心目標是**消除學員之間的環境差異**，讓所有人站在同一條起跑線上。對於需要進行AUV 開發，硬體驅動的正確安裝與容器隔離尤為重要。

---

## 💻 軟體安裝清單與安裝指引

### 1. 作業系統：Ubuntu 24.04 LTS
為確保 ROS 2 與底層驅動的相容性，本培訓以 **Ubuntu 24.04** 作為原生開發環境。
* **電競筆電 / 獨立顯卡用戶**：建議安裝 **Windows + Ubuntu 雙系統**。
* **輕薄筆電 / 無獨立顯卡用戶**：可評估使用 **WSL2 (Windows Subsystem for Linux 2)**，但需注意 USB 裝置映射與 GPU 調用設定較為繁瑣。

---

### 2. IDE：VS Code 及必裝擴充套件
請至 [VS Code 官網](https://code.visualstudio.com/) 安裝，並於 Extensions (Ctrl+Shift+X) 安裝以下套件：
* **Python**：提供語法高亮、格式化與 Debug 支援。
* **C/C++**：用於編譯與開發底層 C++ 節點。
* **ROS**：提供 ROS 2 程式碼自動完成與 Workspace 管理。
* **Docker**：方便管理容器、映像檔與 Volume。
* **GitLens**：視覺化 Git 提交歷史，方便團隊追蹤代碼修改。

---

### 3. AI 輔助開發工具
在第一堂課就完成安裝，讓 AI 成為後續學習的常駐助教：
* **GitHub Copilot** 或 **Codeium**。
* **使用建議**：遇到語法不熟或報錯時，隨時使用 `Ctrl + I` 或聊天視窗向 AI 發問。

---

### 4. 版本控制工具：Git
在終端機執行以下指令完成安裝與設定：
```bash
sudo apt update
sudo apt install git -y

# 設定全域使用者資訊（請改為您自己的資訊）
git config --global user.name "YourName"
git config --global user.email "your_email@example.com"
```

---

### 5. 容器化引擎：Docker & Docker Compose
使用以下指令安裝最新版 Docker Engine：
```bash
# 解除安裝舊版本
for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do sudo apt-get remove $pkg; done

# 設定 Docker 儲存庫並安裝
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y

# 免 sudo 執行 Docker 設定 (需要重新登入或重啟生效)
sudo usermod -aG docker $USER
```

---

### 6. GPU 支援：NVIDIA Driver & NVIDIA Container Toolkit (重要 ⚠️)
確保後續 Docker 容器能調用筆電的 GPU 進行視覺辨識模型訓練。

#### 步驟 A：安裝 NVIDIA 驅動程式
```bash
sudo ubuntu-drivers install
# 安裝完成後，請重新啟動電腦，並在終端機輸入 nvidia-smi 確認是否成功顯示顯示卡資訊：
nvidia-smi
```

#### 步驟 B：安裝 NVIDIA Container Toolkit
這是讓 Docker 容器內程式能夠調用實體 GPU 的關鍵橋樑：
```bash
curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey | sudo gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg \
  && curl -s -L https://nvidia.github.io/libnvidia-container/stable/deb/nvidia-container-toolkit.list | \
    sed 's#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g' | \
    sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list

sudo apt-get update
sudo apt-get install -y nvidia-container-toolkit

# 設定並重啟 Docker 服務
sudo nvidia-container-toolkit policymakers install
sudo systemctl restart docker
```

驗證 Docker 是否成功調用 GPU：
```bash
docker run --rm --gpus all nvidia/cuda:12.0.0-base-ubuntu22.04 nvidia-smi
```
*(如果終端機能印出顯示卡的詳細表格，恭喜你，環境架設完成！)*
