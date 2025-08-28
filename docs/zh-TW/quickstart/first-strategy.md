# 建立第一個投資策略

學會建立投資策略是使用 TQuant Lab 的核心技能。這裡將帶您從零開始，建立一個簡單但實用的投資策略。

!!! success "學習目標"
    完成這個教學後，您將學會：
    
    - ✅ 投資策略的基本架構
    - ✅ 如何設定策略邏輯
    - ✅ 如何執行策略回測
    - ✅ 如何分析策略結果

## 策略範例：價值投資策略

我們要建立一個基於本益比（PE ratio）的價值投資策略：
- 買進本益比低的股票（被低估）
- 賣出本益比高的股票（被高估）

## 步驟1：準備資料

```python
import os
import pandas as pd
from zipline.api import *
from zipline import run_algorithm

# 設定 TEJ API
os.environ['TEJAPI_KEY'] = "您的API金鑰"
os.environ['TEJAPI_BASE'] = "https://api.tej.com.tw"

# 設定股票池：台灣50成分股前10名
os.environ['ticker'] = '2330 2454 2412 2382 2881 2308 2303 1301 1303 2886'
os.environ['mdate'] = '20230101 20231231'

# 下載資料
!zipline ingest -b tquant
```

## 步驟2：定義策略架構

投資策略包含三個核心函數：

### initialize - 初始化設定
```python
def initialize(context):
    """策略初始化設定"""
    # 設定股票池
    context.stocks = [
        symbol('2330'), symbol('2454'), symbol('2412'), 
        symbol('2382'), symbol('2881')
    ]
    
    # 設定調倉頻率：每月第一個交易日
    schedule_function(
        rebalance,  # 調倉函數
        date_rules.month_start(),  # 每月開始
        time_rules.market_open()   # 開盤時執行
    )
    
    # 記錄變數
    context.pe_threshold = 15  # 本益比門檻
```

### rebalance - 調倉邏輯
```python
def rebalance(context, data):
    """每月調倉邏輯"""
    # 獲取當前價格資料
    prices = data.current(context.stocks, 'price')
    
    # 這裡簡化處理：假設獲得本益比資料
    # 實際應用中會從 TEJ 資料庫取得財務指標
    pe_ratios = {
        symbol('2330'): 18,  # 台積電
        symbol('2454'): 12,  # 聯發科  
        symbol('2412'): 25,  # 中華電
        symbol('2382'): 8,   # 廣達
        symbol('2881'): 10,  # 富邦金
    }
    
    # 找出低本益比股票（價值股）
    value_stocks = []
    for stock in context.stocks:
        if pe_ratios.get(stock, 999) < context.pe_threshold:
            value_stocks.append(stock)
    
    # 平均分配資金到價值股
    target_percent = 1.0 / len(value_stocks) if value_stocks else 0
    
    for stock in context.stocks:
        if stock in value_stocks:
            order_target_percent(stock, target_percent)
        else:
            order_target_percent(stock, 0)  # 清倉高估股票
    
    # 記錄持股
    record(num_positions=len(value_stocks))
```

### analyze - 結果分析
```python
def analyze(context, perf):
    """分析策略結果"""
    print("=" * 50)
    print("價值投資策略 - 回測結果")
    print("=" * 50)
    
    # 計算關鍵指標
    total_return = (perf['portfolio_value'][-1] / perf['portfolio_value'][0] - 1) * 100
    max_drawdown = (perf['portfolio_value'].expanding().max() - perf['portfolio_value']).max()
    max_drawdown_pct = (max_drawdown / perf['portfolio_value'].expanding().max()).max() * 100
    
    print(f"總報酬率: {total_return:.2f}%")
    print(f"最大回撤: {max_drawdown_pct:.2f}%")
    print(f"期末資產: {perf['portfolio_value'][-1]:,.0f} 元")
    
    return perf
```

## 步驟3：執行策略回測

```python
# 執行完整策略
result = run_algorithm(
    start=pd.Timestamp('2023-01-01', tz='utc'),
    end=pd.Timestamp('2023-12-31', tz='utc'),
    initialize=initialize,
    handle_data=lambda context, data: None,  # 不需要每日邏輯
    analyze=analyze,
    capital_base=1000000,  # 100萬起始資金
    data_frequency='daily',
    bundle='tquant'
)
```

## 步驟4：視覺化結果

```python
import matplotlib.pyplot as plt

# 繪製策略表現
fig, (ax1, ax2) = plt.subplots(2, 1, figsize=(12, 10))

# 資產價值變化
ax1.plot(result.index, result['portfolio_value'], label='策略表現', linewidth=2)
ax1.set_title('價值投資策略 - 資產價值變化', fontsize=14)
ax1.set_ylabel('資產價值 (元)')
ax1.grid(True, alpha=0.3)
ax1.legend()

# 持股數量變化
ax2.plot(result.index, result['num_positions'], label='持股檔數', color='orange')
ax2.set_title('持股數量變化', fontsize=14)
ax2.set_xlabel('時間')
ax2.set_ylabel('持股檔數')
ax2.grid(True, alpha=0.3)
ax2.legend()

plt.tight_layout()
plt.show()
```

## 策略優化建議

完成基本策略後，您可以嘗試以下優化：

### 1. 加入更多財務指標
```python
# 除了本益比，還可考慮：
# - 股價淨值比 (PB ratio)
# - 股東權益報酬率 (ROE)
# - 負債比率
```

### 2. 動態調整持股權重
```python
# 根據指標強度調整權重
def calculate_weights(pe_ratios):
    # PE越低，權重越高
    weights = {stock: 1/pe for stock, pe in pe_ratios.items()}
    total = sum(weights.values())
    return {stock: w/total for stock, w in weights.items()}
```

### 3. 加入風險控制
```python
def initialize(context):
    # 設定停損停利
    set_max_leverage(1.0)  # 不使用槓桿
    set_long_only()        # 只做多
```

## 下一步學習

🎉 恭喜完成第一個投資策略！接下來您可以：

- 📊 學習[更多資料處理](../example/documents3.md)技巧
- 🧮 探索[Pipeline 工具](../example/documents16.md)進行因子分析
- 📈 使用[視覺化工具](../example/documents4.md)產生專業報表

---

💡 **想看更多策略範例？**
查看[進階策略範例](../example/documents35.md)
