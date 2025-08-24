## 傳統影像處理

> 使用時機：AI訓練前的資料前處理。不良圖檔的校正。

- 影像預處理
    > 可能不適用產業：醫療圖檔(檔案龐大、特定格式)。
    - 作業流程
        > - Prompt (by Gen-AI): 請用這張圖幫我規劃影像的處理步驟。
        
        1. 使用灰階(2通道)取代彩圖(3通道)，把資料量縮減。必要時要處理通道轉換(RGB2BGR)，導入模糊處理。
            - 濾除雜訊的模糊化：如先用馬賽克產生胡椒鹽雜訊，再用高斯模糊。 
            - 促進灰階處理方法：自適應平均、高斯自適應法、等化法。
        2. 定義閥值取出邊界，對RGB圖檔進行二值化(通常是255和0黑白)，可能自動或手動處理，全依經驗調整。
            - 若二值化效果不佳，回到1.處理雜訊與圖像轉換。
        3. 使用侵蝕與膨脹去除雜訊：根據需求規劃兩者順序，通常是先侵蝕(吃掉內容)、再膨脹(增大範圍)。
            - 使用濾鏡(kernal, 濾波器)(= 遮罩filter)處理圖像，在圖面上滑動。與AI訓練的卷積處理相同。
            - 開運算(白帽)：結合先侵蝕再膨脹兩功能。
            - 閉運算(黑帽)：結合先膨脹再侵蝕兩功能。
        4. 檢測邊緣，對灰階圖檔轉換出邊緣圖檔，用於提取輪廓
            - 常用邊緣檢測工具：Laplacian、Canny、Sobel。

- OCR (光學字元識別)
    - 套件：[Tesseract-OCR](https://github.com/tesseract-ocr/tesseract) [ref: https://twgo.io/yebtq]
    - 安裝步驟
        1. Windows版Tesseract-OCR 安裝程式的下載網址：https://github.com/UB-Mannheim/tesseract/wiki。
        2. Tesseract-OCR預設只有英文語言包，需要自行至語言包網址：https://github.com/tesseract-ocr/tessdata_best 下載繁/簡中文語言包。
        3. 啟動安裝 tesseract-ocr-w64-setup-5.4.0.20240606.exe。
        4. 在 C:\Program Files\Tesseract-OCR\tessdata 貼上語言包檔案：
            - chi_sim.traineddata       #簡體中文
            - chi_tra.traineddata       #繁體中文橫書
            - chi_tra_vert.traineddata  #繁體中文直書
        5. 啟動虛擬環境 `conda activate your-vm-name`，安裝套件 `conda install Pillow`，`pip install pytesseract==0.3.8`。
        6. 執行 py。若有未辨識出的文字，透過影像再處理，讓所有文字都儘可能被辨識出來。
        7. 讀取順序(通道)是第幾張圖(頁碼)。定義可框選位置x1,y1,x2,y2。

        > Tip: py 原碼加入 input()，讓資料顯示留在 terminal。

- OpenCV 搭配 OCR 的使用
    - 辨識原理與步驟-傳統車牌辨識
        0. 設定拍照位置及ROI敏感區
        1. 讀取圖檔轉成灰階圖
        2. 使用高斯模糊轉成平滑圖
        3. 檢測邊緣用 Sobel、Canny (略除不必要資訊)
        4. 執行閉運算、框出輪廓：先膨脹後侵蝕，找出輪廓回疊在複製的原圖上
        5. 遍歷所有輪廓，透過判斷式(邊界矩形 寬 > 高 * 2 或用比例)取出疑似車牌的輪廓
        6. 透過 numpy 擷取疑似圖檔
        7. 比對並印出文字
        > Tip: 校正角度和形狀(為提高OCR的辨識度)