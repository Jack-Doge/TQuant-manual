# Data Collection

本課程實作如何將資料（如價量、基本面、籌碼面等）ingest 到 zipline 回測引擎中。透過這個動作，後續使用 Pipeline API 時，就可以很有效率地獲取資料並計算因子。

## 載入所需套件
```python
import os
```

## Bundle 設置

介紹環境變數設定，並運用設定的環境變數做價量、非價量資料的 bundle。

**在 import zipline 前，必須先設定以下環境變數(os.environ)：**

**價量資料 bundle（tquant bundle）設定**

- os.environ['TEJAPI_BASE'] = "https://api.tej.com.tw"
用於連結TEJ資料庫

- os.environ['TEJAPI_KEY'] = "Your_Key"
用於識別使用者

- os.environ['ticker']
tickers = 'xxxx xxxx xxxx' os.environ['ticker'] = tickers 設置我們所需要的股票代碼

- os.environ['mdate'] = 'yyyymmdd yyyymmdd'
設置取得資料的起始時間與結束時間

- !zipline ingest -b tquant 將上方資料 ingest 進 bundle。

**設置非價量資料 bundle（fundamentals bundle）需要的資訊：**

- os.environ['fields'] = 'field1, field2, field3'
設置撈取非價量資料的欄位，其中 field1、field2、field3 代表資料欄位名稱

- os.environ['include_self_acc'] = 'N'(預設)
設置是否涵蓋公司自結財務，其中設定'Y'代表財務資料包含自結財務，'N'則表示不包含

- !zipline ingest -b fundamentals 將非價量資料 ingest 進 bundle。

## 價量資料

- 價量資料指 OHLCV 與除權息相關資訊。以下範例載入指定股票價量資料：
載入指定股票的價量資料，其中 IR0001 為台灣發行量加權股價報酬指數。

```python
tickers = 'IR0001 0050 0056 00737 1108 1101 1102 1103 6243 6451 1216 1301 1303 1326 1402 1476 1590 1605 1722 1802 2002 2105 2201 2207 2227 2301 2939 4108 4148 6431 6541 6657 2883 2891 2543 2929 2303 6505 9926 2308 2311 2317 2324 2325 2327 2330 2347 2353 2354 2357 2379 2382 2395 2408 2409 2412 2448 2454 2474 2492 2498 2603 2609 2615 2618 2633 2801 2823 2880 2881 2882 2883 2884 2885 2886 2887 2888 2890 2891 2892 2912 3008 3009 3034 3037 3045 3231 3474 3481 3673 3697 3711 4904 4938 5854 5871 5876 5880 6239 6415 6505 6669 6770 8046 8454 9904 9910'
print("總共有 :",len(tickers.split()),"筆資料")


os.environ["TEJAPI_BASE"] = 'https://api.tej.com.tw'
os.environ['TEJAPI_KEY'] = "Your_Key"
os.environ['ticker'] = tickers
os.environ['mdate'] = '20200103 20230530'

!zipline ingest -b tquant
```


## 簡易 ingest 價量資料

simple_ingest()函數提供另一種取得指定股票價量資料的方法，資料同樣會存入 bundle 中：

simple_ingest()函數載入價量資料所需參數資訊如下：

- name ( str ) = 'tquant'
定義 bundle 名稱

- tickers ( list or str ) = ['xxxx', 'xxxx', 'xxxx'] or 'xxxx xxxx xxxx'
設置我們需要的股票代碼

- start_date ( str ) = 'yyyy-mm-dd'
設置起始日期

- end_date ( str ) = 'yyyy-mm-dd'
設置結束日期

```python
from zipline.data.run_ingest import simple_ingest

start = '2020-01-03'
end = '2023-05-30'

simple_ingest(name = 'tquant',
              tickers = tickers,
              start_date = start,
              end_date = end
              )
```

## 更新價量資料

透過輸入 zipline update -b tquant 指令，可以更新當前使用的 tquant bundle，以獲得最新的交易價格和交易量資訊。

```bash
# 執行前
!zipline bundle-info
```
```bash
!zipline update -b tquant
```
```bash
# 執行後
!zipline bundle-info
```


## 新增價量資料

使用 `zipline add -t <tickers_want_to_add>` 指令，可以向現有的 tquant bundle 中新增所選的股票。

```bash
# 執行前
!zipline bundle-info
```
```bash
!zipline add -t "6523 6208"
```
```bash
# 執行後
!zipline bundle-info
```

# Data

運用 TEJTOOLAPI 整併資料。TEJTOOLAPI 主要整併股價與不同屬性的資料庫，透過輸入股票代碼 (TICKERS) 和欄位 (FIELD) 後，可將欲抓取的資料整併為以日頻率的 DataFrame。

- [Data Preprocess](#Data_preprocess)

本課程實作如何將資料（如價量、基本面、籌碼面...等）ingest 到 zipline 回測引擎中。
透過這個動作，後續使用 Pipeline API 時，就可以很有效率地獲取資料並計算因子。

- [Data Collection（價量資料）](#Data_collection_pv)
- [Data Collection(非價量資料)](#Data_collection_not_pv)