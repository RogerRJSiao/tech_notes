# 閱讀筆記-LLM概念

> - 資料來源：文科生也能輕鬆實現！自建自用大語言模型LLM (出版社：財經傳訊 | 江達威)
> - 閱讀日期：2026-04
> - 資料整理：蕭瑞展

## 1. 為何要自建自用大語言模型

### 1-1. 資訊查詢與AI發展史
- 搜尋引擎：Google, Yahoo
- 語音轉文字(speech-to-text) + 搜尋引擎：Apple Siri (2007), Google Assistant, Amazon Alexa, MS Cortana
- 文字型聊天機器人(chatbot)
- 導入LLM的應用 + UX：ChatGPT (2022)
    - OpenAI開發，2017年初次發布模型GPT (generative pre-trained transformer)，2022年提供ChatGPT服務，2024年GPT-4引入多模態。
- 歐盟於2024年頒布並實施人工智慧法案(AI Act)：強調AI應具備可解釋(explainable)、可負責(responsible)精神。
- 第一波AI熱潮1950年代，第二波1980年代發展出2大方向專家系統(expert system, ES)、類神經網絡(artificial neural network, ANN)。第三波興起於2010年代至今。

### 1-2. AI應用領域
- 領域1. 電腦視覺(computer vision, CV)
- 領域2. 自然語言處理(natural language procssing, NLP)
- 領域3. 資料科學(data science, DS)
- 其他領域：推薦系統(recommender system, RS)、智慧決策支援系統(intelligent decision support system, IDSS)
- 發展強度：窄AI/弱AI(ANI)、通用AI/強AI(AGI)、超AI(ASI)
    - 強AI程度：(由低至高) conversational < resoning < autmomous < innnovating < organizational AI

### 1-3. 優化LLM模型表現
- 重新訓練
    - 概念：適用於開放模型，取得原始資料集，建構大量繁複的乘加算 + 激勵函數運算。
    - 劣勢：模型改動大，投入技術與成本高
- 微調
    - 概念：使用預訓練模型(pre-trained model)，進行微調(fine-tuneing)
    - 優勢：(1) 避免洩漏商業機密、(2) 避免系統受誤導、(3) 不受限於原有模型的特定價值避答機制、(4) 模型改動較小，但投入技術與成本仍高。
- 模型輕量化
    - 概念：透過量化(quantization)、剪枝(pruning)、知識蒸餾(distallation)、二值化(binatization)等資訊技術，讓原碼參數量需求降低，提供一般PC可用的模型。
    - 優勢：(1) 降低算力負擔，無須另外購置AI PC設備(CPU+GPU or CPU+NPU)
    - 劣勢：誤判比例增加
- 檢索增強生成(retrival-augmented generation, RAG)
    - 概念：由使用者限定資料範疇，強化情境相關資訊，再讓LLM找出解答、產生文字內容。
    - 優勢：(1) 無須資料清洗、(2) 根據預算可搭配微調、RAG技術之開發。
    - 案例：個人知識管理(personal knowledge management, PKM)。Deep Research 開發與應用 - OpenAI 或 Perplexity。
- 思考鏈(chain-of-thought, CoT)
    - 概念：避免或改善AI幻覺
- 評估 LLM 能力測試 (四大類項 - 英文、程式、數學、中文)
    - MMLU：57類問題範疇
    - DROP：斷句分拆、閱讀理解
    - IF-Eval：列舉
    - GPQA-Diamond
    - SimpleQA
    - LiveCodeBench：程式
    - Codeforces：程式
    - SWE Verified：程式
    - FRAMES：RAG能力
    - AIME 2024：數學
    - MATH-500：數學
    - CLUEWSE：中文
    - C-Eval：中文
    - Gecko：文生圖
    - DSG1k：文生圖
    - TIFA：文生圖
    - DrawBench：文生圖
    - AILuminate：倫理安全

### 1-4. 硬體選擇-基礎設備
- 比較雲端與地端架設
    | 項目 | 雲端架設 (遠端架設) | 地端架設 |
    | --- | --- | --- |
    | 實作方式 | 基於Open AI在Azure公有雲(Azure OpenAI, AOAI)，各自在雲端開立獨立空間，進行各自的微調訓練。 | 自行購置並架設高算力GPU的主機，以及使用本地足量記憶體，訓練及推論模型。免用 Ethernet (RJ-45) 或 WiFi (4G/LTE、5G)。 |
    | 優勢 | (1) 彈性擴增算力<br>(2) 模型儲存容量大<br>(3) 因應非開放原碼要求<br>(4) 初期建置成本較低 | (1) 不受制於外部網路品質<br>(2) 可自行管理資安防護<br>(3) 節省長期變動成本 |

- 評估推論模型時的硬體需求【重要!】
    1. `RAM 位元組數 = 參數數量 × 每個參數的 bit 數 ÷ 8`
        - 以 llama3.2 的 3b 版本為例，在 Ollama 官網資訊頁面，模型參數 `3.21B`，量化 `Q4_K_M`，容量需求佔量估算 `2.0GB`。==> 這個模型的參數32.1億個，每個參數量化成4-bit，`3.21 × 10^9 × 4 bit ÷ 8 = 1.605 × 10^9 bytes`。
        - 另外加上其他資訊，通常 +20~40%，如KV cache（推理用）、metadata、tensor alignment、runtime buffer，故為官方標註的 2.0GB。
    2. `每秒 token 數 = 算力 TOPS (op/sec) × 利用率 ÷ 每個 token 所需運算量 (op/token)` 
        - 此為理論值，實際更受模型權重讀取、KV cache 存取影響，通常跟模型大小成正比。
        - 算力 40 TOPS = 每秒可以做 40 兆次整數運算。(trillion operations per second, TOPS)
        - 利用率通常不高，GPU 30%-70%、NPU（手機）10%-40%、CPU 更低。
        - 每個參數在推論時，通常會用到「乘法 + 加法」各一次，矩陣乘法（matrix multiplication）`y_i = w1*x1 + w2*x2 + w3*x3 + ... + wN*xN`，故每個 token 所需運算量約 2 次運算。
        - 若載入比 7b 大的模型，建議安裝在工作站(work station)、伺服器(server)，串接多部伺服器執行。也可能用 40 TOPS 效能 AI PC 執行，表示在 8-bit 精度下每秒最多 40 兆次運算。
    3. 檢查主機資源 (最低規格：Win10 以上 + RAM 16 GB + 硬碟空間 20 GB)
        - OS系統：設定 > 系統 > 系統資訊 > Windows 規格 > 版本
        - 記憶體：設定 > 系統 > 系統資訊 > 裝置規格 > RAM
        - 硬碟：本機 > 檢視 > 硬碟大小總計
        - GPU/高筱能運算的加速晶片：設置大量硬體乘加器(multiply accumulator, MAC)，捨棄原本 3D 繪圖需要的著色器(shader)
        - CUDA：由 NVIDIA 開發的軟體，用於搭配 GPU 加速開發 AI 模型

    4. 選定模型放置路徑 (合理配置 SSD 資源)
        - 預設路徑：`C:\Users\rjsiao\.ollama\model\blobs\*`
        - 變更路徑：設定 > 系統 > 系統資訊 > 進階系統設定 > 環境變數 > 新增變數 `OLLAMA_MODELS` (變數值是 `D:\MODELS`) > 重啟電腦 > 新 pull 取得的模型都改在新路徑。
        - 以 .ollama 命名，在 Linux OS 此目錄 (directory) 會隱藏，但在 Windows OS 這個檔案夾 (folder) 不隱藏。其下一層的 models\blobs 存放一個模型檔、多個附屬檔。
    5. 其他建議作法
        - 不建議以舊電腦執行模型，因為高階電腦才適合虛擬化
        - 不建議以舊電腦執行模型，因為舊電腦不適合
        - 不建議用虛擬記憶體跑 LLM，因為 SSD 頻繁切換資訊造成存取效率仍低於 RAM。
        - 隨時留意硬體加速支援的相關設定，包括 LLM 軟體偵測硬體存在並使用，以及 NVIDIA GPU 最新支援設定

### 1-5. 軟體選擇-LLM模型
- 模型來源：Hugging Face, GitHub, Ollama
    - 檢查已下載模型與版本：`ollama -v`
- 開放模型、部分開放模型、專屬/封閉模型
    - Linux基金會提出模型開放框架(Model Openness Framework, MOF)：17個區塊可開放
        - 程式碼6：評估用程式碼、預處理程式碼、訓練程式碼、推論程式碼、模型架構、函式庫與工具
        - 資料6：資料集、評估用資料、簡單模型輸出、模型權重與參數、模型元資料、組態/配置檔
        - 文件5：資料卡、研究論文、評估結果、模型卡、技術報告
    - 部分開放：open-weight models (開放權重模型)、open-washing (假開源/洗白式開源)
    - 現有模型
        - 封閉模型 - Claude - Amazon/Google 投資 Anthropic
        - 封閉模型 - ChatGPT/Copilot - MicroSoft 投資 OpenAI
        - 開放模型 - Llama - Meta
        - 封閉模型 - Bard (2023)/Gemini - Google
        - 開放模型 - Qwen (2023-) - Alibaba
        - 開放模型 - Granite - IBM
        - 部分開放模型 - DeeepSeek - DeepSeek
        - 封閉模型 - Grok - xAI

- 適用繁體中文語言的模型
    - 提示詞要求請用中文回答。
    - 找尋名稱有chinese的模型。
    - 找尋國科會TAIDE訓練模型。
    - 選擇Qwen模型。

- 參數數量
    - 計量單位：0.5b = 5000萬，33m = 3300萬
        - 以 [llama3](https://ollama.com/library/llama3) 為例，有 80 億(8b)、700 億(70b) 兩個版本。
        - Ollama 官網上提供的版本命名，以-和:標註，可能與其他 LLM 網站命名不同。
        - 以 `ollama run llama3.2` 下載，等同於下載最新的版本，`ollama run llama3.2:latest`。

    - 標記器(tokenizer)：將自然語言字詞轉成 token，將文本轉換為模型可以處理的數據，再進行處理。
        - 通常不是一字對應一個 token，且雲服務常以此作為收費計價，如將 input、cached input、output 分開計算。[Open AI Tokenizer](https://platform.openai.com/tokenizer)

        - 資訊領域 token (符元/代幣/權杖)

            | 情境  | 翻譯  | 說明  | 舉例  |
            | -- | -- | -- | -- |
            | NLP / AI | 詞元 | 文字被切分後的最小單位，模型計算用的文字單位  | "Hello world" → `Hello`, `world`（2 個 token）  |
            | 程式語言 / 編譯器 | 詞法單元 / 記號 | 程式碼中最小有意義的元素 | `int x = 10;` → `int`, `x`, `=`, `10`, `;`   |
            | 資安 / API 驗證 | 權杖 / 令牌 | 用於身份驗證的憑證 | API 請求 header：`Authorization: Bearer abc123` |
            | 區塊鏈 / 加密貨幣 | 代幣 | 數位資產或權益單位    | 在 Ethereum 上發行的 ERC-20 token |
            | 一般資訊語境 | 標記 | 泛指被識別或標示的單位  | HTML 中的 `<div>` 可視為一種 token |


- 模型種類

    | 模型分類 | 說明 | 舉例 |
    | --- | --- | --- |
    | 尋常類模型(無特定/All) | 衍生版的命名相似，可能來自原開發者、非官方開發者 | mistrallite (Ｍistral), zephyr (Ｍistral衍生), openhermes (Ｍistral衍生), mathstral (Ｍistral，數理), phi (MicroSoft), llama3-gradient (Meta), llama3-chatqa (Meta), tinyllama (Meta), gemma (Google), codegemma (Google), shieldgemma (Google), qwen (Alibaba), granite (IBM), deepseek (DeepSeek) |
    | 嵌入模型(embeding model) | 實現RAG | granite-embedding (IBM) |
    | 視覺輸入模型(vision) | bakllava (Ｍistral與LLaVA) | |
    | 提供工具呼叫模型(tools) | 當尋常類模型被以工具型式呼叫。 | mistral (Ｍistral), qwen (Alibaba) |

    - 混和專家模型(mixture of experts, MoE)：例Mistral、DeepSeek。
    - 推論模型(resoning model)
    - 無碼模型(dolphin model)：不太需要 fine-tune、不太需要複雜 prompt，但比較容易產生敏感內容、幻覺。

- 設定使用者介面/窗口
    - CLI/CUI：開啟 CMD
    - 瀏覽器 (以 Chromium 開發) + 延伸程式：MS Edge 是內建的 (pre-intalled, build-in) + Page Assist 附加元件 (extention, plug-in, add-on)，兩者相容/兼容(compatibility)，並將語系改成繁體中文。
        - 從 Ollama 下載的模型皆為預訓練模型，Page Assist 預設是指向 DuckDuckGo 搜尋引擎補充回答資料及以外的提問。若為測試模型本身，或不允許外部搜尋引擎提升回覆品質，Page Assist 可切換此功能，自行決定是否讓模型指向某個搜尋引擎。
        - Page Assist 自訂模型【進階】
        - Page Assist 衍生子模型【進階】
    - Open WebUI + Docker Desktop on Windows + WSL：WSL 讓 Window 系統具備與 Linux 相仿的執行環境，讓 Open WebUI 誤以為目前處於 Linux 可執行。
    - AingDesk
        - 劣勢：(1) 僅支援簡體中文、(2) 可用瀏覽器僅中國的百度、搜狗、360搜素、(3) 以API呼叫線上LLM亦為中國公司的服務。
    - AnythingLLM
        - 分成不收費的桌機版、月費方案的雲端服務版。
        - 若系統本身沒有 Ollama 函式庫，安裝過程會被詢問是否順便安裝，關乎於模型運作與是否用GPU執行。
        - 優勢：屬於本機端的應用程式，可選擇 LLM 偏好(Ollama、LM Studio、外部 LLM 線上服務...)。
    - Jan
        - 由 Homebrew Computer 開發
        - 優勢：(1) 直接匯入並使用 .ggif 模型檔，(2) 可選的模型數量相當多，(3) 選取模型時預先提示記憶體與運行效率
    - GPT4All
        - 優勢：安裝簡易
        - 劣勢：閃退發生在點擊icon或使用過程，比起 Ollama 可選的模型數量相當可選的模型數量相當有限
    - Chatbox AI
        - 啟用時的指定AI模型提供者，分成Chatbox AI Cloud、API key (雲服務)、本地模型(本機端)
        - 優劣勢-電腦版(免費)：屬於本機端的應用程式，以個人 PC 為主
        - 優劣勢-網頁版(月費)：無法確保隱私，易受網路服務品質影響
        - 優劣勢-手機版(免費)：安卓或蘋果皆可，或以.apk手動安裝
    - LM Studio
        - 優勢：易於切換用戶身分，推論時顯示當前硬體資源消耗狀態。
        - 劣勢：支援小型RAG，文件數量5件，總容量不超過30MB。
    - Pinokio 
        - 安裝時出現「Windows已保護您的電腦」，點擊其他資訊 > 仍要執行。主因是該開發商尚未列於 MS 官方註冊認可的名單內。
        - 優勢：生成內容多樣，值得一試!!!

- 更新升級模型
    - 可能要同時升級 Ollama 版本，否則不支援新版模型。
    - 可能出現不可預知錯誤，特別是使用尚未正式 1.0 版。==> 在測試 PC 安裝使用，或暫不更新該模型。
    
### RAG - 以 Page Assist 實作為例
- 事前作業
    - (必要) 安裝嵌入模型
        - Page Assist 會強烈建議在「RAG 設定」裡安裝 nomic-embeded-text。
    - (必要) 新增知識範疇
        - Page Assist 可支援 .pdf、.txt、.csv、.md 檔案類型。
        - 知識新增成功的狀態：待處理 -> 處理中 -> 已完成。
    - (非必要) 新增提示詞
        - 可「自訂提示詞」，也可用「Copilot提示詞」讓它參考前者生成。
- 執行推論
    - 注意記憶體是否充足
        - Page Assist 若顯示「記憶體不足」，關閉多餘 process，重新送出提示詞。
- 優化表現
    - 調整嵌入式模型細部參數
    - 更換提示詞
    - 更換同款模型但餐數量更多的 => 回應速度變慢
    - 更換不同款但智慧程度更高的 => 根據需求，建立標竿測試報告，比對不同模型的輸出品質

### VLM - 以 Page Assist 實作為例
- 事前作業
    - (必要) 安裝視覺語言模型
        - 安裝如 llava:7b 模型