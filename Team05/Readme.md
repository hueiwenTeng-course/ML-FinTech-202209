# 玉山商業競賽－疑似洗錢交易預測
### Team members: 廖川立、鄭棣升、何維
## **1. Motivation**

對于金融業而言，洗錢防制是一個必然面對的難題與挑戰。犯罪者利用各種洗錢管道將非法資金漂白，以逃避司法機關的查緝與追訴，甚至將該犯罪所得再次利用于其他不法行爲當中。
金融機構若不積極審查各種由其經手的交易行爲，則將淪爲犯罪集團的漂白管道，除了損及自身商譽之外，又因爲金融機構具有集合廣大人民資金的特性，而擾亂了金融市場秩序。除此之外，金融犯罪者不斷以新興科技或渠道從事不法所得的掩飾或隱匿行爲，洗錢態樣推陳出新，致使金融業單靠人力顯然不足以辨識出可疑的犯罪活動。
因此，我們希望可以利用機器學習模型，更精准地篩選可疑洗錢活動，增强防止洗錢效率。

![](https://i.imgur.com/OUGMQrZ.jpg)






## **2. Data**
### ***（1）Data***
#### ***子表重複行項目（子表間對照的索引指標***）
![](https://i.imgur.com/kvQgohx.png)

#### ***子表內容（括號內表示行列數）：***
***CCBA***
![](https://i.imgur.com/0qj8ZdU.png)

***CDTX***
![](https://i.imgur.com/hUzcTAZ.png)

***CUSTINFO***
![](https://i.imgur.com/NoBZrBx.png)

***DP***
![](https://i.imgur.com/H22KvJ2.png)

***REMIT***
![](https://i.imgur.com/eGImXTg.png)

***ALERT DATE 及 Y***
![](https://i.imgur.com/9ixd0U7.png)
sar_flag=1 共233個，佔比0.97%。
### ***（2）EDA*** 
#### （1）洗錢防製Domain knowledge
![](https://i.imgur.com/7v6VeW8.png)

#### （2）sar值在總樣本的分配情況

![](https://i.imgur.com/RhW6VpB.png)

數據統計如下：
![](https://i.imgur.com/hvS2DN3.png)
#### 結論：在所有交易明細中，以單一交易作為基準，sar=1的數量極少。

#### （3）針對 sar=1 交易明細分析

#### 子表1：cdtx（消費國別）
![](https://i.imgur.com/10wtokh.png)


#### 結論：當sar=1時，在cdtx(消費國別)子表中發現：
根據country（消費地國別），標記洗錢對應的地區主要為 *非台灣地區*，佔比73%。
根據cur_type（消費地幣別），biaoji洗錢對應的幣別主要為 *非台幣交易*，佔比72%。

#### 子表2：custinfo（客戶信息）
![](https://i.imgur.com/vwMq8z0.png)
![](https://i.imgur.com/NL2EvXl.png)
![](https://i.imgur.com/X0EJDvf.png)
#### 結論：當sar=1時，在custinfo（客戶信息）子表中發現：
根據risk_rank（風險等級），標記洗錢對應的 *風險等級主要為1*，佔比78%。
根據occupation_code（職業），標記洗錢對應的 *職業主要為17-19-12-18* ，以上四類共佔比78%。
根據AGE（年齡），標記洗錢對應的 *年齡主要為3-2-4-5*，以上四類共佔比98%。

#### 子表3：dp（交易明細）

![](https://i.imgur.com/yfTu4rL.png)

![](https://i.imgur.com/BtI4KYm.png)

![](https://i.imgur.com/GnyxU6U.png)

#### 結論：當sar=1時，在dp（交易明細）子表中發現：
根據info_asset_code（臨櫃現金交易），標記洗錢主要為 非臨櫃現金交易，佔比99%。
根據cross_bank（跨行交易），標記洗錢主要為 非跨行交易，佔比66%。
根據ATM（實體ATM交易），標記洗錢主要為 實體ATM交易，佔比88%。

### ***（3）Data Preprocessing***
1. 先將ALERT DATE表和Y表使用alert_key作爲索引值合并起來，再把此表以同樣方式合并到CUSTINFO表 。（這裏將此表稱爲info_sar，以便後續引用）
此舉目的為分辨出被舉報為可疑案件的交易是由哪一位客戶完成的。
![](https://i.imgur.com/PSJ15b2.png)


1. 將info_sar表合并到其餘四個擁有各種類交易記錄的子表，以此觀察某特定或可疑客戶的交易模式。
以觀察客戶在進行CCBA相關交易的交易模式爲例：
![](https://i.imgur.com/2vvQHTo.png)

3. 空值處理：
* info_sar：occupation_code有空值，但發現空值并不影響後續機器學習模型後續的判斷，因此直接drop掉
![](https://i.imgur.com/4hnswtv.png)
（可以看到當occupation_code是空值時，sar_flag全部等於0，因此不影響後續機器學習模型的判斷）
* CCBA，CDTX，REMIT：合并後無空值問題 
* DP：主要空值出現在tx_amt，fiscTxId，txbranch。
    處理空值方法：先將原dataset以
    * ATM = 1 or 0（是否爲實體ATM交易）
    * cross_bank = 1 or 0（是否爲跨行交易）
     
    進行切割，並以此去分析各交易記錄以上三點的分配是怎麽樣的，再決定要怎麽處理空值的問題。
    以下以ATM = 1，cross_bank = 0爲例，觀察fiscTxId的分配：
    ![](https://i.imgur.com/lHe073Y.png)
    ![](https://i.imgur.com/UjEF0jZ.png)
    （可以看到無論sar_flag = 1 or 0，其眾數皆爲fiscTxId = 4。因此在考慮到不影響fiscTxId在此資料集作爲衆數的資料顯著性，我們決定以fiscTxId = 4補上空值。）
    接下來我們以類似方式陸續補上tx_amt及txbranch的空值來解決空值問題。




## 3. Formulation
### *競賽要求*
提交檔案內容：包含所有須預測的案件名單（alert_key）之報SAR機率，由機率高排至低
example：
![](https://i.imgur.com/EtyLLXz.png)
目標：
通過已經給定的0-364天的交易信息（包含是否為洗錢）建立模型
預測364天以後的交易信息中為洗錢的機率


評分方式：
![](https://i.imgur.com/UgD9yk9.png)

## 4. Analysis
### *分析方法*：
1. 透過使用每個字表中的每筆交易記錄投入機器學習模型進行訓練，藉此瞭解可疑交易記錄的模式爲何。接著使用模型對public資料集（即新發生的alert_key）中的每筆交易進行預測。
2. 完成預測之後，統計每個子表中擁有alert_key，且預測結果為sar_flag = 1的交易記錄數量。如某筆交易記錄被舉報為sar_flag = 1的數量超過某個閾值，則那筆交易將被判別為sar_flag = 1（expert ensemble的概念）

![](https://i.imgur.com/DvlVylU.png)

### ***Benchmark***（**直接使用機器學習方法進行分析）

| ML Model | Score （Precision of Recall @N-1） |
|--------- | -------- |
| Decision Tree | 0.01567884 :thumbsup:|
| Logistic Regression | 0.00177923 |
| Gradient Boosting | 0.00878811 |
| Random Forest | 0.00987649 |

### ***修正***
 我們發現直接套用機器學習模型進行預測并沒有得到很好的效果。因此，我們采取了以下措施：

*  由於Data imbalance十分嚴重（sar_flag幾乎都是0），因此我們認爲可以先對每個字表進行PCA降維（n_components = 2) ，并對降維后的資料集使用SVM (Support Vector Machine，kernel = 'rbf'）進行分類。
(以下以DP資料集爲例)
![](https://i.imgur.com/pp4fcPG.png)
（紅色：sar_flag = 1 ; 紫色：sar_flag = 0)

* 使用SVM的目的為將降維後的資料集（features為principal components 1&2）進行分類（sar_flag = 1 or 0）。接著再對分類出來的兩組資料集，即爲使用SVM判斷為sar_flag = 1 和sar_flag = 0的兩組資料集，投入機器學習模型（Decision Tree, Logistic Regression, Gradient Boosting, Random Forest)進行預測。
* 此舉能夠減少False Negative（Type II Error)發生的比例。（即SVM預測某特定交易sar_flag = 0,但其實那筆交易sar_flag應爲1）

在使用以上修正方法後，機器學習模型預測分數獲得一定程度的增長：

| ML Model | Score（Precision of Recall @N-1)|
| -------- | -------- |
| Decision Tree | 0.03125000 :thumbsup:|
| Logistic Regression | 0.00377956 |
| Gradient Boosting | 0.01978567 |
| Random Forest | 0.02225689 |

其中以Decision Tree的分數為最高。

### ***競賽結果***
![](https://i.imgur.com/M6IR7AF.png)
**總排名為 ：55 / 684** :trophy:

## 5. Conclusion
* 深切感受到Data Preprocessing的重要性及其難處：我們在處理CCBA，CDTX，REMIT，DP與info_sar的時候，因爲對於python（尤其是pandas）程式語言的不熟悉而花費很多時間。——> 以後必定會努力練功QQ	:sweat_smile:
* 儘管python提供很多機器學習的套件供我們使用，但我認爲我們還是應該要學習在不利用套件的情況下開發機器學習演算法。這樣我們才能夠更瞭解此演算法背後的原理進而因應我們的需求對其客制化。:desktop_computer:
* 這次競賽讓我們獲得滿滿對於機器學習模型開發的經驗，也瞭解到我們對於機器學習這個領域的理解僅止於皮毛。因此以後我們一定會精進自己對於機器學習的認知，以在日後應用這些知識於財務金融領域中！:dollar:
## 6.Reference
- [玉山人工智慧競賽網站](https://tbrain.trendmicro.com.tw/Competitions/Details/24)
- [SAS反洗錢AI](https://www.sas.com/zh_tw/insights/articles/risk-fraud/ai-for-anti-money-laundry.html)
- [疫情加速銀行採用AI技術防止洗錢](https://www.informationsecurity.com.tw/article/article_detail.aspx?aid=9538)
- [AI防治洗錢實際應用](https://ai.iias.sinica.edu.tw/ai-on-aml-and-cft-in-practice/)
