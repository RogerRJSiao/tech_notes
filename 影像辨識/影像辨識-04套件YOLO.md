# YOLO物件偵測

- 原理：以一次性辨識的物件偵測
    - 不同於二次辨識SSD。另有其他物件辨識Fast R-CNN。
    - 深度學習：需要達到三層，最常見的是CNN、NN。CNN是輸入前對圖像做卷積處理。
        1. 卷積層+池化層：交錯用多個遮罩處理、縮小圖像，取出特徵(feature maps)。需要根據輸出需求調整順序順序和次數。
            - kernal/filter/遮罩/濾波器。
            - max-pooling是取出組大值、
        2. 輸入層1層：是flattened之後的pixel格數，根據使用模型的要求，另加數據過濾、標準化、正規化、中心化處理。如224 x 224 px。
        3. 隱藏層n層：n個。
        4. 輸出層1層：產出費別用分類，或產出數值回歸
- 發展史
    - YOLO v1 ~ v5是建構在 .NET程式下，不容易安裝部署。
    - 台灣團隊開發YOLO v5、v7。
    - YOLO v12加入追蹤。n是nano
    - Ultralytics目前最新v13 (2025年7月)。

- 物件辨識的學習順序
    - MLP
    - 手寫數字辨識
    - Cifar-10辨別貓狗
    - COCO 80種物件辨識 (YOLO起始初學，對應YOLO v12 nano)
    - Cifar-100
    - TensorFlow (Google) | PyTorch (Meta)(較易上手)
- 物件追蹤
    - 加入時間序列
    - 以track_id區別，跳號是因為那80種類別有非你關注的、不顯示。
    - 也會偵測出X座標、Ｙ座標、信心值、class_id。

- 評價指標
    - 信心值(IoU)：預測框與實際框的交集 / 預測框與實際框的聯集，介於0 ~ 1之間。(做研究會要求公開)
    - 準確率 -> 平均準確率(mAP)：AP[.50:.05:.95]是從0.50到0.95之間的信心值累計
        - 混淆矩陣
            - Precision rate 誤報 = TP / (TP + FP)
            - Recall rate 漏報 = TP / (TP + FN)
            - F1 score 調和平均數 = 2 x Precision x Recall / (Precision + Recall)

