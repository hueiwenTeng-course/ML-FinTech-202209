# line 訊息整理師：提取聊天室的重點訊息
### Team members:姚奕慈
## Motivation

 ![image](https://github.com/Bellayao06/text-assistant/blob/main/demo1.png)
 
 2020推出的[LINE統計分析工具](https://chonyy.github.io/line-message-analyzer/)，輸入聊天記錄便可分析和朋友每日聊天訊息、常用詞，呈現統計圖表、文字雲。本次專題創新該工具，利用機器學習提取聊天記錄的重點，為LINE用戶標示群組對話重點，而後繪製文字雲、整合為摘要。

## Data

 本次專題分析的LINE訊息為2022/8/29服飾二手拍群組的討論內容，該群組為我和另外兩位朋友一起創建，當天的訊息討論內容主要為二手攤位的佈置和活動日程。資料的部分取用LINE聊天設定中的「傳送聊天紀錄」匯出txt檔，刪去時間、用戶名的欄位，只留下訊息部分，利用[jieba结巴中文分词](https://github.com/fxsjy/jieba)將句子分詞。分詞方式有兩種——訊息連接為文章後分詞、一則則訊息分詞。

**EDA-詞頻表**
![image](https://github.com/Bellayao06/text-assistant/blob/main/%E6%88%AA%E5%9C%96%202023-01-10%20%E4%B8%8B%E5%8D%881.45.01.png)

## Formulation

 在完成分詞後，接著進行文字前處理，包括建立自設詞彙庫、刪去停用詞，完成調整分詞、去除贅字。其中應用的停用詞表有三種，引用[中文常用停用詞表 (cn_stopwords)](https://github.com/goto456/stopwords/blob/master/cn_stopwords.txt)，將簡體字轉換為繁體字，並將中國用語刪去；參考[英文停用詞表](https://www.ranks.nl/stopwords)加上自身對於LINE訊息常見字的解讀，創作Stopwords(1)；分析多個LINE聊天室的聊天記錄，統整常見字，集結成Stopwords(2)。經過文字前處理，便可以著重在真正重要的分詞，繪製文字雲、建立詞向量。
 
 接著應用文字前處理後的分詞，計算TFIDF，挑選前60高的關鍵分詞，呈現文字雲，而詞的大小代表著詞頻高低。另一方面，利用[Word2Vec](https://radimrehurek.com/gensim/models/word2vec.html)模型訓練詞向量，而後藉skip-gram串連文字雲中的大字樣分詞，將關鍵詞組織為簡短有力的摘要。
 
## Analysis

 **Wordcloud**
 ![image](https://github.com/Bellayao06/text-assistant/blob/main/%E6%88%AA%E5%9C%96%202023-01-10%20%E4%B8%8B%E5%8D%882.31.46.png)
 
  **Wordcloud**
 ![image](https://github.com/Bellayao06/text-assistant/blob/main/%E6%88%AA%E5%9C%96%202023-01-10%20%E4%B8%8B%E5%8D%882.31.28.png)

## Conclusion
[Keynote](https://www.icloud.com/keynote/0caWZoE8Bbg7Ig90vMGeDaduw#20221013_Ten_Yao_line_assistant)
