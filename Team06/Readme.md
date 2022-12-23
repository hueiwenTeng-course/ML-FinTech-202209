


# 實體K棒預測與當沖交易
## 1. Motivation
### 當沖交易優勢：

當沖交易相對波段交易能夠做到的有以下三點:1.避開隔夜的跳空風險 2.交易成本低 3.交易週期短。
然而，當沖交易也有其限制，因為持倉時間有限，如何有效率的選擇進場時機將左右我們的當沖交易績效，因此在有限的時間內建立交易的優勢很重要。
除了找出當日會有波動的標的之外，若是能**預測波動方向較一致的盤勢**，找出比較不會上下沖洗的盤勢，或許能夠讓我們在日內的波段行情中找到好的交易機會。

![](https://i.imgur.com/xbj8Qjl.jpg)

### 實體K介紹:

K線由開盤價、收盤價、最高價、最低價四種價格組成
實體Ｋ即是**Ｋ線的實心部分**，不包含上影線與下影線
實體K佔比＝開盤價和收盤價距離/最高價與最低價的距離

![](https://i.imgur.com/LXGMW9R.png)
我們認為實體Ｋ佔比較大的盤勢，波動的方向會較一致，且不會有較大的日內反轉，適合當沖的突破策略; 相對的，上下影線部分較大的盤勢可能容易被盤勢玩弄。


### 實體K當沖濾網測試 ：
```
Ｍulticharts 回測簡單的當沖策略
* 標的：台積電期貨 5分K
* 策略內容：價格突破＋實體Ｋ濾網 
* 實體Ｋ濾網: 實體Ｋ佔比>0.5 且 實體Ｋ大小超過2塊 (只做大實體K的盤)(修改)

進場方式:
* 向上突破五根K高點做多
* 向下突破五根K低點做空

出場方式:
* 持有到收盤，1%停損
```


實體K濾網績效:上帝視角（預先知道今天實體Ｋ比例，if>0.5才使用突破策略買進）
![](https://i.imgur.com/OuwmdqQ.png)
買進持有績效
![](https://i.imgur.com/sHJKxXd.png)
無濾網績效：只用使用突破策略
![](https://i.imgur.com/yFtRzqN.png)

<!--2317
![](https://i.imgur.com/hcTg6cG.png)
-->

2. Data 

 
 ### 標的:
台積電 2330 <!--(鴻海 2317, 聯發科 2454)-->
### 資料來源:
Tickdata: 價量資料
TEJ：籌碼資料
### 樣本期間:  
2017/01~2022/06
<!-- 2016-06-02~2022-06-20  -->
| training data (7) | testing data (3) |
| -------- | -------- |
| 2017/01~2020/08 | 2020/09~2022/06     |


### Response variable

At time $t$, we calculate $y$ using the prices at day $t+1$.
$$y=I_{\{physical > 0.4 ,\ high-low>1\}},$$ 

where $$physical = abs(close-open)/(high-low).$$


### Explainatory variables (at day $t$)

$S_{t,i}$ denote the stock price at the $i$-th observation at day $t$.

| **價量指標**           | 描述                                   |
| ------------------------ | --------------------------------------------- |
| EMA                      | 移動平均線(價格變數)                                       |
| CCI                      | 順勢指標                                              |
| SAR                      | 反轉指標                                 |#Stop And Reverse
| ADX                      | 動向指標                  |#Directional Movement Index
| OBV                      | 能量潮指標                                       |#On Balance Volume
| ATR                      | 價格波動指標                                             |
| AROONOSC                 | Aroon指標                                              |
| RSI                      | 相對強弱指標                                      |
| **三大法人**             |                                               |
| foreign                  | 外資買賣超(張)                                |
| trust                    | 投信買賣超(張)                                |
| dealer                   | 自營買賣超(張)                                |
| day_foreign              | 外資買賣超日數                                |
| day_trust                | 投信買賣超日數                                |
| day_dealer               | 自營買賣超日數                                |
| ratio_foreign            | 外資成交比重                                  |
| ratio_trust              | 投信成交比重                                  |
| ratio_dealer             | 自營成交比重                                  |
| **融資融券**             |                                               |
| ratio_credit             | 信用交易比重                                  |
| increase_margin          | 融資增加(張)                                  |
| decrease_margin          | 融資減少(張)                                  |
| total_margin             | 融資餘額(張)                                  |
| balance_margin           | 融資餘額(千元)                                |
| utilization_margin       | 融資使用率                                    |
| increase_short           | 融券增加(張)                                  |
| decrease_short           | 融券減少(張)                                  |
| total_short              | 融券餘額(張)                                  |
| balance_short            | 融券餘額(千元)                                |
| utilization_short        | 融券使用率                                    |
| ratiotoday_margin        | 融資增減比率                                  |
| ratiotoday_short         | 融券增減比率                                  |
| ratio_st_mg              | 券資比                                        |
| offset                   | 資券互抵(張)                                  |
| ratio_offset             | 資券互抵比例                                  |
| ratio_turnover           | 實際週轉率％                                  |
| **董監持股**             |                                               |
| ratio_friegnShares_noD&S | 外資總持股率_不含董監%                        |
| ratio_trustShares        | 投信持股率％                                  |
| ratio_D&S_Shares         | 自營持股率％                                  |
| ratio_dealerShares       | 董監持股％                                    |
| ratio_D&S_Pledge         | 董監質押％                                    |

### EDA:

### 樣本數
Train   ：label=1 :  501 label=0 :  513
Test    ：label=1 :  259 label=0 :  176
All          ：label=1 :  760 label=0 :  689

#### 1. Distribution Plot
<!--實體K分布-->
![](https://i.imgur.com/Smo5LYL.png)


#### 2. Pair Plot
<!--Y與X關係-->
##### 價量指標
![](https://i.imgur.com/uesQAhr.jpg)
前一日是較有趨勢的盤，隔日傾向較沒有趨勢
##### 三大法人
![](https://i.imgur.com/nUgGCm9.jpg)

##### 融資券
![](https://i.imgur.com/7Oo3YyV.jpg)
##### 董監持股
![](https://i.imgur.com/RI4wUIQ.png)



#### 3. Heat map
![](https://i.imgur.com/RhGeFRC.png)


## 3. Formulation
y: Trend 實體K
X: 價量變數與籌碼變數
目標:用X預測Y

Benchmark: Logistic Regression
Method: DecisionTree, XGBoost

## 4. Analysis

### 4.1 Confunsion matrix



#### 4.1.A: Confusion matrix with static models
| training data (7) | testing data (3) |
| -------- | -------- |
| 2017/01~2020/08 | 2020/09~2022/06     |

Original method (static parameters:使用訓練集中的一組參數去預測第二天的$y$，且在測試期間具有相同的參數)


| 績效 |Logistic|DecisionTree|XGBoost|
| ------ |:------ | --- | --- | 
| Accuracy  | 0.5 | 0.54| <font color="#f00">**0.59**</font> |  |  
| F1 Score  | 0.59| 0.67 | <font color="#f00">**0.71**</font> |  |     
| Recall    | 0.58| 0.59 | <font color="#f00">**0.61**</font> |  |     
Precision | 0.6| 0.79 | <font color="#f00">**0.85**</font> |  |  

Note:模型中參數已經過GridSearchCV最佳化
#### 4.1.B: Confusion matrix with dynamic-adjusted models
![](https://i.imgur.com/SFkhF83.png)
#### 我們想要使用動態才能在靜態的模型中再更進一步，理由是靜態切割的方式可能沒有辦法考慮到市場的趨勢(ex.2020年全球遇上新冠肺炎，疫情衝擊經濟)，所以我們使用動態切割train和test的集合。
|data period| window |shift|
|---|----|---|
|2017/01~2022/06|250 days|30 days|

testing
Sliding window train test split prediciton (Use 250-day historical to obtain a model and parameters to predict $y$ one-days ahead,finally we re-estimate the model and predict with the new parameters)



| 績效 |Logistic|DecisionTree|XGBoost|
| ------ |:------ | --- | --- | 
| Accuracy  | 0.5 | 0.57 | <font color="#f00">**0.6**</font> |  |   
| F1 Score  | 0.59| 0.68 | <font color="#f00">**0.73**</font> | |    
| Recall    | 0.58| 0.61 | <font color="#f00">**0.61**</font>  |  |    
| Precision | 0.6 | 0.78 | <font color="#f00">**0.9**</font> | |   

### 4.2 人類視角實體Ｋ濾網績效 (Dynamic-adjusted models)

#####   Logistic Regression 樣本外績效2020/09~2022/06
多單權益曲線:
![](https://i.imgur.com/0C7TpIm.png)
空單權益曲線:
![](https://i.imgur.com/lGXmfp7.png)
<!--只做多單的績效:
![](https://i.imgur.com/kkfGN8I.png)-->

#####   DecisionTree 樣本外績效2020/09~2022/06
多單權益曲線:
![](https://i.imgur.com/oNtiM7T.png)
空單權益曲線:
![](https://i.imgur.com/1rzqECo.png)
<!--只做多單的績效:
![](https://i.imgur.com/pqgzwwf.png)-->

#####   XGBOOST 樣本外績效2020/09~2022/06
多單權益曲線:
![](https://i.imgur.com/j5s2OaA.png)
空單權益曲線:
![](https://i.imgur.com/YovKnEj.png)
<!--只做多單的績效:
![](https://i.imgur.com/pYCPymZ.png)-->


結論: 由上圖可看出，多單的價格突破搭配實體K濾網效果不錯，空單則效果不好。股市長期多頭，因此空單突破可能需要更多濾網或更複雜策略才能穩定獲利

### 策略優化
```
價格突破＋實體Ｋ濾網 當沖策略(不做空)
* 標的：台積電期貨 5分K
* 初始資金:100000

進場方式:
* 日K濾網(昨日收盤價>近三日收盤價)
* 向上突破五根K高點做多
* 一天只進場一次

出場方式:
* 持有到收盤，0.5%停損
```
| 交易績效 |Logistic|DecisionTree|XGBoost| No Physical Filter(突破+日K)|
| ------ |:------ | --- | --- | --- | 
| 淨利  | 10200 | 75100 | <font color="#f00">**159000**</font> | 62000 |   
| 最大策略虧損 (%)  | 60.99%| 34.11% | <font color="#f00">**31.35%**</font> |41.75% | 
| 交易次數  | 118 | 159 | 170  | 200 |    
| 勝率 | 38.98% | 38.36% | <font color="#f00">**42.35%**</font> | 38.5%|  
| 年報酬率 | 5.67% | 41.75% | <font color="#f00">**88.39%**</font> | 34.47% | 
* 樣本外買進持有淨利:14484
## 5. Conclusion

1. XGBOOST 比較好
1. Dynamic-adjusted models 比較好
1. 實體K濾網在多單的簡單突破策略(突破近N根高點做多)較有效果
1. 股市長期多頭，因此空單突破可能需要更多濾網或更複雜策略才能穩定獲利




## Reference

## Supplimentary materials




### Appendix 

#### Confusion matrix with static models: 我們使用不同模型形成的Confusion matrix鑑別靜態模型的好壞
![](https://i.imgur.com/tm9dixZ.png)
###### Accuracy:模型預測正確數量所佔整體的比例
###### Precision:陽性的樣本中有幾個是預測正確
###### Recall:事實為真的樣本中有幾個是預測正確
![](https://i.imgur.com/THoBuCB.png)
###### F1-score:同時考慮Precision & Recall，平衡地反映這個演算法的精確度

![](https://i.imgur.com/Ye9Iebc.png)
![](https://i.imgur.com/IwCeDxI.png)
![](https://i.imgur.com/4lx2hIO.png)
<!--![](https://i.imgur.com/QzBZVeV.png) -->

### Confusion matrix with dynamic-adjusted models:鑑別動態模型的好壞
![](https://i.imgur.com/pREuNt9.png)
![](https://i.imgur.com/S6EcYPz.png)
