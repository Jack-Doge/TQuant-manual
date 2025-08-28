# 環境建置

開始使用 TQuant Lab 之前，需要先建置開發環境。我們提供三種安裝方式，**推薦使用 Docker 方式**。

!!! success "建置目標"
    完成環境建置後，您將擁有：
    
    - ✅ 完整的 TQuant Lab 開發環境
    - ✅ Jupyter Notebook 介面
    - ✅ 所有必要的 Python 套件
    - ✅ TEJ 資料庫連接能力

## 方式一：Docker 安裝（推薦，最簡單）

### 為什麼推薦 Docker？
- 🚀 **一鍵安裝**：無需處理複雜的套件相依性
- 🔒 **環境隔離**：不會影響您現有的 Python 環境
- 📦 **完整配套**：內建所有必要工具

### 安裝步驟

#### 1. 安裝 Docker Desktop
- [Windows 下載](https://www.docker.com/products/docker-desktop/)
- [Mac 下載](https://www.docker.com/products/docker-desktop/)

#### 2. 下載 TQuant Lab 映像檔
```bash
docker pull tej87681088/tquant:latest
```

#### 3. 建立資料儲存空間
```bash
docker volume create tquant-data
```

#### 4. 啟動 TQuant Lab
```bash
docker run -v tquant-data:/app -p 8888:8888 --name tquant tej87681088/tquant
```

#### 5. 開始使用
1. 等待終端機顯示類似網址：`http://127.0.0.1:8888/tree?token=XXXXX`
2. 複製網址到瀏覽器
3. 開始使用 Jupyter Notebook！

!!! tip "重啟容器"
    如果容器關閉了，可以用以下指令重新啟動：
    ```bash
    docker start tquant
    docker logs --tail 3 tquant  # 查看登入網址
    ```

---

## 方式二：Anaconda 安裝（適合 Python 使用者）

### 安裝步驟

#### 1. 下載環境設定檔
- [Windows 版本：zipline-tej.yml](https://github.com/tejtw/zipline-tej/blob/main/zipline-tej.yml)
- [Mac 版本：zipline-tej_mac.yml](https://github.com/tejtw/zipline-tej/blob/main/zipline-tej_mac.yml)

#### 2. 建立虛擬環境

=== "Windows"
    ```bash
    cd C:\Users\您的使用者名稱\Downloads
    conda env create -f zipline-tej.yml
    conda activate zipline-tej
    ```

=== "Mac"
    ```bash
    cd /Users/您的使用者名稱/Downloads
    conda env create -f zipline-tej_mac.yml
    conda activate zipline-tej
    ```

#### 3. 啟動 Jupyter
```bash
jupyter notebook
```

---

## 方式三：pip 安裝（進階使用者）

!!! warning "注意"
    此方式可能遇到套件相依性問題，建議有經驗的使用者使用。

### 建立虛擬環境
```bash
# 建立環境
conda create -n tquant-env python=3.11
conda activate tquant-env

# 安裝套件
pip install zipline-tej
pip install notebook
```

---

## 驗證安裝

無論使用哪種方式，都可以用以下程式碼驗證安裝：

```python
# 檢查版本
import zipline
print(f"Zipline 版本: {zipline.__version__}")

# 檢查 TEJ 連接
import os
os.environ['TEJAPI_KEY'] = "your_api_key"  # 請替換
os.environ['TEJAPI_BASE'] = "https://api.tej.com.tw"

print("✅ 環境建置完成！")
```

## 取得 API 金鑰

使用 TQuant Lab 需要 TEJ 的 API 金鑰：

1. 前往 [TEJ 官網](https://www.tejwin.com/)
2. 註冊帳號
3. 申請 TQuant Lab 授權
4. 取得 API 金鑰

## 下一步

環境建置完成後，立即進行 [10分鐘體驗](quick-demo.md)，看看 TQuant Lab 的神奇效果！

---

💡 **遇到安裝問題？**
查看[詳細安裝教學](../example/documents1.md)或[常見問題](../example/documents37.md)
