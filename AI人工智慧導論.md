閱讀筆記

Source: 人工智慧導論 2019 鴻海教育基金會 
Date: 2025-02
rjsiao

認識AI
-
1. 如何做人工智慧
- 培養觀察力。
- 訓練問問題。
- 聊天機器人用遞歸神經網路納入前一次狀態。
2. 函數f是解答本
- 定義域X：所有可能問題的集合。
- 值域Y：所有可能答案的集合。
3. 如何使用AI解決問題
- (1) 提問。
- (2) 將問題轉換成函數。
- (3) 收集歷史資料，注意過度擬合。70%訓練資料、30%測試資料。
- (4) 以機器學習、神經網路建構函數學習機。
- (5) 學習或訓練機器學習機，建模，注意損失函數。
4. 近期AI大爆發主因及挑戰
- 複雜的軟體、計算能力提升、大數據累積。
- 電腦無法提出好問題，且無法自動建造函數學習機。
5. 常見與機器學習有關的演算法
- 分類：對應監督式學習，將未知新訊息歸納到已知資訊類別，根據多個特徵將分類結果加上標籤，如生物學分類。
- 分群：對應非監督式學習，資料集沒有明確分類，需以特徵區分成不同群集。
- 一般表示方法：2~3個維度用線性分類器，超平面n維度用數學方程式。
6. 監督式學習
- 支持向量機 (SVM)：根據核函數用決定以線性或非線性分類器分群，找出最大支持向量(兩分類間距離分類線最近的點取對大距離)
- 決策樹 (decision tree)：建立多元樹，包括根節點、節點、葉，透過迭代試驗找出一分類原則有較高正確率的資訊獲利。
- K近鄰 (KNN)：k值表示比對k個已分類最接近的資料點，通常為奇數。
7. 非監督式學習
- K平均 (k-means)：隨機分成k個群集，持續迭代計算出每個分群的重心與重新分群。

神經網絡架構
1. 標準NN = 全連結神經網絡
- 使用時首先決定輸入、輸出的維度。
- 人工神經網絡：輸入層、隱藏層、輸出層。連接神經網絡、前饋神經網絡。
- 決定隱藏層數量，以及每個隱藏層的神經元數量。隱藏層數量大於3層稱為深度學習。
- 損失函數 L 的變數有二：(1)權重數值 wi 越大，輸入的越重要。(2)偏值 bi 用來調整總刺激。
- 激活函數判斷刺激夠大為1，可忽略者0。
- 學習速率 r 
- 梯度下降法/反向傳播法：梯度 gradient L 與損失函數 L 方向相反。
2. 卷積CNN：常用於圖形辨識
- 卷積層：機器以多個過濾器查看照片，分次檢查不同特徵，將特徵強度分數記錄在計分板。
- 池化層：從固定選區大小(如2*2)挑出指定值(最大、最小、平均)。
- input-卷積層-池化層-卷積層-池化層-...-全連結層-output
3. 遞歸RNN：保有前次輸入記憶
- 隱藏狀態是輸出會當作下次的輸入，也會同時傳給同一層鄰居。
- 適用於與時間有關的判斷，和需要參考前後文的判斷。如翻譯機、接續創作文章或圖片。

圖像辨識
-圖像是由色塊/像素組成的矩陣，解析度是像素的行數列數。
-空間濾波器：對影像的預處理程序，包括調整大小、旋轉，透過卷機運算(移動->對齊->計算乘積和->補零)提高精確度。
1. 濾波器/遮罩/核心，如3*3方形。
- 平均濾波器/平滑濾波器、權重平均濾波器：減少雜訊，模糊化。
- 中值濾波器：濾除雜訊。
- 索伯濾波器：透過離散性差分計算圖像亮度之梯度，凸顯邊緣。
2. 深度神經網絡DNN：輸入層 + n層隱藏層 + 輸出層
3. 卷積CNN：簡化DNN完成訓練，以AlexNet為例。
- 卷積層：利用卷積核概念，接收特徵圖，提取特徵。
- 池化層：找出局部特殊值，降低計算資源。
- 全連接層：將兩不同位置之特徵向量進行轉換，交付分類器進行分類。
- 激活層：解決線性系統的疊加問題，引入非線性函數運算到每次的線性運算中。包括 sigmoid 函數、tanh 雙曲正切函數、ReLU 線性整流函數(最常使用)。
- 標準化指數層：將計算結果訂於1~0之間，前面會連接全連接層，後面結果即為分類器。
4. 訓練模型：
- (1)資料集：訓練集、驗證集、測試集。
- (2)解決過度擬合：增加資料量(=資料增強)，訓練時隨機停止某些神經元(=加入丟棄機制，降低深度學習複雜度)。
5. 圖像辨識應用
- 自動化整理相簿
- 建立視覺資料之圖片網、影片網
- 發展家庭保全系統，影格率/fps。
- 使用用戶生成內容/用戶側寫，完成互動式行銷，提高廣告轉化率。
6. 免費數據庫
- ImageNet
7. 圖像辨識研究：陽明交大人工智慧與多媒體實驗室，史丹佛大學電腦視覺研究室，美國麻省理工學院電腦科學與人工智慧實驗室，訊連科技，玩美移動、谷歌深度大腦。
8. 未來發展：資料策略，資料長 CDO。

視頻辨識/影像辨識
1. 影像是n個圖像組成，連續圖像，在二維圖像加上時間維度。每秒取樣幾個影格FPS。前處理：資料量大，取樣頻度，再取樣率高低。
2. 動作估計：提取影像中重要資訊的方法，將成對的水平與垂直位移量動作向量v建立估測結果，常用於影像壓縮。假設有三，物體於相鄰兩影格間位移量不大，物體不隨時間變色，物體不隨時間形變。物件層級大於區塊層級，製作動作向量圖設時間點第t影格、第t+1影格，分割畫面mxn個dxd大小區塊，計算絕對誤差值總和。
3. 物體追蹤：以深度神經網絡的滑動視窗(滑窗)掃瞄圖像，找出圖像中特殊表示並輸出類別，計算各組特徵與待追蹤目標物體與第t影格特徵間的最小差異值，當小於某個閾值時可得知追蹤物體的新位置，建立移動軌跡。
4. 行為辨識：實作時有四個難題要解，受取像、方位、光線、角度影響大，相同物種的同一類動作變異大，取樣率不同會被誤判成相同動作，單一圖像有許多干擾物體存在。第三點解法是再採樣策略。第四點解法是搭配注意力機制可濾除不必要的資訊。
- 再採樣策略：單影格行為識別、多影格延遲混合行為識別、多影格早期混合行為識別、多影格緩慢混合行為識別。
- 注意力機制：使用注意力遮罩除掉不必要物體，兩個相同張量的原始圖像與注意力遮罩相乘結果。


