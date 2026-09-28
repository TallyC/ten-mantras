# 禪意數位經典隨誦系統 — 系統維護手冊與經典移轉指南

> **當前版本**：v2.2  
> **線上發布環境**：[https://ddm-ten-mantras.surge.sh](https://ddm-ten-mantras.surge.sh)  
> **架構形態**：單檔純原生架構（Single-File HTML/CSS/Vanilla JS）+ 靜態音訊資源，無後端、無打包工具依賴、可雙擊離線運行。

---

## 壹、 系統全景與核心架構

本專案「法鼓山十小咒・修持隨誦系統」是一套專為漢傳佛教每日定課設計之現代數位持誦 Web 應用。整體架構秉持全域 Agent 開發準則，具備以下五大核心特色：

1. **零建置單檔架構 (Zero-Build Single File Architecture)**：
   - 核心邏輯、版面樣式、互動腳本皆內聚於 `index.html`，不依賴 Webpack、Vite 或 React/Vue 等框架。
   - 引入 CDN：`Tailwind CSS (v3.4.1)` 用於現代 Utility 佈局；`Lucide Icons (v0.344.0)` 用於經典佛教與操作圖示。
2. **禪意科技護眼美學 (Tally Zen Tech Aesthetic)**：
   - 採用沉穩暖暗調基底（曜石灰黑 `#14171A`、古茶灰 `#1C2024`、羊皮紙微光 `#E6DFD5`）。
   - 字幕高亮以香檳金（Champagne Gold `#E2C07D`）微光搭配琉璃金垂直指示條呈現。
3. **安全快取降級 (SafeStorage)**：
   - 封裝 `SafeStorage` 代理物件，當以 `file:///` 本機雙擊直接開啟時，自動降級至記憶體儲存（`_data`），徹底杜絕瀏覽器 `SecurityError` 拋出導致全頁崩潰。
4. **高保真梵唄音訊工程 (High-Fidelity Audio Engine)**：
   - 音訊經過無損高頻保留與響度標準化（-14 LUFS）。
   - **速度彈性調整機制**：隨誦播放速度非固定不變，而**須依據原始音檔的節奏特質靈活配置**。例如法鼓山早課原音板位節奏偏快，故提供 `0.75x` 慢速精進版輔助初學者；若未來移轉之新經典原音節奏本身已極為沉穩悠緩（如禪門慢板梵唄），則可能維持 `1.0x` 或僅提供微調級距。全系統透過 `preservesPitch = true` 防止音高變形與金屬機械音。
5. **動態字幕即時對齊 (Dynamic Lyric Synchronization)**：
   - 採用基於「梵唄板位字數權重分配演算法（Woodfish-weighted Timestamp Algorithm）」，實現毫秒級時間軸自動對齊與平滑置中滾動。

---

## 貳、 專案目錄結構

```text
c:/000prajna/十小咒/
├── audio/                      # 音訊切片目錄
│   ├── 1.mp3                   # 如意寶輪王陀羅尼 (128kbps CBR)
│   ├── 2.mp3                   # 消災吉祥神咒
│   ├── 3.mp3                   # 功德寶山神咒
│   ├── 4.mp3                   # 準提神咒
│   ├── 5.mp3                   # 聖無量壽決定光明王陀羅尼
│   ├── 6.mp3                   # 藥師灌頂真言
│   ├── 7.mp3                   # 觀音靈感真言
│   ├── 8.mp3                   # 七佛滅罪真言
│   ├── 9.mp3                   # 往生咒 (拔一切業障根本得生淨土陀羅尼)
│   └── 10.mp3                  # 大吉祥天女咒
├── index.html                  # 系統主程式 (完整單檔 HTML/CSS/JS)
└── README.md                   # 系統維護手冊與經典移轉指南 (本文件)
```

---

## 參、 核心資料模型規格 (Data Schemas)

未來若欲移轉至不同經典（如《大悲咒》、《心經》、《楞嚴咒》、《普門品》、《金剛經》），只需依循以下兩個標準 Schema 替換資料：

### 1. 經文結構清單：`TEN_MANTRAS` (Array of Objects)

```javascript
const TEN_MANTRAS = [
  {
    id: 1,                       // 經典唯一流水編號 (整數)
    name: "如意寶輪王陀羅尼",       // 經典/咒語全名
    theme: "滿願息災・福慧圓滿",    // 主題特色短句 (呈現於目錄卡片)
    cbetaId: "T20n1152",         // 大正藏/CBETA 目錄代碼
    source: "《觀自在菩薩如意心陀羅尼念誦儀軌》", // 經典出處文獻
    cbetaUrl: "https://cbetaonline.dila.edu.tw/zh/T1152", // CBETA 線上查閱連結
    benefit: "一心念誦，能除滅一切罪障，所求皆得成就...", // 功德修持指引 (Markdown / 文本)
    lines: [                     // 依梵唄唱誦停頓切分之句子清單
      {
        text: "南無佛馱耶 南無達摩耶 南無僧伽耶", // 句子純文字
        words: [                 // 逐字標音陣列 (供切換字音模式)
          { char: "南", zh: "ㄋㄚˊ", py: "ná" },
          { char: "無", zh: "ㄇㄛˊ", py: "mó" },
          { char: "佛", zh: "ㄈㄛˊ", py: "fó" },
          { char: "馱", zh: "ㄊㄨㄛˊ", py: "tuó" },
          { char: "耶", zh: "ㄧㄝ", py: "yē" },
          // ...其餘字
        ]
      },
      // ...其餘句子
    ]
  },
  // ...其餘經典
];
```

### 2. 斷句時間軸對齊清單：`MANTRA_LINE_TIMESTAMPS` (Object)

將每首經典的 `id` 作為 Key，其 Value 為長度與 `lines` 完全一致的區間時間陣列（單位：秒）：

```javascript
const MANTRA_LINE_TIMESTAMPS = {
  1: [
    { start: 0.0, end: 6.8 },    // 第 1 句音訊起訖點
    { start: 6.8, end: 12.3 },   // 第 2 句音訊起訖點
    // ...
  ],
  2: [ /* 經典 2 各句起訖 */ ],
  // ...
};
```

---

## 肆、 梵唄音訊工程與時間戳推導 SOP

若要為新經典錄製、剪輯音訊並建立精準歌詞對齊，請嚴格執行以下標準程序：

### 步驟 1：音源提取與精確裁切 (yt-dlp + ffmpeg)

1. **獲取高音質原音**：
   ```bash
   yt-dlp -x --audio-format mp3 --audio-quality 0 -o "source.mp3" "<YOUTUBE_OR_SOURCE_URL>"
   ```

2. **定位各首起訖時間（毫秒級）並無損導出**：
   ```bash
   # 範例：導出第 1 咒 (0秒 至 28.5秒)，加入 0.2 秒平滑淡入淡出，防止起頭爆音
   ffmpeg -y -ss 00:00:00.000 -to 00:00:28.500 -i source.mp3 -af "afade=t=in:ss=0:d=0.2,afade=t=out:st=28.3:d=0.2" -b:a 128k audio/1.mp3
   ```

### 步驟 2：評估原始音檔特質與播放速度配置原則

> [!IMPORTANT]
> **播放速度的選項設計並非一成不變，必須依據「原始音檔的節奏快慢」與「音質耐受度」客製化決定：**
> 1. **快板/定課原音（如法鼓山早晚課十小咒）**：
>    - 原始節奏極快（一字約 0.3~0.4 秒），初學者難以同步對位，故實測配置 `1.0x` 與 `0.75x` 兩檔最適宜。
>    - 若強行降至 `0.5x` 或 `0.25x`，瀏覽器時域延展演算法（WSOLA）會引發刺耳之「金屬顫音/電子機器人雜音」，且呼吸拖腔嚴重失真，應予刪除。
> 2. **慢板/悠緩梵唄（如傳統寺院大悲懺、晚課讚偈、禪坐梵唄）**：
>    - 原始節奏本已沉穩慢板，甚至字句間有長引磬、長拖腔，此時**不宜再降速**（否則拖腔拉長數倍變形），反而可維持 `1.0x`，或甚至提供 `1.2x` 予日常熟練定課者選擇。
> 3. **前端速度選單客製方式**：
>    - 若欲調整選項，直接修改 `index.html` 中 `#audioSpeedSelect` 的 `<option>` 標籤，並在 `ChantAudioPlayer.playbackRate` 允許範圍內同步加入即可。

### 步驟 3：利用木魚板位權重演算法生成時間戳

傳統梵唄念誦具有極規律之字拍特性（木魚一字一拍，句末煞板或拖腔通常約 1.2 ~ 1.5 拍）。當經文句子較長時，直接使用以下 Python 腳本即可自動推導出高度吻合的時間軸：

```python
# 各首經典音訊總長度 (秒)
DURATION = 28.5

# 經文字句清單
lines = [
    "南無佛馱耶 南無達摩耶 南無僧伽耶",
    "南無觀自在菩薩摩訶薩 具大悲心者",
    # ...
]

# 去除空白計算各句有效字數，句末加上 1.2 拍之停頓權重
weights = []
for line in lines:
    clean = line.replace(' ', '').replace('，', '').replace('。', '')
    weights.append(len(clean) + 1.2)

total_weight = sum(weights)
current_time = 0.0
timestamps = []

for w in weights:
    line_duration = (w / total_weight) * DURATION
    end_time = round(current_time + line_duration, 2)
    timestamps.append({"start": round(current_time, 2), "end": end_time})
    current_time = end_time

print(timestamps)
```

---

## 伍、 前端核心模組與互動機制

### 1. 梵唄播放管理器 (`ChantAudioPlayer`)

- **狀態管理**：
  - `currentMantraId`：目前撥放之經典 ID。
  - `mode`：`single`（單遍）、`loop`（循環）、`chain`（10咒連續/全本連播）、`target`（自訂指定遍數）。
  - `speed`：當前設定速度（預設為 `1.0` 或依原始音檔特質配置），呼叫 `audio.playbackRate = speed`，並強制指定 `audio.preservesPitch = true`。
  - `completedCount`：累計持誦遍數。
- **跨篇自動翻頁連播 (`chain` 模式)**：
  - 當經典撥放結束觸發 `onended` 事件時，判斷若為 `chain` 模式，自動計算 `nextId = currentMantraId + 1`。
  - 呼叫 `openPracticeView(nextId)` 自動切換 UI 至下一首，並立即執行 `ChantAudioPlayer.setMantra(nextId, true)` 無縫開唱。
- **逐句高亮與置中滾動演算法**：
  - 監聽 `audio.ontimeupdate`，取得 `currentTime`。
  - 於 `MANTRA_LINE_TIMESTAMPS[id]` 比對出命中之 `lineIndex`。
  - 批次更新 DOM，為當前句添加 `.speaking-active` 樣式。
  - 透過 `currentEl.scrollIntoView({ behavior: 'smooth', block: 'center' })` 將當前持誦句始終保持於使用者眼平視野正中央。

### 2. 本地儲存與每日打卡日曆 (`SafeStorage` + `Calendar`)

- 儲存鍵值：
  - `ten_mantras_history`：記錄每日修持日期與各咒持誦遍數。
  - `ten_mantras_settings`：記錄字音喜好（注音/拼音/純文字）、音量大小、自訂定課遍數目標。
- 打卡日曆：
  - 自動生成當月 1 至 31 日方格。
  - 若當日持誦次數 > 0，即印上琉璃合掌印記（🙏），點選日期可調閱當日修持清單。

---

## 陸、 移轉至新經典 Step-by-Step SOP

若您欲建立新經典專案（例如《大悲咒與八十四相修持系統》、《金剛經修持隨誦》）：

1. **建立新目錄**：
   - 建立資料夾 `c:/000prajna/<經典名稱>/`。
   - 複製本專案之 `index.html` 與相關目錄結構至新目錄。
2. **準備音訊檔案並評估速度檔位**：
   - 將念誦音檔剪輯為 `audio/1.mp3`, `audio/2.mp3`...。
   - 建議採用 128kbps CBR MP3，確保檔案大小適中（單曲約 300KB ~ 1MB），首頁載入零延遲。
   - **檢視音檔節奏**：依據原音節奏決定速度下拉選單選項（若為急板，可提供 `1.0x / 0.75x`；若為沉穩慢板，可僅保留 `1.0x` 或新增微調檔位）。
3. **替換經文資料陣列**：
   - 搜尋並修改 `index.html` 中的 `const TEN_MANTRAS = [...]`。
   - 依照格式填入各段落之名稱、出處、導讀指引、斷句文字及注音/拼音。
4. **設定時間軸對齊**：
   - 執行本手冊第肆章之 Python 腳本，產生每段經文的起訖秒數。
   - 替換 `const MANTRA_LINE_TIMESTAMPS = {...}`。
5. **調整專案名稱與版本標籤**：
   - 搜尋 `<title>`、頂部導覽列標題與版本 Badge（例如升級為 `v1.0` 或新專案名）。
6. **本機驗證與發布**：
   - 於本機瀏覽器開啟驗證：
     1. 逐句隨誦與字幕滾動是否置中。
     2. 速度切換與聽感是否自然、無電子失真音。
     3. 跨章節連播是否順暢翻頁。
     4. 重新整理後遍數計數與日曆打卡是否留存。
   - 執行 Surge 部署：
     ```bash
     npx surge . <your-project-domain>.surge.sh
     ```

---

## 柒、 常見問題與排查指南 (Troubleshooting)

1. **變速播放時出現電子機器雜音或破音？**
   - **原因**：部分瀏覽器在 `playbackRate` 偏離 1.0 過多（尤其是低於 `0.7` 如 0.5x、0.25x）時，時域音訊延展演算法（WSOLA）會產生嚴重的頻譜顆粒化與金屬諧波。
   - **解決方式**：
     - **切勿一律套用固定倍速**，務必依據「原始錄音的原速與腔調特質」實測決定速度檔位。
     - 若原音偏快，下限建議設定在 `0.75x ~ 0.8x`；若原音已非常悠緩慢板，則建議保持 `1.0x` 原速。
     - 程式碼層級務必維持 `audio.preservesPitch = true`（鎖定原音基頻）。
2. **本機雙擊開啟（`file:///`）時，持誦遍數無法累計？**
   - **原因**：部分安全性較高之瀏覽器限制 `file://` 協定存取 `localStorage`。
   - **解決方式**：系統已內建 `SafeStorage` 封裝，會自動靜默降級至記憶體變數，確保程式碼不中斷拋錯。建議使用者透過靜態 HTTP 伺服器或 Surge 網址使用，以享有跨裝置保存之功能。
3. **連續播放下一首時經文沒切換？**
   - **原因**：`onended` 回呼函式若僅切換音訊而未呼叫 `openPracticeView(nextId)`，會導致畫面留在舊經文。
   - **解決方式**：已在 `ChantAudioPlayer` 中整合 `openPracticeView(nextId)` 與播放器同步重設，確保音畫同步翻頁。

---

*南無本師釋迦牟尼佛・願以此功德，普及於一切，我等與眾生，皆共成佛道。*
