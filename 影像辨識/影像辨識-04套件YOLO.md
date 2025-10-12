# YOLO物件偵測、語意分割

- YOLO原理：以一次性辨識的物件偵測
    - 不同於二次辨識SSD。另有其他物件辨識Fast R-CNN。
    - 深度學習：需要達到三層，最常見的是CNN、NN。CNN是輸入前對圖像做卷積處理。
        1. 卷積層+池化層：交錯用多個遮罩處理、縮小圖像，取出特徵(feature maps)。需要根據輸出需求調整順序順序和次數。
            - kernal/filter/遮罩/濾波器。
            - max-pooling是取出組大值、
        2. 輸入層1層：是flattened之後的pixel格數，根據使用模型的要求，另加數據過濾、標準化、正規化、中心化處理。如224 x 224 px。
        3. 隱藏層n層：n個。
        4. 輸出層1層：產出費別用分類，或產出數值回歸

- YOLO發展史
    - YOLO v1 ~ v5是建構在 .NET程式下，不容易安裝部署。
    - YOLO v1 v3 要建立Linux環境
    - 台灣團隊開發YOLO v5、v7。YOLO安裝
    - YOLO v12加入追蹤。n是nano
    - Ultralytics目前最新v13 (2025年7月)。

- YOLO物件辨識的學習順序
    - MLP
    - 手寫數字辨識
    - Cifar-10辨別貓狗
    - COCO 80種物件辨識 (YOLO起始初學，對應YOLO v12 nano)
    - Cifar-100
    - TensorFlow (Google) | PyTorch (Meta)(較易上手)

- YOLO應用場景
    - 物件辨識+影像追蹤
        - 加入時間序列
        - 常用COCO類別80類，[0]是人。
        - 物件偵測後，YOLO會給定track_id，也會給定該項的X座標、Ｙ座標、信心值、class_id。
        - 注意！track_id跳號，是因80種類別class_id之中有偵測出來，但非你關注的、不顯示的類別。
        - 注意！架設相機/攝影器材位置後，不可任意移動，會影響量測尺度、熱區位置劃設。
        - 注意！限制2D平面圖像或影像。
        - 車道影像應用：可直接計算特定的class_id總數，但計算位移速度必須「梯形校正(trapezoidal correction, perspective correction）)」。(需Google量測長度、現地用皮尺丈量)
        - 電子圍籬應用：以單張圖檔取出熱區位置像素點(可透過roboflow、小畫家)，並設定該辨識物件框與熱區重疊比例表示入侵。配合車輛辨識，可處理車數、車流、車速，最後將資料回傳給邊緣運算使用。
    - 骨架分析
        - 只能取出17個點，無法如mediapipe能標上更多節點可用。
        - 大量被用於醫療、運動產業。
    - 語意分割
        - YOLO原本開發這塊是用在自駕車，精度要求不高。
        - 去背的效果如同illustrator。
    - 

           
- AI建模-訓練權重(抓出特徵)
    - 取得
        - 從roboflow取得資料集。
        - 來自公司內部圖集。
        - 公開資料集。
    - 標註
        - labelImg：若欲公司圖檔，能保有機密性。
            - 從Github下載：https://github.com/HumanSignal/labelImg。
            - 到 data/predefined_classess.txt，變更標注類別名稱。
            - 透過 `python labelImg.py` 開啟標註介面。
            - 開啟指定圖庫的檔案夾(=目錄)，選擇YOLO格式，開始選圖並標註。
            - 單圖標註完畢時，每張圖.jpg旁邊會多一個.txt，內存有標圖的位置座標。
            - 準備資料集，切割資料集訓練集、驗證集(8:2)，再分類.jpg和.txt。
            - 檢視圖檔狀況與標注狀況。
            - 製作yaml檔案 (=對照表)。
                ```yaml
                train:  // 訓練集目錄
                val:    // 驗證集目錄
                test:   // 測試集目錄

                nc:     // 類別總數 int
                names: ['類別1名稱', '類別2名稱']   // 取自標註的 predefined_classess.txt
                ```
        - roboflow：需付費或公開，可能有資安風險。
    
    - 訓練及驗證
        - step 1: 確認資料集位置與yaml檔案指定位置相同。
        - step 2+4: 下載模型，並且開始訓練
            - 專案夾test/images、train/images、valid/images、data.yml
            - 選擇模型尺寸、YOLO版本、訓練週期、批次大小、輸入影像長寬(只能縮小)、訓練可視化。
                - 除了測試集、驗證集，也可以加入測試集。
            - 過程與產出結果
                - yolo12n.pt 是預計訓練的模型。
                - run/detect/train/weight/best.pt 最好的模型
                - run/detect/train/weight/last.pt 最後一次的模型
                - 除了基本的圖，也可以把建模的資料寫入excel。
    - 評價指標
        - 信心值(IoU)：預測框與實際框的交集 / 預測框與實際框的聯集，介於0 ~ 1之間。(做研究會要求公開)
        - 準確率 -> 平均準確率(mAP)：AP[.50:.05:.95]是從0.50到0.95之間的信心值累計
            - 混淆矩陣
                - Precision rate 誤報 = TP / (TP + FP)
                - Recall rate 漏報 = TP / (TP + FN)
                - F1 score 調和平均數 = 2 x Precision x Recall / (Precision + Recall)
