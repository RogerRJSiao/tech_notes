> AI影像辨識-李建輝
> - 智慧機械、智慧製造
> - 機械手臂、系統整合

- 傳產轉型-智動化、智慧化 -> PHM生產系統
    1. 勞力密集型 vs 資本密集型
        - 面臨困境 -> 失敗原因：產量延遲計算、系統導入問題、設備購置費用高
    2. 鞋業現況
        - EBA發泡材公差2cm，不良品：每日30000雙鞋。
        - 鞋底視覺檢測，過於精準會增加不良品的問題。
            - 微波槍是用2.4GHz正弦波，會有受熱不均問題。爆米花鞋。
        - AI皮料檢驗。

- AI訓練配備
    - CPU Intel core i5以上，與外接RAM執行力有關。
    - GPU 以大量單核處理。1. NVidia、2. Intel OpenVINO。
        - RTX4060Ti GAMING OC 8G。一定要看CUDA運算力，通常有Ti的較高。8G是這個GPU的記憶體。
        - batch size：根據顯示卡記憶體，使用程式將檔案切片輸入資料。

- 視覺影像
    - AI + AOI：用AI輔助AOI。
        1. AI處理不良品檢測，處理特徵是否合規。sort
            - AI函式庫
                - openCV free：開源程式碼。
                - evision
                - hawlcan 開發版30萬元，速度和穩定度都高，用C#調用。購買key(8萬/PC-based機台)限用幾個func。
                - mil 10萬元，速度和穩定度都高。
            - 設備
                - Intel PC, GPU RTX-3050
            - 資料集
                - 以產線的實務建立，多條產線的圖像都要收納。
        2. AOI傳統影像(後)處理，處理大小尺寸判別。二次檢驗。classify
            - 電腦只懂像素(pixel)，加入比例尺(px對應mm)取得真實尺寸。
                - 標線長度、電桿距離。
                - 使用影像做電子圍籬。
            - 自訂棋盤格的精度，用於相機校正，固定相機位置。
            - 相機與影像搭配
                - 機械手臂震動要考慮，無法達到高精度。
                - 輸送帶震動不允許。
                - 水平相位必須校正。
                - 開放式玻璃影響打光，某一段暗房再打光取樣。
    - 視覺處理取樣
        - 2D視覺：X+Y, 單一鏡頭拍照，無法判斷遠近。
        - 2.5D視覺：X+Y+no free Z, 單一鏡頭加紅外線。
            工業相機3萬(焦距、FOB)
            光源(條型光、背光、正光...，金屬反光、寶特瓶透光、玻璃透光、陰影)
            取像最重要，再用軟體彌補拍照
        - 3D視覺：X+Y+Z, 掃描斷面取得點雲。從點雲取出外徑，讓機械手臂對應。30萬

- 車輛辨識
    取得影像是最困難的。
    車輛種類、車速辨識、影像追蹤動態
    影像校正/梯度校正
    電子圍籬處理車數、車流、車速
    邊緣運算

- py管理環境
    - 切換專案用，package之間會有版本衝突，解決open src衍生的問題。

    | 功能 | 指令 |
    | --- | --- |
    | 列出已安裝套件 | `conda list`、`pip list`|
    | 安裝套件 | `conda install opencv-python`、`pip install opencv-python`|
    | 建立並進入檔案夾 | `madir case3118`，`cd case3118` |
    | 建立VM | `conda create --name case31118 python=3.11.8` |
    | 複製VM | `conda create --name rjsiao --clone case31118` |
    | 刪除VM | `conda env remove --name case31118` |
    | 啟用VM | `conda activate case31118` |
    | 停用VM | `conda deactivate` |
    | 列出所有環境 | `conda env list` |
    | (VM)列出當前環境套件 | `conda info` |
    | (VM)安裝cv2套件 [查詢pypi.org] | `pip install opencv-python==4.10.0.84` |
    | (VM)安裝imutil套件 | `pip install imutils==0.5.4` |
    | (VM)進入py互動模式，檢查cv2 | `python`，`>>> import cv2`，`>>> exit()` |
    | (VM)下載套件清單 | `pip freeze > requirements.txt`|
    | (VM)根據套件清單安裝套件 | `pip install --no-cache-dir -r requirement_rjsiao.txt`|
    | (VM)檢查套件 | `conda list` |
    | (VM)開啟VSCode | `code ./your_directory/ch02`|

    - 絕對路徑 vs. 相對路徑
        - 方法1: "" 內的 `\` 要處理成 `\\`，否則會被誤認為\跳脫字元。
        - 方法2: 使用 `r"C:\Users\user\rjsiao\xxx.txt"`

    - 使用者介面
        - 方法1: 直接使用C# EmguCV。
        - 方法2: python Opencv搭配Tkinter, WxPython, QT。
        - 方法3: 把OpenCV打包成exe，內嵌到C#執行。

    - powershell的tab輪選/tab補全
        1. 🔧 補全命令
        ```
        輸入部分命令後按 Tab：
        conda ac[TAB]
        👉 會自動補成：
        conda activate
        ```
        2. 📁 補全檔案或資料夾路徑
        ```
        cd D:\Proje[TAB]
        👉 補成 D:\Projects\（若存在）
        ```
        3. 📦 補全 Conda 環境名稱
        ```
        如果你輸入：
        conda activate cas[TAB]
        👉 會循環補全你電腦上所有以 cas 開頭的環境名稱（例如 case31118）
        ```
        4. 🔁 持續按 Tab 鍵可輪流切換可能選項
        ```
        每按一次 Tab 就會切換到下一個可能的補全
        按 Shift + Tab 可反向輪選（在部分 PowerShell 版本有效）
        ```

- 學習工具
    - STEAM教育網 https://steam.oxxostudio.tw
    - 為你自己學PYTHON https://pythonbook.cc

- AI發展
    - 2025發展進程：LLM和語音辨識、多模態。



    





