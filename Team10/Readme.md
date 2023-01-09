# 玉山商業競賽－防制洗錢
### Team members : 戰媺、吳旻修、潘俞綺

## 1. Motivation
【你說可疑不可疑？－疑似洗錢交易預測】 

對金融業而言，洗錢防制是必然面對的難題與挑戰。金融機構若不積極審查各種由其經手的交易行為，除了損及自身商譽外，也擾亂了金融市場秩序。

我們希望能透過玉山銀行提供去識別化後的顧客帳戶交易等資料，再加以我們上課學習到的機器學習方法建立模型，來降低洗錢可疑活動的誤報率。

![](https://i.imgur.com/OAt4H2N.jpg)


## 2. Data 
### 資料初步討論

Insights:

- Monthly data : ccba
- Daily data : cdtx, dp, remit
- Non-date data : custinfo
- 有交易行為的檔案：cdtx, dp, remit

### Dataset description
==ccba== : 

1. The number of rows and columns : (59075, 10)
2. The number of customers : 4745

| Variables | Meaning            | Type        | Notes                              |
|:--------- |:------------------ |:----------- |:---------------------------------- |
| cust_id   | 顧客編號           |             |                                    |
| lupay     | 上月繳款總額       | Categorical | 經神秘轉換，數字序列有前後順序意義 |
| byymm     | 帳務年月           | Numerical   | 經神秘轉換                         |
| cycam     | 信用額度           | Numerical   | 經神秘轉換                         |
| usgam     | 已使用額度         | Numerical   | 經神秘轉換                         |
| clamt     | 本月分期預借現金額 | Numerical   | 經神秘轉換                         |
| csamt     | 本月預借現金金額   | Numerical   | 經神秘轉換                         |
| inamt     | 本月分期消費金額   | Numerical   | 經神秘轉換                         |
| cucsm     | 本月消費金額       | Numerical   | 經神秘轉換                         |
| cucash    | 本月借現金額       | Numerical   | 經神秘轉換                         |

==cdtx 信用卡消費記錄== : 

1. The number of rows and columns : (1043014, 5)
2. The number of customers : 3945

| Variables | Meaning       | Type        | Notes                              |
|:--------- |:------------- |:----------- |:---------------------------------- |
| cust_id   | 顧客編號      |             |                                    |
| date      | 消費日期      | Categorical | 經神秘轉換，數字序列有前後順序意義 |
| country   | 消費地國別    | Categorical | 經神秘轉換，（130=台灣）           |
| cur_type  | 消費地幣別    | Categorical | 經神秘轉換，（47＝台幣）           |
| amt       | 交易金額-台幣 | Numerical   | 經神秘轉換                         |

==custinfo== : 

1. The number of rows and columns : (25751, 6)
2. The number of customers : 7708
3. The number of alert key : 25751

| Variables       | Meaning    | Type        | Notes      |
|:--------------- |:---------- |:----------- |:---------- |
| cust_id         | 顧客編號   |             |            |
| alet_key        | alert主鍵  |             |            |
| risk_rank       | 風險等級   | Categorical |            |
| occupation_code | 職業       | Categorical |            |
| total_asset     | 行內總資產 | Numerical   | 經神秘轉換 |
| AGE             | 年齡       | Categorical |            |

==dp 存錢與轉帳紀錄== : 

1. The number of rows and columns : (1969818, 12)
2. The number of customers : 6196

| Variables       | Meaning           | Type        | Notes                                                             |
|:--------------- |:----------------- |:----------- |:----------------------------------------------------------------- |
| cust_id         | 顧客編號          |             |                                                                   |
| debit_credit    | 借貸別            | Categorical |                                                                   |
| tx_date         | 交易日期          | Categorical | 經神秘轉換，數字序列有前後順序意義                                |
| tx_time      | 交易時間          | Categorical | 經神秘轉換，數字序列有前後順序意義                                |
| tx_type         | 交易類別          | Categorical |                                                                   |
| tx_amt          | 交易金額          | Numerical   | 經神秘轉換                                                        |
| exchg_rate      | 匯率              | Numerical   |                                                                   |
| info_asset_code | 資訊資產代號      | Categorical | 經神秘轉換，tx_type=1且info_asset_code=12時，該交易為臨櫃現金交易 |
| fiscTxId     | 交易代碼          | Categorical | 經神秘轉換                                                        |
| txbranch     | 分行代碼          | Categorical | 若為跨行交易，則僅代表交易對手銀行代碼，無分行資訊                |
| cross_bank      | 是否為跨行交易    | Categorical | (0=非跨行;1=跨行)                                                 |
| ATM             | 是否為實體ATM交易 | Categorical | (0=非實體ATM交易;1=實體ATM交易)                                   |


==remit 外匯紀錄== : 

1. The number of rows and columns : (17167, 4)
2. The number of customers : 1144

| Variables        | Meaning              | Type        | Notes                              |
| ---------------- | -------------------- | ----------- | ---------------------------------- |
| cust_id          | 顧客編號             |             |                                    |
| trans_date       | 外匯交易日（帳務日） | Categorical | 經神秘轉換，數字序列有前後順序意義 |
| trans_no         | 交易編號             | Categorical | 經神秘轉換，代表不同的匯出方式     |
| trade_amount_usd | 交易金額             | Numerical   | 經神秘轉換                         |

==alert_time== : 
- public_x_alert_date :
1. The number of rows and columns : (1845, 2)
2. The number of alert key : 1845
- train_x_alert_date : 
1. The number of rows and columns : (23906, 2)
2. The number of alert key : 23906

| Variables | Meaning           | Type        | Notes                              |
| --------- | ----------------- | ----------- |:---------------------------------- |
| alert_key | alert主鍵         |             |                                    |
| date      | alert主鍵發生日期 | Categorical | 經神秘轉換，數字序列有前後順序意義 |

==y== : 

1. The number of rows and columns : (23906, 2)
2. The number of alert key : 23906

| Variables | Meaning            | Type        | Notes                 |
| --------- | ------------------ | ----------- | --------------------- |
| alert_key | alert主鍵          |             |                       |
| sar_flag  | alert主鍵報SAR與否 | Categorical | (0=未報SAR;1=有報SAR) |

| y   | 數量  | 比例 |
| --- | ----- |:---- |
| 0   | 23672 | 0.99 |
| 1   | 234   | 0.01 |


``` mermaid
graph LR;
分割資料-->SAR=1;
分割資料-->SAR=0;
SAR=1-->分開做EDA;
SAR=0-->分開做EDA;
分開做EDA-->相互比較確定有無洗錢的差異;
```

### SAR=0 v.s. SAR=1

- **Dataset : ==cdtx==**

1. $country$

| SAR=0                                      | SAR=1                                      |
| ------------------------------------------ |:------------------------------------------ |
| ![](https://i.imgur.com/3xvruR7.png) | ![](https://i.imgur.com/IzQ9sWn.png) |

:::info
1. 沒洗錢的主要國別為130（台灣）佔比高達82%
2. 沒洗錢的國別134佔比僅1.6%，而有洗錢的國別134飆升至54%
:::

2. $cur\_type$

| SAR=0 | SAR=1 |
| --- | --- |
| ![](https://i.imgur.com/EjRkE91.png) | ![](https://i.imgur.com/UI0RETq.png)|
| ![](https://i.imgur.com/DQ0ykRF.png) | ![](https://i.imgur.com/psu3po0.png) |

:::info
1. 沒洗錢的主要幣別為47（台幣），高達91.9%，但在有洗錢的時候，下降至第二高並且只有佔27%
2. 有洗錢由幣別46為大宗，佔比來到37.8%，相較於他在沒洗錢時的比例2.9%相差懸殊
:::

- **Dataset : ==custinfo==**
1. $risk\_rank$
 
| SAR=0                                 | SAR=1                                 |
| ------------------------------------- |:------------------------------------- |
| ![](https://i.imgur.com/YNiy5l3.png) | ![](https://i.imgur.com/LGZWcTf.png) |
| ![](https://i.imgur.com/K4sSS27.png)  | ![](https://i.imgur.com/JLqZM7R.png)  |

:::info
1. 風險等級的順序的排序並未有差異，但可以看到風險等級1的客戶，在有洗錢的部分提高至88.4%，比例變相當的高
2. 沒洗錢的時候有風險等級0的客戶，但在有洗錢時，風險等級0的客戶完全沒有
:::

- **Dataset : ==dp==** 
1. $ATM$

| SAR=0 | SAR=1 |
| --- | --- |
| ![](https://i.imgur.com/ZPEocLC.png) | ![](https://i.imgur.com/Lt3hdY9.png) |
| ![](https://i.imgur.com/1DEbuhj.png) | ![](https://i.imgur.com/X44c4iC.png) |

:::info
1. 沒洗錢的資料使用ATM的佔比為75.6%，而在有洗錢的資料中使用ATM的佔比提高到87%
2. 有洗錢的很大比例都是使用ATM，不是使用ATM的僅有12.9%
:::

2. $info\_asset\_code$

| SAR=0 | SAR=1 |
| --- | --- |
| ![](https://i.imgur.com/BLO7dH4.png) | ![](https://i.imgur.com/MbNkXL6.png) |
| ![](https://i.imgur.com/Y2ETyGV.png) | ![](https://i.imgur.com/BlbD316.png) |

:::info
1. 在資訊資產代碼中，代碼13的在沒洗錢時佔比約47%，而有洗錢的時候提高到62.5%，提高蠻多
:::

- **Dataset : ==remit==**

1. $trans\_no$

| SAR=0 | SAR=1 |
| --- | --- |
| ![](https://i.imgur.com/Ro63MIs.png) | ![](https://i.imgur.com/VaBrZJx.png) |
| ![](https://i.imgur.com/MHEph8K.png) | ![](https://i.imgur.com/18hSes4.png) |

:::info
1. 可以發現在沒洗錢的部分，交易編號2雖然只有佔比0.7%，但在沒洗錢的部分，則是完全沒有
2. 交易編號0的在沒洗錢是佔比66%，到了有洗錢的部分飆升至93.5%，可見洗錢的交易編碼大多是0
:::

## 3. Formulation

$y = \left\{
\begin{array}{r}
   0, 未報SAR \\
   1, 有報SAR
\end{array}
\right.$

$X=\{ x_{1,i,t}, x_{2,i,t}, ..., x_{14,i,t} \}$

$f(X) = (p_1,\ p_2,\ldots,\ p_m),\ where \ p_i\in[0,1],\ i=1,\ldots,m.$

$where\ f:$ 機器學習方法, $p_i:$ 該alert key的SAR=1之機率（有洗錢）

- 上傳檔案為將alert key照SAR=1之機率由高到低排序。


- 比賽評分方式，以Recall@N-1的Precision進行評分，意即在抓到N-1個真正報SAR案件名單下的Precision，公式如下：

$$Recall@N-1=\frac{N-1}{抓到N-1個真正報SAR案件所需名單量}$$

$$where\ N=該月所有真正報SAR的案件數$$


## 4. Analysis
### Benchmark
#### 合併後資料格式

:::warning


註：
1. 金額相關變數：如果一日有多筆，則將一日的的交易金額（amt）取平均，並且新增一欄單日交易累積次數。
2. 類別變數：如消費地國別（country）,消費地幣別（cur_type）......等等，若在單一天如果有不同的值，則取眾數。
3. 缺值處理：

| 交易金額或次數等於0          | 補眾數          | 新增數值代表「沒有」     |
|:---------------------- |:--------------- |:------------------------ |
| amt                    | country         | daily_debit_credit(None) |
| amt_count              | cur_type        | cross_bank(2)            |
| daily_tx_amt_ntd       | tx_type         | atm(2)                   |
| daily_tx_amt_ntd_count | info_asset_code | trans_no(5)              |
| trade_amt_usd          |                 |                          |
| trade_amt_usd_count    |                 |                          |
:::

| Notation                     | Varialbes                                        | Description            |
|:---------------------------- | ------------------------------------------------ | ------------------ |
| $i$                             | cust_id                                          | 顧客編號           |
| $t$                          | date                                             | 該筆資料行為的日期 |
| $x_{1,i,t}$ |country | 消費地國別（有使用Frequency encoding）|
| $x_{2,i,t}$ |cur_type | 消費地幣別（有使用Frequency encoding）|
| $x_{3,i,t}$ |amt | 消費交易金額（將同一天不同筆的交易金額累積）|
| $x_{4,i,t}$ |amt_count | 單日消費交易次數|
| $x_{5,i,t}$ |daily_tx_amt_ntd | 借貸交易金額（將同一天不同筆的交易金額累積）|
| $x_{6,i,t}$ |daily_tx_amt_ntd_count | 單日借貸交易次數|
| $x_{7,i,t}$ |daily_debit_credit | 借貸別（有使用Frequency encoding）|
| $x_{8,i,t}$ |daily_tx_type | 借貸交易類型（有使用Frequency encoding）|
| $x_{9,i,t}$ |daily_info_asset_code | 借貸交易資訊資產代號（有使用Frequency encoding）|
| $x_{10,i,t}$ |daily_cross_bank | 是否為跨行交易（有使用Frequency encoding）|
| $x_{11,i,t}$ |daily_ATM | 是否為實體ATM交易（有使用Frequency encoding）|
| $x_{12,i,t}$ |daily_trade_amount_usd | 外匯交易金額（將同一天不同筆的交易金額累積）|
| $x_{13,i,t}$ |daily_trade_amount_usd_count | 單日外匯交易次數|
| $x_{14,i,t}$ |daily_trans_no | 外匯交易編號（有使用Frequency encoding）|

#### ML Method
:::success
將上述的資料型態依照cust_id, date與alert_key, SAR合併，有缺值則直接捨棄（由於產生alert_key當天可能無任何交易行為），再依照73比例生成訓練集跟測試集，測試上述的補值方法是否可以產生好的結果。
:::
| Method | Confusion matrix | Notes |
| ------ | ---------------- | ----- |
|Logistic Regression|$\begin{bmatrix}5427 & 2 \\69 & 0\end{bmatrix}$|共69筆資料實際是1，都預測是0 |
LDA |$\begin{bmatrix}5427 & 2 \\69 & 0\end{bmatrix}$|共69筆資料實際是1，都預測是0|
QDA|$\begin{bmatrix}4736 & 693 \\56 & 13\end{bmatrix}$|正確預測到13個1，卻錯誤預測693個0為1|
Naive Bayes|$\begin{bmatrix}5393 & 36 \\69 & 0\end{bmatrix}$|共69筆資料實際是1，都預測是0|
Decesion Tree|$\begin{bmatrix}5345 & 84 \\69 & 0\end{bmatrix}$|共69筆資料實際是1，都預測是0|

confusion matrix : column預測, row實際
:::danger
由於我們在一開始合併資料時，自行補了太多缺值，導致資料失真，因此用機器學習來訓練時，結果並不好，實際資料為1的都預測不到，唯一一個QDA有成功預測到13個1，但是卻誤將693個0認為1，因此我們決定在合併資料時改進做法，盡量避免填補缺值的動作。
:::

## 改進
### Preprocessing
#### 如何合併資料
:::warning
針對每個變數各新增一欄類別變數去判斷該變數是否有缺值，
1. 該變數有缺值：
- 變數缺值的部分補0
- 類別變數的部分填1，代表有缺值
2. 該變數無缺值：
- 變數缺值的部分維持原本的數值
- 類別變數的部分填0，代表無缺值
:::
==one hot encoding==:
Convert 'occupation','risk_rank', 'AGE' to a binary matrix
==pd.crosstab==:
計算'cur_type','tx_type','trans_no'各類別的次數
==MinMaxScaler==
#### 合併後資料格式


| Input variable   |                             | type        |
| ---------------- | --------------------------- | ----------- |
| risk_rank（4）   | 風險等級                      | categorical |
| total_asset      | 行內總資產                   | numerical   |
| AGE  (11)        | 年齡                        | categorical |
| amt              | 交易金額-台幣（信用卡）     | numerical   |
| wo_amt           | 有無信用卡交易              | categorical |
| cur_type(57)     | 消費地幣別 （信用卡）       | numerical |
| trade_amount_usd | 交易金額_折合美金(轉帳交易) | numerical   |
| wo_trade_amt     | 有無轉帳交易                | categorical |
| trans_no(5)      | 交易編號                    | numerical |
| wo_remit         | 有無外匯交易                | categorical |
| cash_deal        | 是否為臨櫃現金交易          | categorical |
| ATM              | 是否為實體ATM交易           | categorical |
| cross_bank       | 是否為跨行交易              | categorical |
| NTD_amt          | 交易金額（台幣）            | numerical   |
| wo_deal          | 有無提款交易                | categorical |
| tx_type          | 交易類別                     |  numerical    |
| cycam            | 信用額度                    | numerical   |
| usgam            | 已使用額度                  | numerical   |
| clamt            | 本月分期預借現金金額        | numerical   |
| csamt            | 本月預借現金金額            | numerical   |
| inamt            | 本月分期消費金額            | numerical   |
| cucsm            | 本月消費金額                | numerical   |
| cucah            | 本月借現金額                  |   numerical     |
| wo_ccba          | 有無信用卡帳單資料               | categorical        |


#### The score of the E san competition



| Method | Score |
| --- | --- |
| Naive Bayes | 0.006779 |
| Gradient Boosting Classifier| 0.007874 |
| Histogram Gradient Boosting Classifier | 0.009049 |
| Histogram Gradient Boosting Classifier+ Smote | 0.008025 |
| Random Forest | **0.018018** |

:arrow_right: 其中以Random Forest的表現最好

## 5. Conclusion

1. 改進完合併資料的方式後，我們又試了各種機器學習的方法，其中以Random Forest的表現最好（排名88/總隊數684）。
2. 這次的比賽遇到最大的困難就是，玉山給了很多個不同的檔案，所以要將所有檔案中的資料整合就會需要很好的方法，不然在合併的過程中，就會損失掉原本資料帶給我們的資訊，進而讓後續的分析也失準。



## Reference
[2022 玉山商業競賽-你說可疑不可疑？－疑似洗錢交易預測](https://tbrain.trendmicro.com.tw/Competitions/Details/24)
## Supplimentary materials
