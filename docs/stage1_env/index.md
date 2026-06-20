# 階段一：第一堂課 - 環境建置 🛠️

本階段的核心目標是**消除學員之間的環境差異**，讓所有人站在同一條起跑線上。對於需要進行 AUV 開發，硬體驅動的正確安裝與容器隔離尤為重要。

---

## 💻 軟體安裝清單與安裝指引

---

## 【分類一】 作業系統與硬體連結 (Ubuntu 24.04 LTS)

####  使用 WSL2 安裝 Ubuntu 24.04 

WSL2 是 Windows 內建的 Linux 子系統，適合不想重新分割硬碟且無獨立顯卡的學員。

##### 1. 輸入指令的位置
在 Windows 系統中，按下 `Win + Q` 搜尋 **「PowerShell」**，對著它點選右鍵，選擇 **「以系統管理員身分執行 (Run as Administrator)」**。

##### 2. 安裝指令
請在開啟的 PowerShell 視窗中輸入以下指令並按下 Enter：
```powershell
wsl --install -d Ubuntu-24.04
```
> [!NOTE]
> 如果系統提示您需要重新啟動電腦，請重新啟動。重啟後 Windows 會自動開啟一個新的 Ubuntu 命令提示字元視窗，繼續安裝程序。

##### 3. 初始設定
安裝完成後，Ubuntu 視窗會要求您設定**使用者名稱與密碼**：
1. **Enter new UNIX username**: 請輸入您自訂的帳號名稱 (請使用小寫英文字母，如 `auvuser`，不可有空格)。
2. **New password**: 輸入您自訂的密碼 (輸入時畫面**不會顯示任何字元或星號**，這是 Linux 的安全保護機制，請盲打後按 Enter)。
3. **Retype new password**: 再次輸入相同密碼並按 Enter。

密碼設定完成後，請在 **Ubuntu 終端機** 內執行以下指令更新系統套件：
```bash
sudo apt update && sudo apt upgrade -y
```
*(執行 `sudo` 指令時會要求您輸入剛剛設定的密碼。)*

##### 4. 關鍵附加設定：AUV 硬體連接 (USB/串口) 映射 ⚠️
WSL2 預設無法存取主機的 USB 實體裝置（例如 USB 轉 TTL 模組、IMU、壓力感測器等）。我們必須安裝 **usbipd-win** 工具：
1. **安裝 usbipd-win**：在 Windows **PowerShell (以系統管理員身分執行)** 輸入以下指令：
   ```powershell
   winget install usbipd-win
   ```
   *安裝完成後，請重新啟動電腦以生效。*
2. **基本操作指引 (後續連接硬體時使用)**：
   * 在 Windows PowerShell 中執行 `usbipd list` 查找連接的 USB 裝置（例如記下其 Bus ID，如 `2-1`）。
   * 將裝置共享綁定（首次需執行一次）：`usbipd bind --busid <BUSID>`。
   * 將裝置掛載進 Ubuntu：`usbipd attach --wsl --busid <BUSID>`。
   * 在 Ubuntu 終端機中輸入 `lsusb`，即可看到該 USB 裝置，代表已成功導進 Linux！

##### 5. 如何檢查安裝成功
* **檢查 WSL 狀態 (在 Windows PowerShell 中)**：
  ```powershell
  wsl -l -v
  ```
  若顯示中 `NAME` 為 `Ubuntu-24.04`、`VERSION` 為 `2`，即代表成功安裝。
* **檢查 Ubuntu 版本 (在 Ubuntu 終端機中)**：
  ```bash
  lsb_release -a
  ```
  應顯示 `Description: Ubuntu 24.04 LTS`。

---

## 【分類二】 開發工具整合 (VS Code & AI 助手)

### 1. IDE 安裝：VS Code 及必裝擴充套件
* **Python**：提供語法高亮、格式化與 Debug 支援。
* **C/C++**：用於編譯與開發底層 C++ 節點。
* **Dev Containers**：方便管理容器、映像檔與 Volume 映射開發。
* **WSL** ⚠️ (WSL2 用戶必裝)：讓您可以直接在 Windows 的 VS Code 視窗中，無縫連接並開發 WSL2 內的專案。
* **Docker** ⚠️：用來直覺地在左側邊欄檢視 Container 狀態、Log 畫面與 Volume 檔案，對初學者非常友善。

Windows 與 Mac 環境之 C/C++ 工具鏈基礎安裝指南可參閱：
* **Windows C 環境與 VS Code 基本設定**：[安裝指引影片/文件 1](https://drive.google.com/file/d/1bcwUeHuFPzFiuwqt9WiwahagIiMyr13K/view?usp=sharing)、[額外設定讓輸出在 Terminal 顯示](https://drive.google.com/file/d/1nGTVGBeMm6VnJw8juysmK-tyy3qVV2_L/view?usp=sharing)
* **MacBook C 環境與 VS Code 基本設定**：[安裝指引影片/文件 2](https://drive.google.com/file/d/1_RzE2PpczKwdKkK3NbJ51VkOPTK7QKyK/view?usp=sharing)

---

### 2. AI 輔助開發工具與平台
在開始撰寫程式碼之前，讓 AI 成為您的常駐助教。本培訓將使用 **Antigravity (Google DeepMind 研發的 AI 代理 IDE)** 與 **Codex (AI 輔助程式補全套件)**。

#### 步驟 A：註冊 GitHub 帳號
GitHub 是全球最大的程式碼託管與協作平台，也是後續交作業與團隊專案的核心工具。

1. **造訪註冊頁面**：開啟瀏覽器進入 [GitHub 官網 (github.com)](https://github.com/)。
2. **填寫資料**：點選網頁右上角的 **「Sign up」**。
   * 輸入您的常用 **Email 信箱**。
   * 設定您的 **密碼 (Password)**。
   * 輸入您的 **使用者名稱 (Username)** (這將會是您的公開識別名稱，如 `tom-auv-2026`)。
   * 選擇是否接受電子報（輸入 `y` 或 `n`），並完成簡單的「人機驗證（拼圖或旋轉圖案）」。
3. **驗證信箱**：到您的 Email 收取驗證碼並輸入，即可完成註冊。
4. **準備完成**：請記住您的帳號與密碼，我們在第二階段的 Git 協作中會頻繁使用！

---

#### 步驟 B：下載與熟悉 Antigravity (Google DeepMind AI 協作開發平台)
Antigravity 是 Google DeepMind 打造的 AI-first 開發環境 (基於 VS Code 分支開發)，它不只是編輯器，還能讓多個 AI 代理（Agents）在後台幫您規劃、執行和測試程式。

##### a. 下載與安裝
* **下載位置**：開啟瀏覽器造訪 **[antigravity.google](https://antigravity.google)**。
* **安裝步驟**：
  * **WSL2 / Linux 用戶**：下載 `.deb` 安裝包，在終端機執行 `sudo dpkg -i <filename>.deb`，或直接下載軟體包進行安裝。
  * **Windows / macOS 用戶**：下載對應的安裝檔，按預設選項直接雙擊安裝。
* **登入帳號**：啟動 Antigravity 後，點選左下角登入您的 Google 帳號以啟用 AI 核心功能。

##### b. 熟悉 Antigravity 介面與功能
* **載入工作區**：點選 `File -> Open Folder`，選擇我們的 `auv-training-2026` 專案資料夾。
* **Agent Manager (代理管理器)** 🤖：
  * 在右側面板中，您可以看到 Agent 控制台。
  * 您可以發起一個任務（Task），例如：`幫我寫一個計算推進器 PWM 的 Python 類別`。
* **Artifacts (產出產物)** 📄：
  * 當 AI Agent 收到您的指令後，它不會只在聊天室噴程式碼，而是會主動在工作區建立諸如 `implementation_plan.md` (實作規劃案) 或 `task.md` (待辦清單)。
  * 您只需點選 **「Proceed」** 批准，AI 代理就會自動在後台幫您編輯程式、建立檔案甚至執行測試！

---

#### 步驟 C：下載與熟悉 Codex (AI 程式碼智慧補全)
Codex 是功能強大的 AI 程式碼編寫與即時補全套件，可直接嵌入在您的編輯器中，提供流暢的「即時自動完成」體驗。

##### a. 下載與安裝
1. 開啟 **Antigravity** 或 **VS Code**。
2. 點擊左側邊欄的 **Extensions (擴充套件，快捷鍵 `Ctrl + Shift + X`)**。
3. 在上方搜尋欄輸入 **`Codex`**（或視課程引導安裝 GitHub Copilot / Codeium 等相容套件）。
4. 點選 **「Install」** 按鈕完成安裝，並依畫面提示完成帳號授權連結。

##### b. 熟悉與使用技巧
* **單行/多行程式碼自動補全 (Inline Completion)**：
  * 當您開始在程式檔案中打字時，Codex 會自動在游標後方以 **「淡灰色文字」** 預測您可能想寫的內容。
  * 如果預測正確，按下 **`Tab` 鍵** 即可一鍵採用；若不需要，直接繼續打字即可。
* **透過註解生成程式碼 (Comment-to-Code)**：
  * 在程式碼中寫下一行中文註解，例如：
    ```python
    # 計算推進器 PWM 的物理安全區間限制，限制在 1100 到 1900 之間
    ```
  * 按下 Enter 換行，Codex 將會自動生成對應的實作程式碼，同樣按下 **`Tab`** 即可採用。
* **快捷視窗 (Chat & Inline Edit)**：
  * 選取某段程式碼，按下 **`Ctrl + I`**，即可開啟小視窗直接向 AI 說「幫我重構這段程式」或「幫我這段加上防呆機制」。

---

## 【分類三】 版本控制與代碼管理 (Git & GitHub)

### 1. 版本控制工具：Git 基本設定
在終端機執行以下指令完成安裝與設定：
```bash
sudo apt update
sudo apt install git -y

# 設定全域使用者資訊（請改為您自己的資訊）
git config --global user.name "YourName"
git config --global user.email "your_email@example.com"
```

---

### 2. 關鍵認證設定：建立 GitHub SSH 金鑰
GitHub 已停止支援密碼驗證。為了之後能夠順利將作業與程式碼 push 到 GitHub，必須設定 SSH 金鑰：
1. **生成金鑰對** (在 Ubuntu 終端機執行)：
   ```bash
   ssh-keygen -t ed25519 -C "your_email@example.com"
   ```
   *出現提示時，直接連按三下 Enter 鍵（使用預設檔案路徑且不設定額外密碼）。*
2. **複製公鑰內容**：
   ```bash
   cat ~/.ssh/id_ed25519.pub
   ```
   *請將終端機輸出的這一整串文字（以 `ssh-ed25519` 開頭）全部複製起來。*
3. **新增至 GitHub 網頁**：
   * 登入 GitHub，點選右上角個人頭像 -> **Settings**。
   * 點選左側選單的 **SSH and GPG keys**。
   * 點選右上角 **New SSH key**。
   * **Title** 輸入您筆電的識別稱（例如 `my-laptop`），**Key** 的欄位中貼上剛才複製的公鑰內容，並點選 **Add SSH key**。
4. **測試連線** (在 Ubuntu 終端機執行)：
   ```bash
   ssh -T git@github.com
   ```
   *若看見 `Hi [您的使用者名稱]! You've successfully authenticated...`，代表設定成功！*

---

## 【分類四】 Docker & NVIDIA

### 1. 容器化引擎：Docker & Docker Compose
使用以下指令安裝最新版 Docker Engine：
```bash
# 解除安裝舊版本
for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do sudo apt-get remove $pkg; done

# 設定 Docker 儲存庫並安裝
sudo apt-get update
sudo apt-get install ca-certificates curl -y
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y

# 免 sudo 執行 Docker 設定
sudo usermod -aG docker $USER
```

> [!IMPORTANT]
> **免 sudo 立即生效設定** ⚠️
> 上述 `usermod` 指令在一般情況下需要重新登入或重開機才會生效。請在同一個終端機視窗中執行以下指令，強迫更新群組權限，避免後續 Docker 指令因為權限不足 (Permission Denied) 而報錯：
> ```bash
> newgrp docker
> ```

---

### 2. GPU 支援：NVIDIA Driver & NVIDIA Container Toolkit (重要 ⚠️)
確保後續 Docker 容器能調用您電腦的實體 GPU 進行視覺辨識模型與 AUV 模擬訓練。

#### 步驟 A：設定 NVIDIA 驅動程式 (WSL2 用戶)
* **重要安全警告** ⚠️：由於您使用的是 **WSL2**，**絕對不要**在 WSL2 的 Ubuntu 系統內執行 `sudo apt install nvidia-driver-...` 或任何 `ubuntu-drivers` 安裝指令！這會搞爛系統。
* **正確做法**：您只需直接在 Windows 主機中下載並更新至最新的 [NVIDIA 顯示卡官方驅動程式](https://www.nvidia.com/Download/index.aspx)。安裝完成後，WSL2 會自動共用 Windows 端的 GPU 驅動。
* **驗證驅動**：請在 Ubuntu 終端機中輸入以下指令，若能看見您的實體顯示卡資訊，即代表設定成功：
  ```bash
  nvidia-smi
  ```

---

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
sudo nvidia-ctk runtime configure --runtime=docker
sudo systemctl restart docker
```

---

#### 驗證 Docker 是否成功調用 GPU

**請注意**：請確保 Windows 端的 NVIDIA 驅動已安裝完畢。

接著請在 Ubuntu 終端機執行以下指令：
```bash
newgrp docker # 確保免 sudo 權限立即生效
docker run --rm --gpus all nvidia/cuda:12.5.0-base-ubuntu24.04 nvidia-smi
```
*(如果終端機能印出顯示卡的詳細表格，恭喜你，環境架設完成！)*
