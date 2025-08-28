# 10分鐘體驗 TQuant Lab

快速體驗 TQuant Lab 的核心功能，立即看到一個完整的投資策略回測結果。

!!! success "學習目標"
    完成這個體驗後，您將：
    
    - ✅ 成功執行第一次策略回測
    - ✅ 看到策略績效圖表
    - ✅ 理解 TQuant Lab 的基本運作流程

## 前置準備

確保您已完成[環境建置](setup.md)。

## 步驟1：一鍵下載資料（2分鐘）

```python
# 設定環境變數
import os
os.environ['TEJAPI_KEY'] = "您的API金鑰"  # 請替換成您的金鑰
os.environ['TEJAPI_BASE'] = "https://api.tej.com.tw"

# 設定要分析的股票和時間
os.environ['ticker'] = '2330'  # 台積電
os.environ['mdate'] = '20220101 20231231'  # 2022-2023年資料

# 一鍵下載資料
!zipline ingest -b tquant
```

!!! tip "取得 API 金鑰"
    如果您還沒有 API 金鑰，請到 [TEJ 官網](https://www.tejwin.com/) 申請。

## 步驟2：建立買進持有策略（3分鐘）

這是最簡單的投資策略：買進股票後一直持有。

```python
from zipline.api import order_target_percent, record, symbol
from zipline import run_algorithm
import pandas as pd

def initialize(context):
    """初始化策略"""
    context.asset = symbol('2330')  # 台積電
    
def handle_data(context, data):
    """每日交易邏輯"""
    # 將100%資金投入台積電
    order_target_percent(context.asset, 1.0)
    
    # 記錄當前股價
    record(price=data.current(context.asset, 'price'))

def analyze(context, perf):
    """分析結果"""
    print("策略執行完成！")
    print(f"總報酬率: {(perf['portfolio_value'][-1] / perf['portfolio_value'][0] - 1) * 100:.2f}%")
    return perf
```

## 步驟3：執行回測（2分鐘）

```python
# 執行策略回測
result = run_algorithm(
    start=pd.Timestamp('2022-01-01', tz='utc'),
    end=pd.Timestamp('2023-12-31', tz='utc'),
    initialize=initialize,
    handle_data=handle_data,
    analyze=analyze,
    capital_base=1000000,  # 起始資金100萬
    data_frequency='daily',
    bundle='tquant'
)
```

## 步驟4：查看結果（3分鐘）

```python
# 查看策略表現
import matplotlib.pyplot as plt

# 繪製資產價值變化
plt.figure(figsize=(12, 6))
plt.plot(result.index, result['portfolio_value'])
plt.title('投資組合價值變化')
plt.xlabel('時間')
plt.ylabel('資產價值 (元)')
plt.grid(True)
plt.show()

# 顯示關鍵績效指標
print("=" * 50)
print("投資績效摘要")
print("=" * 50)
print(f"期初資金: {result['portfolio_value'][0]:,.0f} 元")
print(f"期末資產: {result['portfolio_value'][-1]:,.0f} 元")
print(f"總報酬: {(result['portfolio_value'][-1] - result['portfolio_value'][0]):,.0f} 元")
print(f"報酬率: {((result['portfolio_value'][-1] / result['portfolio_value'][0]) - 1) * 100:.2f}%")
```

## 🎉 完成體驗！

恭喜！您已經成功：

1. ✅ 下載了台積電的歷史資料
2. ✅ 建立了買進持有策略  
3. ✅ 執行了完整的回測分析
4. ✅ 產生了績效報表

## 下一步

現在您已經體驗了 TQuant Lab 的核心功能，接下來可以：

- 📚 學習[建立第一個策略](first-strategy.md)
- 🔧 探索更多[資料處理](../example/documents3.md)功能
- 📊 了解[進階分析工具](../example/documents4.md)

---

💡 **想要更詳細的說明？**
查看[完整的回測範例](../example/documents55.md)
