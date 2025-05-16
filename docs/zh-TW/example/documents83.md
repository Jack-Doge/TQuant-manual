# 滑價模型介紹
* 交易成本（transaction costs）被廣泛認為是影響投資績效的重要因素。它們不僅影響投資績效，還影響了將資產轉換成現金的難易度。

* 在真實世界的交易中存在許多種類的交易成本，其中一種是間接成本（indirect cost），間接成本包含了滑價（slippage）、流動性（liquidity）等。因為股價隨時都在變動，下單時的些微時間差也可能造成預期的價格與成交價有落差，而這個價差就是滑價。流動性則會影響交易的難易度，我們通常可以用交易量來間接評估流動性。若股票平均來說交易量高，則通常代表該股票流動性高、可以迅速進行交易，同時滑價的影響也會降低。

* 若沒有考慮滑價及流動性可能會高估投資策略的獲利，特別是在投資組合中有成交量較低（流動性差）的股票、資金量（capital base）大或過度集中交易單一個股時，影響會更為明顯。這也是回測（backtesting）的一大目的，考量投資策略在真實世界運行的可能性。


## zipline.api.set_slippage(self, equities=None, futures=None)
設定回測時所使用的滑價模型。

## Parameters：
* equities (EquitySlippageModel, optional) －用於交易股票的手續費模型。
    * EquitySlippageModel：`zipline.finance.slippage`

* futures (FutureSlippageModel, optional) －用於交易期貨的手續費模型。（目前不支援）
    * FutureSlippageModel：`zipline.finance.slippage`

## Raises：
SetSlippagePostInit－`slippage` 只能在 initialize 階段使用。

## Notes：
* `set_slippage` 只能一次用一種方法。

## 手續費模型共有三種：
* `zipline.finance.commission.FixedSlippage`
* `zipline.finance.commission.VolumeShareSlippage`
* `zipline.finance.commission.FixedBasisPointsSlippage`

## Examples：
```python
from zipline.api import set_slippage
from zipline.finance import slippage

def initialize(context):
    set_slippage(slippage.<其中一種 slippage models>)
```