# 閱讀筆記-LLM概念

> - 資料來源：Ollama 本地 AI 全方位攻略：命令列功能、五大主題測試、RAG、Vibe Coding、MCP，一本搞定所有實戰應用 (出版社：旗標 | 施威銘研究室)
> - 閱讀日期：2026-06
> - 資料整理：蕭瑞展


## 2. 部署自建LLM概念

### 2.1 本地部署框架選擇--整合前端UI+後端推論

| 開發框發 | 下載位址 | 優劣勢 |
| --- | --- | --- |
| Ollama | https://ollama.ai/ | 客製彈性高、擴充功能大，被譽為LLM界的Docker，以描述檔(引用模型參數、模板配置、超參數)隔開封裝。<br>兼容macOS、Linux、Windows系統，免費開源，官方提供多種語言模型可下載。<br>動態分配資料給GPU的VRAM，加速與CPU的協作運算。<br>最大亮點是擁有內建REST API、Python套件、JS套件。 |
| LM Studio | https://lmstudio.ai/ | 底層結合llama.cpp引擎。<br>Apple專屬的MLX加速框架。<br>支援GGUF格式，對初學者友善。 |
| GPT4All | https://www.nomic.ai/gpt4all | 底層結合llama.cpp引擎。<br>以C++和Python為基礎開發。<br>支援從Hugging Face匯入多個模型。<br>LocalDocs是讓本地模型讀取檢索用戶的本地檔案。|
| Text Generation Web UI<br>(Oobabooga webUI) | https://github.com/oobabooga/text-generation-webui | 底層結合llama.cpp引擎、Transformers、ExLllama V2/V3，可直接加載GPTQ、GGUF等格式模型。<br>擴充外掛眾多。(如stablediffusion圖像生成) |
| LocalAI | https://localai.io/ | 底層結合llama.cpp (文字生成)、whisper (語音轉文字)、stablediffusion (圖像生成)、bark (語音生成)等後端引擎，故適合多元功能模型。<br>Golang開發，直接支援macOS、Linux系統，另裝WSL2或Docker可用於Windows。 |

### 2.2 Ollama系統架構

```mermaid
flowchart LR
    A[CLI命令列介面] --> | 1. 送出HTTP request | B["Ollama Server <br>(HTTP Server Port 11434)"]
    B --> | 5. 串流，呈現在terminal | A
    B --> | 2. 呼叫API | C[llama.cpp]
    C --> | 4. 送出HTTP response | B
    C -.- | 3-2. 使用硬體運算力 | D[GPU/CPU]
    C -.- | 3-1. 載入本地模型權重 | E[local model files]
```
#### 運作機制簡述
- 前後端分離，透過CLI送出HTTP請求。(步驟1)
- 透過ModeFile建立自定義模型(客製化)，用於調整提示詞、對話格式、超參數設定、LoRA微調結果，這個檔案可匯出。(步驟3-1)

### 2.3 Ollama常用指令與快捷鍵
| 指令 | 範例 | 說明/備註 |
| --- | --- | --- |
| 下載模型檔到本地端<br>`ollama run model:version` | `ollama run gemma3:1b` | version 不寫或寫 `latest`，都是最新的模型版本。<br>若只是下載但不執行，請用 `ollama pull gemma3:1b` |
| 檢查已載或建立的模型<br>`ollama list` | `ollama list` | 包括 `ollama run model:version` 和 `/save your_chat_session` 建立儲存與於本機的模型。 |
| 顯示特定模型明細<br>`ollama show model` | `ollama show gemma3` | 顯示 Model (模型架構：參數量、上下文長度、詞嵌入維度、量化程度)、Capabilities (模型功能：如具備文字生成、語音轉文字、語音)、Parameters (超參數：溫度、top_k、top_p、stop)、License (授權：範圍、修改時刻) |
| 顯示當前執行的模型與資源分配<br>`ollama ps` | `ollama ps` | 若不完全用VRAM顯存，將在 PROCESSOR 顯示如 31%/69% CPU/GPU。 |
| 關閉模型<br>`/bye` | `/bye` | |
| 結束模型執行<br>Ctrl + d | 
| 效果與 `/bye` 相同。 |
| 複製模型<br>`ollama cp model new_model_name` | `ollama cp gemma3 new_gemma3` | |
| 用 ModeFile 建立新模型<br>`ollam create new_model_name -f "file_path"`  | `ollama create gemma3 -f "C:\Users\rjsiao\Modefile\finance_modefile.txt"` | |
|
| 移除模型<br>`ollama rm model` | `ollama rm gemma3` | |
| 多行發問<br>`""" your requests with multiple lines """` | `"""`<br>`first line`<br>`second line`<br>`"""` | 用 """ """ 包裹多行發問的內容。 |
| 清空對話紀錄<br>`/clear` | `/clear` | 重置模型對話紀錄。|
| 清空終端機畫面<br>Ctrl + l | | 不重置模型對話紀錄。 |
| 終止執行<br>Ctrl + c | | |
| 終止執行<br>`ollama stop` | `ollama stop` | |
| 儲存對話狀態<br>`/save your_chat_session` | `/save chat_translator`| 儲存歷史對話紀錄，用於角色設定、延續上下文。 |
| 載入歷史對話紀錄<br>`/load your_chat_session`<br>或 `ollama run your_chat_session` | `/load chat_translator` | 只將對話紀錄與原本模型組裝 |
| 上載外部文件到模型<br>`ollama run model:version prompt < "file_path"` | `ollama run gemma3:1b "請點列以下文件內容的重點" < "C:\Users\rjsiao\abc.txt"` | 限文字檔 |
| 測試模型速度<br>`ollama run --verbose model "prompt"` || 查看初次載入記憶體時間，包括載入 load -> 讀取 prompt eval -> 生成 eval |

### 2.4 建立客製化模型 - Modefile

1. 撰寫自己的 Modefile (副檔名 = .txt/.py/.js)
    ```
    # 定義載入基礎模型
    FROM gemma3
    # 設定超參數
    # 採樣度(micro_stat)、上下文輸入出token數(num_ctx, num_predict)、輸出重複程度(repeat_penalty, repeat_last)與豐富度(microstat)、創意度(temperature)、固定同問項輸出(seed)、禁止輸出內容(stop)、文字接龍時區間與排序(top_k, top_p, min_p)
    PARAMETER temperature 0.6
    PARAMETER ...

    # 設定系統提示模板(角色、功能描述等)
    SYSTEM """
    你是一位專業的財務分析助手，會解釋財務報表的資料。請提供...
    """
    # 設定初始顯示的歷史對話紀錄，提高回覆穩定性。(可以多組對話紀錄)
    MESSAGE user 是否能幫我分析財務報表?
    MESSAGE assistant 可以的，請給我數據和資料。
    ```

2. 用這份 ModeFile 建立新模型：`ollam create new_model_name -f "file_path"`

### 2.5 啟用自訂環境變數
- 方法 1. 直接修改 OS 系統或使用者的環境變數
  - Windows OS (以系統管理員身分。若加上 `/M` 才能套用於全部使用者)
    ```
    setx OLLAMA_KEEP_ALIVE 5m /M
    setx OLLAMA_GPU_OVERHEAD 536870912 /M
    setx OLLAMA_DEBUG 2 /M
    ```
  - MacOS (在 `~/Library/LaunchAgent/ollama-env.plist` 的設定，才能永久生效)
    ``` Shell
    launchctl setenv OLLAMA_KEEP_ALIVE 5m
    launchctl setenv OLLAMA_GPU_OVERHEAD 536870912
    launchctl setenv OLLAMA_DEBUG 2  
    ```
- 方法 2. 新開終端機時啟用「臨時環境變數」
  - Windows OS
    ``` CMD
    set "OLLAMA_KEEP_ALIVE=5m"
    set "OLLAMA_GPU_OVERHEAD=536870912"
    set "OLLAMA_DEBUG=2"
    ollma serve
    ```
  - MacOS
    ``` Shell
    export OLLAMA_KEEP_ALIVE=5m
    export OLLAMA_GPU_OVERHEAD=536870912
    export OLLAMA_DEBUG=2
    ollama serve
    ```

### 2.6 評估硬體需求
- **模型大小**
  | 模型格式 | 定義 | 優劣說明 |
  | --- | --- | --- |
  | 浮點數精度(FP) | 每個參數占用位元數(bit)。單精度 FP32 是每個參數占用 32 bit，扣除佔位符 1 bit、指數位 8 bit，有效位元 23 bit。半精度 FP16 佔位符 1 bit，指數位 5 bit，有效位元 10 bit。| 精度越高，推理結果越準確，但算力資源消耗大。目前主流是用 FP16，符合模型效能幾乎不減(-0.01%)，且運算加速顯著。 |
  | 模型大小 | `參數數量 x 浮點數精度(bit) / 8 (bit)` | 模型容量越大，運算資源需求越大。 |
  | 量化程度(Q) | 模型參數與中間層運算結果過程中，由浮點數高精度轉換成整數低精度的方式，Q8/Q4/Q2是把參數(權重)分別壓縮為8/4/2個bit，若加入偏移項Q8_0對稱量化(type 0)，則變成Q8_1非對稱量化(type 1)。二次量化_K (k-quart)是進一步再壓縮模型大小，Q4_K表示先壓縮成 4 bit，再二次壓縮成 6 bit。<br>`還原近似權重(w) = 壓縮後整數權重(q) x 縮放比率(block scale) + 偏移項(block min)`。量化後的參數多了縮放比率、偏移項。 | 用於壓縮模型大小，並降低載入記憶體需求容量，達到加速運算。<br>(1)多數採用「訓練後量化」把原本FP32或FP16的參數權重，分群「block」映射到一個整數範圍。壓縮率越大，誤差也愈大，Q4壓縮大於Q8。<br>(2)加入偏移項能提高近似精度，但也增加運算資源Q8_1，如。<br>(3)二次量化是把每 8 個「block」再併成一個 6 bit 的「super-block」，壓縮率越高會犧牲精度 _K_L < _K_M < _L_S。 |

- **模型壓縮**
  - 量化(quantization)
  - 參數剪枝(parameter pruning)：將較不重要的參數剔除。
    - 結構化剪枝：刪除權重時以層、行、列、區塊，提升運算速度效能，但預測效能可能大幅降低。
    - 非結構化剪枝：刪除權重前先對參數重要性評分，再移除不重要的，可能產生稀疏矩陣(sparse matrix)不利運算加速，後續常要再微調(fine-tune)模型。
  - 低秩近似(low-rank approximation)：將大矩陣拆成低維度，如奇異值分解(Singular Value Decomposition, SVD)，逼近給定的原始權重矩陣。
  - 知識蒸餾(knowledge distillation)：用大模型訓練小模型，如DeepSeek 模型是拿 ChatGPT-4o 教師模型的「軟標籤」訓練出來。

- 硬體載入語言模型機制
  - Windows OS
    1. 將模型參數全部載入到 RAM。 ==> 模型成功在 CPU (最小 4 core) 運行。
    2. Ollama 動態分配模型資源到 VRAM。 ==> 模型全部或部分成功在 GPU 執行。
    3. 回覆速度(eval rate)是每秒可生成多少 token，計量單位 tokens/sec。
    4. 若模型是混和專家模型(mixture of experts, MoE)架構，只啟動部分參數行生成回答，故運行速度快。qwen3:30b 是每次生成回答只是參數 30b 的 3b 個。
  - MacOS (統一記憶體架構 + M 晶片，適合大模型，但可能高溫降速)
    1. 將模型參數全部載入到 RAM。 ==> 模型成功在 CPU 與 GPU 之間運行。

  > model size = 量化後的估算值(GB)
  > RAM > model size + OS + 暫存快取 + APP
  > `RAM >= model size x 1.3`
  > GPU VRAM > model size + 暫存快取 + 運算結果。表示充分運用 GPU 平行計算能力達 99% 的效能，CPU 不參與運算。
  > GPU VRAM < model size + 暫存快取 + 運算結果。表示充分運用 GPU 平行計算能力，CPU 也參與運算。
  > `GPU VRAM >= model size x 1.2`
  > GPU 全力運轉速度 = CPU 全力運轉速度 x 3