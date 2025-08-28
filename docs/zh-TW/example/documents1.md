# TQuant Lab 安裝教學

本頁面詳細說明如何安裝 TQuant Lab 開發環境，提供三種安裝方式供您選擇。

!!! tip "新手建議"
    第一次使用？建議先查看[快速入門](../quickstart/setup.md)，有更簡化的安裝步驟。

## 系統需求

在開始安裝前，請確認您的系統符合以下需求：

| 項目 | 建議版本 | 備註 |
|------|----------|------|
| **核心套件** | Zipline | 主要回測引擎 |
| **Python** | 3.8 ~ 3.11 | **強烈建議使用 3.11** |
| **作業系統** | Windows 或 macOS | Linux 系統請參考進階設定 |
| **Pandas** | 1.5.3 或 2.0.0 | 高於 2.0.0 可能出現錯誤 |
| **Numpy** | 1.23.5 | 高於 1.23.5 可能出現錯誤 |

## 安裝方式選擇

我們提供三種安裝方式，**建議選擇方式 A 或 B**：

| 方式 | 適合對象 | 優點 | 缺點 |
|------|----------|------|------|
| **A. Docker** | 所有使用者 | 一鍵安裝、環境隔離 | 需要學習 Docker 基礎 |
| **B. Anaconda** | Python 使用者 | 環境管理方便 | 需要處理相依性 |
| **C. pip** | 進階使用者 | 安裝快速 | 可能遇到套件衝突 |

---

## 方式A：Docker 安裝（推薦）

Docker 提供最簡單且穩定的安裝體驗，強烈建議新手使用。

!!! success "為什麼選擇 Docker？"
    - 🚀 **一鍵安裝**：無需處理複雜的套件相依性
    - 🔒 **環境隔離**：不會影響您現有的開發環境
    - 📦 **完整配套**：內建所有必要的分析工具
    - 🛠️ **易於維護**：更新和重置都很簡單

### 步驟 1：安裝 Docker Desktop

請先下載並安裝 Docker Desktop：

- [Windows 版本下載](https://www.docker.com/products/docker-desktop/)
- [Mac 版本下載](https://www.docker.com/products/docker-desktop/)

### 步驟 2：選擇下載方式

我們提供兩種方式下載 TQuant Lab：

=== "方式一：直接下載（推薦）"

    這是最簡單的方式，直接從 Docker Hub 下載現成的映像檔：

    ```bash
    docker pull tej87681088/tquant:latest
    ```

=== "方式二：自建映像檔"

    如果您需要客製化環境，可以下載 Dockerfile 自行建置：

    - [下載 tquant_jupyter](https://github.com/tejtw/zipline-tej/blob/main/dockerfile/tquant_jupyter)（適用 Jupyter）
    - [下載 tquant_bash](https://github.com/tejtw/zipline-tej/blob/main/dockerfile/tquant_bash)（適用排程）

    ```bash
    # 進入 Dockerfile 所在目錄
    cd <dockerfile目錄路徑>
    
    # 建置映像檔
    docker build -f tquant_jupyter . -t "tquant:latest"
    ```

### 步驟 3：建立資料儲存空間

建立一個 Docker volume 來保存您的工作檔案：

```bash
docker volume create data
```

### 步驟 4：啟動 TQuant Lab 容器

根據您選擇的下載方式，執行對應的指令：

=== "使用方式一（直接下載）"
    ```bash
    docker run -v data:/app -p 8888:8888 --name tquant tej87681088/tquant
    ```

=== "使用方式二（自建映像檔）"
    ```bash
    docker run -v data:/app -p 8888:8888 --name tquant tquant
    ```

!!! info "指令說明"
    - `-v data:/app`：將 Docker volume 掛載到容器
    - `-p 8888:8888`：將容器的 8888 port 映射到本機
    - `--name tquant`：為容器命名為 "tquant"
    - `--rm`：（可選）容器停止後自動刪除

### 步驟 5：開始使用 Jupyter Notebook

1. 容器啟動後，終端機會顯示類似以下的網址：
   ```
   http://127.0.0.1:8888/tree?token=XXXXXXXXXXXXXXXX
   ```

2. 複製完整網址到瀏覽器中

3. 開始使用 TQuant Lab！

### 管理 Docker 容器

!!! tip "常用容器操作"

    **重新啟動關閉的容器：**
    ```bash
    # 查看所有容器
    docker ps -a 
    
    # 啟動指定容器
    docker start tquant
    
    # 查看容器日誌（獲取網址）
    docker logs --tail 3 tquant
    ```
    
    **檔案位置（Windows WSL 環境）：**
    ```
    \\wsl$\docker-desktop-data\data\docker\volumes\data\_data
    ```

---

## 方式B：Anaconda 安裝（推薦）

適合已有 Python 開發經驗的使用者，透過 Anaconda 管理虛擬環境。

!!! warning "重要提醒"
    強烈建議使用虛擬環境來避免套件版本衝突。[了解為什麼需要虛擬環境](https://csguide.cs.princeton.edu/software/virtualenv#definition)

### 步驟 1：下載環境設定檔

請根據您的作業系統下載對應的設定檔：

<div class="grid cards" markdown>

-   :material-microsoft-windows: **Windows 使用者**
    
    ---
    
    下載 Windows 專用的環境設定檔
    
    [下載 zipline-tej.yml](https://minhaskamal.github.io/DownGit/#/home?url=https://github.com/tejtw/zipline-tej/blob/main/zipline-tej.yml)

-   :material-apple: **Mac 使用者**
    
    ---
    
    下載 macOS 專用的環境設定檔
    
    [下載 zipline-tej_mac.yml](https://minhaskamal.github.io/DownGit/#/home?url=https://github.com/tejtw/zipline-tej/blob/main/zipline-tej_mac.yml)

</div>

### 步驟 2：建立虛擬環境

開啟 Anaconda Prompt 並執行以下指令：

=== "Windows"
    ```bash
    # 進入下載目錄
    cd C:\Users\您的使用者名稱\Downloads
    
    # 建立虛擬環境
    conda env create -f zipline-tej.yml
    
    # 啟動虛擬環境
    conda activate zipline-tej
    ```

=== "Mac"
    ```bash
    # 進入下載目錄
    cd /Users/您的使用者名稱/Downloads
    
    # 建立虛擬環境
    conda env create -f zipline-tej_mac.yml
    
    # 啟動虛擬環境
    conda activate zipline-tej
    ```

### 步驟 3：開始使用

虛擬環境建立完成後，您就可以開始使用 TQuant Lab 了！

```bash
# 啟動 Jupyter Notebook
jupyter notebook
```

!!! success "安裝完成"
    現在您可以開始使用 TQuant Lab 進行量化分析了。

---

## 方式C：pip 直接安裝（進階使用者）

適合有豐富 Python 經驗的進階使用者，但可能遇到套件相依性問題。

!!! danger "注意事項"
    此方式安裝可能遇到未預期的套件衝突問題，需要自行除錯。建議新手使用方式A或B。

### 本機端安裝

建議使用 [Anaconda](https://www.anaconda.com/data-science-platform) 作為開發環境，並建立虛擬環境避免套件衝突。

#### 方法一：使用 Anaconda

```bash
# 建立虛擬環境
conda create -n tquant-env python=3.11

# 啟動虛擬環境
conda activate tquant-env

# 安裝套件
pip install zipline-tej
pip install notebook
conda install -c conda-forge nb_conda_kernels
```

#### 方法二：使用 Python 原生虛擬環境

```bash
# 建立虛擬環境
python -m venv venv

# 啟動虛擬環境（Windows）
venv\Scripts\activate.bat

# 啟動虛擬環境（Mac/Linux）
source venv/bin/activate

# 安裝套件
pip install zipline-tej
pip install notebook
```

### Google Colab 使用

如果您使用 Google Colab，可以直接執行以下程式碼安裝：

```python
# 安裝 TQuant Lab
!pip install zipline-tej
```

#### 解決字體問題（可選）

如果使用 Pyfolio 產生報表，建議執行以下程式碼避免中文字體問題：

```python
import matplotlib
import matplotlib.font_manager as fm

# 下載中文字體
!wget -O MicrosoftJhengHei.ttf https://github.com/a7532ariel/ms-web/raw/master/Microsoft-JhengHei.ttf
!wget -O ArialUnicodeMS.ttf https://github.com/texttechnologylab/DHd2019BoA/raw/master/fonts/Arial%20Unicode%20MS.TTF

# 設定字體
fm.fontManager.addfont('MicrosoftJhengHei.ttf')
matplotlib.rc('font', family='Microsoft Jheng Hei')

matplotlib.font_manager.fontManager.addfont('ArialUnicodeMS.ttf')
matplotlib.rc('font', family='Arial Unicode MS')
```

---

## 驗證安裝與版本管理

### 檢查安裝是否成功

完成安裝後，可以使用以下程式碼驗證是否安裝成功：

```python
# 檢查 zipline-tej 版本
!pip show zipline-tej
```

### 更新到最新版本

為確保使用最新功能和修復，建議定期更新：

```python
# 更新到最新版本
!pip install --upgrade zipline-tej
```

### 版本資訊

- [zipline-tej 最新版本](https://pypi.org/project/zipline-tej/)
- [完整更新日誌](https://github.com/tejtw/zipline-tej/releases)

---

## 下一步

🎉 **恭喜！您已成功安裝 TQuant Lab**

現在您可以：

<div class="grid cards" markdown>

-   :material-rocket-launch: **立即體驗**
    
    ---
    
    用 10 分鐘快速體驗 TQuant Lab 的核心功能
    
    [:octicons-arrow-right-24: 開始體驗](../quickstart/quick-demo.md)

-   :material-book-open-page-variant: **學習教學**
    
    ---
    
    跟著教學建立您的第一個投資策略
    
    [:octicons-arrow-right-24: 第一個策略](../quickstart/first-strategy.md)

-   :material-view-dashboard: **查看範例**
    
    ---
    
    瀏覽完整的分析流程與進階功能
    
    [:octicons-arrow-right-24: 分析流程](documents2.md)

-   :material-help-circle: **尋求協助**
    
    ---
    
    查看常見問題或聯絡技術支援
    
    [:octicons-arrow-right-24: 常見問題](documents37.md)

</div>

## 相關資源

- [TQuant Lab 官方網站](https://tquant.tejwin.com/) - 產品介紹與訂閱
- [GitHub 專案](https://github.com/tejtw/TQuant-Lab) - 程式碼與範例
- [TEJ 官方網站](https://www.tejwin.com/) - 資料服務與支援

---

💡 **安裝遇到問題？**

請查看[常見問題](documents37.md)或透過以下方式聯絡我們：

- 📧 Email：[support@tej.com.tw](mailto:support@tej.com.tw)  
- 💬 Discord：[加入討論區](https://discord.gg/C9KQRc7pnj)
- 📹 YouTube：[觀看教學影片](https://www.youtube.com/@TEJ)