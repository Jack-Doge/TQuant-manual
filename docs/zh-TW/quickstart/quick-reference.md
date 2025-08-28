# 常用功能速查

這是 TQuant Lab 最常使用功能的快速參考，讓您快速找到需要的程式碼。

## 📋 基礎設定

### 環境變數設定
```python
import os

# TEJ API 設定
os.environ['TEJAPI_KEY'] = "您的API金鑰"
os.environ['TEJAPI_BASE'] = "https://api.tej.com.tw"

# 股票與時間設定
os.environ['ticker'] = '2330 2454 2412'  # 股票代碼
os.environ['mdate'] = '20230101 20231231'  # 資料時間範圍
```

### 下載資料
```python
# 下載股價資料
!zipline ingest -b tquant
```

---

## 📈 策略開發

### 基本策略架構
```python
from zipline.api import *
from zipline import run_algorithm
import pandas as pd

def initialize(context):
    """初始化設定"""
    context.stocks = [symbol('2330')]
    
def handle_data(context, data):
    """每日交易邏輯"""
    order_target_percent(context.stocks[0], 1.0)

def analyze(context, perf):
    """結果分析"""
    return perf

# 執行回測
result = run_algorithm(
    start=pd.Timestamp('2023-01-01', tz='utc'),
    end=pd.Timestamp('2023-12-31', tz='utc'),
    initialize=initialize,
    handle_data=handle_data,
    analyze=analyze,
    capital_base=1000000,
    data_frequency='daily',
    bundle='tquant'
)
```

---

## 🛒 交易下單

### 常用下單函數
```python
# 指定股數
order(symbol('2330'), 1000)  # 買進1000股台積電

# 指定金額
order_value(symbol('2330'), 100000)  # 用10萬元買台積電

# 指定比例
order_target_percent(symbol('2330'), 0.5)  # 台積電占資產50%

# 清倉
order_target_percent(symbol('2330'), 0)  # 清空台積電
```

### 獲取持股資訊
```python
def handle_data(context, data):
    # 當前持股
    position = context.portfolio.positions[symbol('2330')]
    print(f"持股數量: {position.amount}")
    print(f"持股價值: {position.market_value}")
    
    # 投資組合總值
    print(f"總資產: {context.portfolio.total_value}")
```

---

## 📊 資料取得

### 價格資料
```python
def handle_data(context, data):
    stock = symbol('2330')
    
    # 當前價格
    current_price = data.current(stock, 'price')
    
    # 歷史價格（最近20天）
    prices = data.history(stock, 'price', 20, '1d')
    
    # 成交量
    volume = data.current(stock, 'volume')
```

### 技術指標計算
```python
def handle_data(context, data):
    stock = symbol('2330')
    
    # 移動平均線
    prices = data.history(stock, 'price', 20, '1d')
    sma_20 = prices.mean()
    
    # RSI指標（簡化版）
    returns = prices.pct_change().dropna()
    rsi = calculate_rsi(returns)  # 需自訂函數
```

---

## 📅 排程功能

### 定期調倉
```python
def initialize(context):
    # 每月第一個交易日調倉
    schedule_function(
        monthly_rebalance,
        date_rules.month_start(),
        time_rules.market_open()
    )

def monthly_rebalance(context, data):
    """月度調倉"""
    # 調倉邏輯
    pass
```

### 其他排程選項
```python
# 每週一
date_rules.week_start()

# 每月最後一天
date_rules.month_end()

# 特定日期
date_rules.on_date('2023-12-31')

# 收盤前執行
time_rules.market_close()
```

---

## 🎯 風險控制

### 基本風險設定
```python
def initialize(context):
    # 只做多，不放空
    set_long_only()
    
    # 不使用槓桿
    set_max_leverage(1.0)
    
    # 單一股票最大持股比例
    set_max_position_size(symbol('2330'), 0.1)  # 不超過10%
```

### 停損停利
```python
def handle_data(context, data):
    stock = symbol('2330')
    position = context.portfolio.positions[stock]
    
    if position.amount > 0:
        current_price = data.current(stock, 'price')
        cost_basis = position.cost_basis
        
        # 停損：虧損超過10%
        if current_price < cost_basis * 0.9:
            order_target_percent(stock, 0)
            
        # 停利：獲利超過20%
        elif current_price > cost_basis * 1.2:
            order_target_percent(stock, 0)
```

---

## 📈 績效分析

### 基本績效指標
```python
def analyze(context, perf):
    # 總報酬率
    total_return = (perf['portfolio_value'][-1] / perf['portfolio_value'][0] - 1) * 100
    
    # 年化報酬率
    days = len(perf)
    annual_return = ((perf['portfolio_value'][-1] / perf['portfolio_value'][0]) ** (252/days) - 1) * 100
    
    # 最大回撤
    rolling_max = perf['portfolio_value'].expanding().max()
    drawdown = (rolling_max - perf['portfolio_value']) / rolling_max
    max_drawdown = drawdown.max() * 100
    
    print(f"總報酬率: {total_return:.2f}%")
    print(f"年化報酬率: {annual_return:.2f}%")
    print(f"最大回撤: {max_drawdown:.2f}%")
```

### 視覺化結果
```python
import matplotlib.pyplot as plt

# 資產價值曲線
plt.figure(figsize=(12, 6))
plt.plot(result.index, result['portfolio_value'])
plt.title('投資組合價值變化')
plt.xlabel('時間')
plt.ylabel('資產價值')
plt.grid(True)
plt.show()

# 報酬率分布
returns = result['returns']
plt.figure(figsize=(10, 6))
plt.hist(returns, bins=50, alpha=0.7)
plt.title('日報酬率分布')
plt.xlabel('報酬率')
plt.ylabel('頻率')
plt.grid(True)
plt.show()
```

---

## 🔍 常見錯誤排除

### 資料問題
```python
# 檢查資料是否存在
def handle_data(context, data):
    stock = symbol('2330')
    if data.can_trade(stock):
        # 可以交易
        order_target_percent(stock, 0.5)
    else:
        print(f"{stock} 無法交易")
```

### 除錯技巧
```python
# 記錄變數
def handle_data(context, data):
    record(
        price_2330=data.current(symbol('2330'), 'price'),
        total_value=context.portfolio.total_value
    )

# 印出除錯資訊
def handle_data(context, data):
    if get_datetime().date() == pd.Timestamp('2023-06-01').date():
        print(f"除錯: 當前時間 {get_datetime()}")
        print(f"除錯: 投資組合價值 {context.portfolio.total_value}")
```

---

## 📚 進階學習資源

| 功能 | 快速連結 | 詳細文件 |
|------|----------|----------|
| 資料下載 | `!zipline ingest -b tquant` | [詳細說明](../example/documents3.md) |
| Pipeline分析 | `run_pipeline()` | [Pipeline教學](../example/documents16.md) |
| 視覺化報告 | `pyfolio.create_tears()` | [Pyfolio使用](../example/documents4.md) |
| 因子分析 | `alphalens.tears` | [Alphalens教學](../example/documents5.md) |

---

💡 **找不到需要的功能？**
查看[完整 API 文件](../example/documents13.md)或在[技術手冊](../example/documents1.md)中搜尋關鍵字。
