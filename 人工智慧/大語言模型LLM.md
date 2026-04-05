# 閱讀筆記-LLM概念

> - 資料來源：文科生也能輕鬆實現！自建自用大語言模型LLM (出版社：財經傳訊 | 江達威)
> - 閱讀日期：2026-04
> - 資料整理：蕭瑞展

## 1. 為何要自建自用大語言模型

### 1-1. 資訊查詢發展史
- 搜尋引擎：Google, Yahoo
- 語音轉文字(speech-to-text) + 搜尋引擎：Apple Siri (2007), Google Assistant, Amazon Alexa, MS Cortana
- 文字型聊天機器人(chatbot)
- 導入LLM的應用 + UX：ChatGPT (2022)
    - OpenAI開發，2017年初次發布模型GPT (generative pre-trained transformer)，2022年提供ChatGPT服務，2024年GPT-4引入多模態。

### 1-2. AI應用領域
- 領域1. 電腦視覺(computer vision, CV)
- 領域2. 自然語言處理(natural language procssing, NLP)
- 領域3. 資料科學(data science, DS)
- 其他領域：推薦系統(recommender system, RS)、智慧決策支援系統(intelligent decision support system, IDSS)
- 發展強度：窄AI/弱AI(ANI)、通用AI/強AI(AGI)、超AI(ASI)

### 1-3. 優化模型表現
- 微調
    - 概念：使用預訓練模型(pre-trained model)，進行微調(fine-tuneing)
    - 優勢：(1) 避免洩漏商業機密、(2) 避免系統受誤導、(3) 不受限於原有模型的特定價值避答機制。
- 模型輕量化
    - 概念：透過量化(quantization)、剪枝(pruning)、知識蒸餾(distallation)、二值化(binatization)等資訊技術，讓原碼參數量需求降低，提供一般PC可用的模型。
    - 優勢：(1) 降低算力負擔，無須另外購置AI PC設備(CPU+GPU or CPU+NPU)
    - 劣勢：誤判比例增加
- 檢索增強生成(retrival-augmented generation, RAG)
    - 概念：由使用者限定資料範疇，強化情境相關資訊，再讓LLM找出解答、產生文字內容。
    - 優勢：(1) 無須資料清洗、(2) 根據預算可搭配微調、RAG技術之開發。
    - 案例：個人知識管理

### 1-4. 比較雲端與地端架設
| 項目 | 雲端架設 (遠端架設) | 地端架設 |
| --- | --- | --- |
| 實作方式 | 基於Open AI在Azure公有雲(Azure OpenAI, AOAI)，各自在雲端開立獨立空間，進行各自的微調訓練。 | 自行購置並架設高算力GPU的伺服器，以及使用本地載入、推論模型的足量記憶體，訓練及推論模型。 |
| 優勢 | (1) 彈性擴增算力<br>(2) 模型儲存容量大<br>(3) 因應非開放原碼要求<br>(4) 初期建置成本較低 | (1) 不受制於外部網路品質<br>(2) 可自行管理資安防護<br>(3) 節省長期變動成本 |

