# Pairs trading with Ornstein-Uhlenbeck process in crypto market
### Team members:楊子逸

# 1. Motivation:

現今配對交易的論文與方向都是往共整合或是深度學習的方式，前者是利用統計套利獲取市場中性報酬，後者結合電腦運算速度仰賴電腦高速運算的高頻交易，快速賺取市場當中的 alpha（超額報酬），而本次交易策略的標地不再是傳統金融商品，改以加密貨幣市場，主要使用前三十大的幣種作為交易對，並使用OU Process 的隨機過程，  在策略中對共變異數殘差（展開）建模的應用，使用加密貨幣的市場的優勢有以下三點：

1. 市場 24 小時進行交易，有訊號可以馬上進行
2. 波動度大，配對交易主要是均值回歸，若快速回歸，且平倉，交易次數可以提升
3. 資本不需太多，不受傳統金融市場的最小單位限制

最重要的是希望著次交易策略是可以確實獲利並上架的，沒有要求打敗市場得到收高收益，只求穩定報酬，以及極小的回撤，同時也是選擇配對交易的目的。

參考論文：  Avellaneda, Lee,(2008) Statistical Arbitrage in the U.S. Equity Market. Quantitative Finance 

# 2. Data

![截圖 2022-12-28 上午1.11.55.png](Pairs%20trading%20with%20Ornstein-Uhlenbeck%20process%20in%20c%203c2f089d0b3645dbb48f9ae551a4bfc5/%25E6%2588%25AA%25E5%259C%2596_2022-12-28_%25E4%25B8%258A%25E5%258D%25881.11.55.png)

幣安交易所： 從圖表可以看出幣安交易所的每日交易量將近是第二名交易所的十倍。

- 幣安價格反應最快，被套利機會少
- 流動量最大，執行配對交易時，較容易交易到及時價格
- 幣種選擇方式
    1. 當前前 50 大幣種
        1. 確保未來可以繼續以此幣進行交易
    2. 兩年來每月的 50 大幣種
        1. 選出**兩年來至今具有高度流動性**的幣
    3. 扣除穩定幣
        1. 已經由 Ｕ本位作為交易方式，因此不需其他穩定幣
    
    結果如下：
    
    > **`ADA, ATOM, BCH, BNB, BTC, CRO, DOGE, ETH,
    LINK, LTC, TRX, VET, XLM, XMR, XRP, XTZ`**
    > 

![截圖 2022-12-28 下午2.10.55.png](Pairs%20trading%20with%20Ornstein-Uhlenbeck%20process%20in%20c%203c2f089d0b3645dbb48f9ae551a4bfc5/%25E6%2588%25AA%25E5%259C%2596_2022-12-28_%25E4%25B8%258B%25E5%258D%25882.10.55.png)

# 3. Formulation

Statistical Arbitrage in the U.S. Equities  Market Avellaneda 和 Lee

統計套利策略有三個特稱：

- 交易信號是**基於系統或基於規則**，而不是由基本面驅動
- 交易**市場中性的**，因為它與市場的貝塔係數為零
- 產生**超額回報**的機制是統計的

## A. 建構模型

$$
\frac{dP_t}{P_t}=\alpha dt+ {\beta_j \frac{dQ_t}{Q_t}} + dX_t
$$

- $*P$* 和 $Q$ 為幣種價格
- $\alpha$ **是 drift term（通常很小，可以忽略不計）
- $***X*$是平穩的均值回歸過程**
    - If  *$X_t < 0$*，**表示 $*P$  低估 $Q$高估***
        - buy  1 元 $*P$*  sell  $\beta$ 元 $Q$
    - If  $*X_t > 0*$， **表示 $*Q$低估 $P$高估***
        - sell 1 元  $*P$  buy*  $\beta$  **元 $Q$

現在擴大 $Q$  的選擇，存在 n 個 Q 可以做到與上式相同的狀況，就如同下式 $F$ **代表其他幣種與$Q$幣種的回報

$$
\frac{dP_t}{P_t}=\alpha dt+ \sum_{j=1}^{n} {\beta_j F_t^{(j)}} + dX_t
$$

> **Note: 幣圈最常考慮的是 BTC**
> 

## B. Ornstein-Uhlenbeck Model

已知若幣種有均值回歸的性質，且會符合上述的形式，其關注重點會是在 $***X$ ，***要如何收斂？收斂速度多快？

這裡使用 OU model ，也就是**平穩的均值回歸過程服從 OU process**

$$
dX_i(t) = \kappa_i(m_i-X_i(t))dt+\sigma_idW_i(t), \kappa_i > 0
$$

<aside>
💡 **估計參數準不準，以及均值回歸速度快不快將左右交易策略績效！**

</aside>

## C. Pure mean-reversion

$$
\sigma_{eq,i} = \frac{\sigma_i}{\sqrt{2\kappa_i}} = \sigma_i \sqrt{\frac{\tau_i}{2}}
$$

$$
s_i = \frac{X_i(t)-m_i}{\sigma_{eq,i}}        
$$

- $s_i$  為 s-score ，用來**衡量$X$ 與平均值的距離**
    - **s-score 將是本交易測略判斷進出場的條件**

## D. 風險因素

尋找哪些風險因素（或資產）來分解幣種收益

本次交易策略測試了兩種不同的方式：

- 使用 BTC 作為大盤趨勢
- ~~基於 PCA 的投資組合~~

# 4. Analysis

## A. 交易步驟：

1. Step 1：每支幣對大盤(BTC) 進行回歸，設定一滾動週期 Rolling 
2. Step 2：計算每支幣回歸係數 $\beta$
3. Step 3：計算殘差，估計 OU Process 的參數( $\kappa_i, m_i, \sigma_i , \sigma_{eq,i}...$)
4. Step 3：計算殘差，估計 OU Process 的參數( $\kappa_i, m_i, \sigma_i , \sigma_{eq,i}...$)
5. Step 4：均值回歸速度足夠快的股票 ( $\kappa_i$ *> 252/30)*計算*s-scores*
6. Step 5：計算 ***s-scores*** 當作交易訊號
    
    ![截圖 2022-12-28 下午2.04.40.png](Pairs%20trading%20with%20Ornstein-Uhlenbeck%20process%20in%20c%203c2f089d0b3645dbb48f9ae551a4bfc5/%25E6%2588%25AA%25E5%259C%2596_2022-12-28_%25E4%25B8%258B%25E5%258D%25882.04.40.png)
    
7. Step 6：分配權重
    
    配對交易不是賺取 $\beta$ 是超額報酬 $\alpha$(本文的 alpha)
    
    1. 手中持有部位應為 1 多 1 空，達到避險需求
    2. 若訊號同時存在則分配等權重在不同部位上
        
        > eg. 若同時存在 3 個頭，2個空頭訊號，手中職有部位應該是每個多頭為 0.333權重，每個空頭為 0.5權重，以此符合規則a
        > 
        > 
        > ![截圖 2022-12-28 下午2.05.32.png](Pairs%20trading%20with%20Ornstein-Uhlenbeck%20process%20in%20c%203c2f089d0b3645dbb48f9ae551a4bfc5/%25E6%2588%25AA%25E5%259C%2596_2022-12-28_%25E4%25B8%258B%25E5%258D%25882.05.32.png)
        > 

## B. 實測結果

### I. 超參數

| 參數列表 | 參數數值 |
| --- | --- |
| 交易頻率 | 1d / 4h / 2h /1h |
| Rolling 週期 | 20/40/60/120 |
| 均值回歸速度  |  $\kappa_i$ > 252/30 沒參與超參數 |
| 進場訊號 s-score | +- 1.25~2 |
| 出場訊號 | +- 1~0.75 |

### **II. 績效成果**

超參數的最佳結果

![1D K / 60 */+-1.25 /+-1*](Pairs%20trading%20with%20Ornstein-Uhlenbeck%20process%20in%20c%203c2f089d0b3645dbb48f9ae551a4bfc5/%25E6%2588%25AA%25E5%259C%2596_2022-12-28_%25E4%25B8%258B%25E5%258D%25883.04.22.png)

1D K / 60 */+-1.25 /+-1*

![2H K/ 120/ *+-1.25 /+-1*](Pairs%20trading%20with%20Ornstein-Uhlenbeck%20process%20in%20c%203c2f089d0b3645dbb48f9ae551a4bfc5/%25E6%2588%25AA%25E5%259C%2596_2022-12-28_%25E4%25B8%258B%25E5%258D%25883.01.49.png)

2H K/ 120/ *+-1.25 /+-1*

![4H K  / 30 /*+-1.25 /+-1*](Pairs%20trading%20with%20Ornstein-Uhlenbeck%20process%20in%20c%203c2f089d0b3645dbb48f9ae551a4bfc5/%25E6%2588%25AA%25E5%259C%2596_2022-12-28_%25E4%25B8%258B%25E5%258D%25883.01.18.png)

4H K  / 30 /*+-1.25 /+-1*

![1H K/ 60/ *+-1.25 /+-1*](Pairs%20trading%20with%20Ornstein-Uhlenbeck%20process%20in%20c%203c2f089d0b3645dbb48f9ae551a4bfc5/%25E6%2588%25AA%25E5%259C%2596_2022-12-28_%25E4%25B8%258B%25E5%258D%25883.09.58.png)

1H K/ 60/ *+-1.25 /+-1*

### III. 額外發現

### **反向操作不做回歸**

`本權益曲線跟一坨屎沒兩樣`

![反向操作對交易的結果有明顯不同的pattern，因此可以嘗試不要賺取均值迴歸，調整適當的參數有機會成為一個CTA 策略。](Pairs%20trading%20with%20Ornstein-Uhlenbeck%20process%20in%20c%203c2f089d0b3645dbb48f9ae551a4bfc5/%25E6%2588%25AA%25E5%259C%2596_2022-12-28_%25E4%25B8%258B%25E5%258D%25883.00.13.png)

反向操作對交易的結果有明顯不同的pattern，因此可以嘗試不要賺取均值迴歸，調整適當的參數有機會成為一個CTA 策略。

# 5. Conclusion Future improvement direction

## `整個績效差到不行，但不代表是一個完全不行的策略`

- 論文的市場是傳統金融市場，其用的大盤是ETF，對個別股數本高相關
    - 加密貨幣場常只看大哥BTC ，可依照產業分類再進行配對交易，增加迴歸的相關性。
        
        ![[https://crypto.com/price/categories](https://crypto.com/price/categories) 註：台灣股市日交易量為 7Billion USD](Pairs%20trading%20with%20Ornstein-Uhlenbeck%20process%20in%20c%203c2f089d0b3645dbb48f9ae551a4bfc5/%25E6%2588%25AA%25E5%259C%2596_2022-12-29_%25E4%25B8%258A%25E5%258D%258810.28.13.png)
        
        [https://crypto.com/price/categories](https://crypto.com/price/categories) 註：台灣股市日交易量為 7Billion USD
        
    - BTC 並不是完美的大盤指數，非每個對幣對 BTC 具有高相關，應驗證兩者的統計關係
        - 以 BTC 為大盤，篩選僅對 BTC 有相關
            
            > 可以對整段時間做共整合分析篩選交易對
            > 
        - 幣幣兩兩之間也能進行配對交易
            
            > eg: LTC 與 ETH 有均值回歸，如果 LTC/ETH 交易量夠大，則不已 LTC/USDT、ETH/USDT 交易 （須考量手續費用與滑價）
            > 

---

- S-score 與進出場條件：
    - S-score 計算是基於一些條件分配，且計算的是$X$ 與平均值的距離，可以考慮更改分配與 Metric 衡量方式
    - $\kappa$  為返回均值的速度，由於加密貨幣波動度大，速度的趨緩也許是一個進出場的方式，避免馬上反彈變化，在低頻率 k 可衡會吃不到利潤

---

- 小時 k 對本策略的反應最好
    - 頻率低的 k 會**無法即時反映**均值回歸效應，就算有也消縱即逝，造成**交易落後的資訊**。
        
        > 配對交易宗旨是賺取遠離均值迴歸的報酬，若在市場上存在多位套利者，機會本就容易稍縱即逝，**因此理論上越高頻，越容易吃到利潤，但也須考量交易成本。**
        > 

# Conclusion **Future improvement direction**

### Reference: Statistical Arbitrage in the U.S. Equity Market( Avellaneda,  Lee,2008)

[https://medium.com/@financialnoob/pairs-trading-with-ornstein-uhlenbeck-process-part-2-225bec1aea20](https://medium.com/@financialnoob/pairs-trading-with-ornstein-uhlenbeck-process-part-2-225bec1aea20)

[https://medium.com/@financialnoob/pairs-trading-with-ornstein-uhlenbeck-process-part-2-225bec1aea20](https://medium.com/@financialnoob/pairs-trading-with-ornstein-uhlenbeck-process-part-2-225bec1aea20)
