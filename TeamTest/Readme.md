# Test
### TA
# ML and FinTech Project:</br>玉山競賽 - 信用卡消費類別推薦

#### Keyword : multiclass classification

## Motivations
受到疫情影響，網路購物、行動裝置普及等因素，108年度零售業消費出現了變化，信用卡消費佔首次超過現金支付，佔比達 37.9%，超過現金交易的 35.1%。

台灣各銀行的信用卡流通量也高達5000多萬張，是台灣總人口數的2倍。因此，在台灣，多數人都有用信用卡消費的經驗，銀行也推出各式各樣的行銷活動，期望吸引消費者使用該銀行信用卡。

若能利用信用卡消費資料精準預測顧客最感興趣的消費類別並對其推銷，勢必能為銀行帶來龐大的效益。

玉山競賽連結：[https://tbrain.trendmicro.com.tw/Competitions/Details/18?utm_source=prof_list&utm_medium=email&utm_campaign=EAOC2021](https://tbrain.trendmicro.com.tw/Competitions/Details/18?utm_source=prof_list&utm_medium=email&utm_campaign=EAOC2021)
:::info
目標 : 預測未來一個月，每一位顧客在16種信用卡消費類別中，前三高的消費類別。
:::


## EDA
### Variable descriptions 
Variable|Description|Type|
|-----|----------|-------|
|dt|消費月份|int64|
|chid|顧客編號|int64|
|shop_tag|消費類別|object|
|txn_cnt|	消費次數|int64|
|txn_amt|	消費金額（經過神秘轉換）|float64|
|domestic_offline_cnt|	國內實體通路消費次數|int64|
|domestic_online_cnt|	國內線上通路消費次數|int64|
|overseas_offline_cnt|	海外實體通路消費次數|int64|
|overseas_online_cnt|	海外線上通路消費次數|int64|
|domestic_offline_amt_pct|	國內實體通路消費金額佔比|float64 |
|domestic_online_amt_pct|	國內線上通路消費金額佔比|float64  |
|overseas_offline_amt_pct|	海外實體通路消費金額佔比|float64 |
|overseas_online_amt_pct|   海外線上通路消費金額佔比|float64 |
|card_i_txn_cnt|	卡片i消費次數(i=1~14)|int64  |
|card_other_txn_cnt|	其他卡片消費次數|int64 |
|card_i_txn_amt_pct|	卡片i消費金額佔比(i=1~14)|float64|
|card_other_txn_amt_pct|	其他卡片消費金額佔比| float64|
|masts|	婚姻狀態| float64|
|educd|	學歷代碼| float64|
|trdtp|	行業別| float64|
|naty|	國籍| float64|
|poscd|	職位別| float64|
|cuorg|	客戶來源| float64|
|slam|	正卡信用額度（經過神秘轉換）| float64|
|gender_code|	性別代碼| float64|
|age|	年紀| float64|
|primary_card|	正附卡註記|int64|
### data.nunique()
:::info
去除 需預測種類(16種) 以外的資料
消費種類(shop_tag) : 16
月份數量(dt) : 24
顧客數量(chid) : 498040
:::
![](https://i.imgur.com/SgUdPtz.jpg)
![](https://i.imgur.com/NTa6LI5.jpg)

### 月份v.s.信用卡消費金額
*去除極端值(txn_amt/txn_cnt > 1,000,000)
#### 1.各類別 信用卡月消費總金額 的時間變化
![](https://i.imgur.com/0NCoM4W.jpg)
:::info
初期相較於後期，刷卡金額高、交易筆數也較多
:::
#### 2.各類別 信用卡月消費平均金額 的時間變化
![](https://i.imgur.com/5SGU8Cc.jpg)
:::info
類別2、6、12、19、21有規律的上下震盪，可能是有固定購買週期的商品種類
類別18、22、25、37、38、39也有上下震盪，但沒有明顯的規律性
類別10、13、15、26、36則有消費越來越多的趨勢
另外，類別10與類別18似乎有正相關性，類別25與類別39似乎有負相關性
前9月(-9)時，類別6、37、38、48都有金額突然暴增的現象
:::

### 基本屬性v.s.消費金額

#### **各類別於不同性別之消費總金額(2類)**
![](https://i.imgur.com/M98Seaz.png)
:::info
於類別2,13,18,19,22,25性別類別0的消費總金額較類別1明顯較多
:::
####  **各類別於不同性別之平均單筆消費金額(2類)**
![](https://i.imgur.com/MAds2EM.png)
:::info
不同類別平均單筆消費金額大致平均，於類別10,18,36性別類別0的平均消費金額較類別1較多
:::
#### **各類別於不同婚姻狀態之消費總金額(2類)**
![](https://i.imgur.com/Ibaf04L.png)
:::info
於類別10,13,36,48婚姻狀態類別2的消費總金額較類別1明顯較多
於類別21,26,37婚姻狀態類別1的消費總金額較類別2明顯較多
:::
####  **各類別於不同婚姻狀態之平均單筆消費金額(3類)**
![](https://i.imgur.com/p3bUaf6.png)
:::info
不同類別平均單筆消費金額基本無差別，類別10,15,36婚姻狀態類別1的消費總金額較類別2較多，婚姻狀態類別3資料極少
:::

### 刷卡次數v.s.消費金額
![](https://i.imgur.com/48Oj6Yu.png)
:::info
資料型態皆為右偏
每種類別的消費金額多集中在0~20000區間
類別37的消費次數最多
類別13的消費次數最少
:::


### 性別v.s.刷卡次數
![](https://i.imgur.com/TEu5eju.png)
![](https://i.imgur.com/bO0kzjf.png)
![](https://i.imgur.com/kel43rV.png)
![](https://i.imgur.com/BOguiCs.png)


:::info
男性消費類別比例60%~70% : 2、13、18
男性消費類別比例70%~80% : 19、22、25
女性消費類別比例最高者 : 39
:::

### 熱力圖
![](https://i.imgur.com/VjNFYaB.png)

:::info
相關係數
信用卡(1)、(4)和總消費次數正相關
信用卡(1)和國內消費線上消費正相關
信用卡(4)和國內實體消費正相關
信用卡(13)和國外實體消費正相關
信用卡(5)和國外線上消費正相關
信用卡(4)和信用卡(2)消費占比負相關，推測互為優惠性質相異卡種
(ex.線上回饋v.s.實體回饋 or 國內回饋v.s.國外回饋)
:::


## Problem formulation and Methods used


### Problem definition

本次競賽目標為預測每個信用卡消費者在下個月消費金額最高的三個類別，最後預測出來的準確度，我們以NDCG指標計算。

### Measures for comparisons 
以$NDCG$(Normalized Discounted cumulative gain)進行評分：
$$NDCG=\frac{\sum_{c\in C}NDCG_c}{\vert C\vert}=\frac{\sum_{c\in C}\frac{DCG_c}{iDCG_c}}{\vert C\vert}$$
其中，$C$為所有需預測之顧客，$c$為其中的一位顧客，$NDCG_c$為顧客$c$之Normalized Discounted cumulative gain，$DCG_c$則為顧客$c$之Discounted cumulative gain，而$iDCG_c$為顧客$c$最理想之Discounted cumulative gain。
$DCG_c$與$iDCG_c$的定義如下：
$$DCG_c=\sum_{i=1}^3\frac{V_{i,c}}{\log_2 (1+i)}$$
$V_{i,c}$：顧客$c$於模型回傳之第$i$個消費類型的實際消費金額($>=0$)
$$iDCG_c=\sum_{i=1}^3\frac{\hat{V}_{i,c}}{\log_2 (1+i)}$$
$\hat{V}_{i,c}$：顧客$c$消費金額第$i$高之消費類型的實際消費金額($>=0$)

若對於顧客$c$，模型預測之前三消費類型之排序如同實際消費金額之排序時，則為最理想的狀況，此時$NDCG_c=1$;若模型預測之前三消費類型顧客$c$皆未消費，則為最差的狀況，此時$NDCG_c=0$。

### Benchmark Method
消費類別：Multinomial Logistic Regression
消費金額：Multiple Linear Regression

### Other Machine Learning Methods Used
XGBoost

## Analysis

設定以主要更變機器學習模型上面(f(x))來預測結果，因此對原始資料的更動較少。

### Data Preprocessing
#### 離群值
將單次消費金額超出1,000,000(非真實數值)的資料刪除

#### 缺失值
將缺失部分填入中位數或是眾數



### 1. Benchmark
用Linear Regression預測出消費金額，接著用Multinomial Logistic Regression預測出消費類別，再找出預測機率前三高的消費類別，我們使用兩種方法進行。
#### (1)最高法
先找出每個顧客預測出消費金額最大的一筆，再使用該筆資料使用Multinomial Logistic Regression預測出前三項機率最高之消費類別作為最終該消費者消費類別中預測消費金額前三高之排序。
#### (2)加權法
我們將每個顧客其中每筆資料使用Multinomial Logistic Regression預測出所有消費類別機率乘上該筆消費用Linear Regression預測出之金額並將各筆相同預測類別金額加總並以前三高之類別作為最終預測結果。

### 2. XGBoost
先用XGBRegressor預測每筆資料的消費金額，再用XGBClassifier跑Logistic Regression，參數方面更動XGBRegressor和XGBClaasifier的learning rate，從預設0.3調到0.2。接著預測出各消費類別的機率，找出前三高的機率類別，作為最終該消費者消費類別中預測消費金額前三高之排序。

### Evalution

||Benchmark |XGBoost|
|--|---|---|
|$NDCG$|(1)0.445376|0.486379|
|      |(2)0.532659|0.543672|


## Conclusion
1.使用XGBoost模型之後，預測結果的分數都勝過Benchmark的(1)最高法、(2)加權法，但似乎各機器模型更換後效果並沒有顯著的差別。
2.我們有使用過許多的機器學習模型進行預測，但礙於資料量過大的關係，遭遇許多技術性困難與跑模型時間過久問題，導致很多模型結果沒跑出來，因此該還是建議進行資料前處裡(加入時間考量)縮小資料規模並增加準確率。
