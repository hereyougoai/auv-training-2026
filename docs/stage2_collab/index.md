# 階段二：生存法則與協作基礎（Linux、Git 與 AI 詠唱） 🤝

在進入正式的 AUV 程式編寫之前，學員必須先學會「如何存活在 Linux 終端機」以及「如何與團隊、AI 協作」。

---

## 📖 核心學習內容

### 1. Ubuntu 基礎指令
AUV 開發大多數時間不使用視窗介面，而是透過 SSH 連線至載具終端機操作。學員需熟練以下指令：
* **目錄切換與查看**：`cd`, `ls -la`, `pwd`
* **檔案操作**：`mkdir`, `touch`, `cp`, `mv`, `rm` (慎用 `rm -rf`)
* **權限管理**：`chmod` (例如 `chmod +x` 賦予腳本執行權限), `chown`, `sudo`
* **系統狀態查詢**：`top`, `htop`, `df -h`

---

### 2. AI Agent 協作心法 (Prompting)
不要把 AI 當成作弊工具，而是將其視為一對一程式助教。
* **報錯排除**：當終端機噴出紅字時，直接複製並對 AI 說：
  > 「我在 Ubuntu 24.04 執行 [某某指令] 時遇到以下錯誤，請幫我分析原因並給予逐步修復方案：[貼上報錯內容]」
* **程式優化**：撰寫程式後，可以請 AI 進行 Code Review：
  > 「這是我寫的 AUV 控制代碼，請幫我檢查是否有邏輯漏洞，並在不改變功能的前提下進行效能優化與加入註解。」

---

### 3. Git 與 GitHub 版本控制概念
理解代碼的歷史軌跡與多人協作的核心概念：
* **工作流流程**：
  ```
  [ 本地端工作區 ] ──(git add)──> [ 暫存區 ] ──(git commit)──> [ 本地儲存庫 ] ──(git push)──> [ GitHub 遠端庫 ]
  ```
* **核心指令**：
  * `git clone <URL>`：下載專案。
  * `git checkout -b <branch-name>`：建立並切換至新功能分支。
  * `git add <file>` / `git commit -m "訊息"`：紀錄檔案變更。
  * `git pull origin main`：同步遠端最新進度。
  * `git push origin <branch-name>`：上傳分支至 GitHub。

---

## 🛠️ 實作小專案：【AUV 團隊簽到板】

透過實際發起 Pull Request (PR) 流程，讓學員體驗真實團隊的協作模式。

### 任務步驟：
1. **點擊進入** 團隊的 GitHub 公開儲存庫。
2. 將專案 **Clone** 至您本地的 Ubuntu 系統中。
3. **建立分支**：`git checkout -b feat/sign-in-<您的名字>`。
4. **建立個人檔案**：
   * 在專案的 `members/` 資料夾下，建立一個以您名字命名的資料夾（使用 `mkdir`）。
   * 在該資料夾下建立 `README.md`，使用 Markdown 撰寫簡單的自我介紹、負責的 AUV 次系統與對今年的期許。
5. **提交變更**：
   ```bash
   git add .
   git commit -m "feat: add <Your Name> to sign-in board"
   ```
6. **推送到遠端**：`git push origin feat/sign-in-<您的名字>`。
7. **發起 Pull Request (PR)**：在 GitHub 網頁上，發起將您的分支合併至 `main` 的 PR。
8. **解決衝突**：若與其他學員的代碼發生衝突，鼓勵使用 AI Agent 詢問 `How to resolve git merge conflicts` 並手動排解。
