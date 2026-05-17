# ㄅㄆㄇ圖卡探險隊 — Vue 3 兒童注音學習遊戲

這是一個用 **Vue 3 + Vite** 開發的注音符號（ㄅㄆㄇㄈ、37 筆）互動學習遊戲，主打「大圖卡、觸控友好、Web Speech API 發音 + 聽音辨位模式」，讓孩子用遊戲方式練習注音。

- ✅ 37 個注音符號完整題庫  
- ✅ 點擊圖卡時自動用 Web Speech API 朗讀  
- ✅ 「聽音辨位」模式：電腦播音、孩子選正確注音圖卡  
- ✅ 聞聲辨位 + 視覺回饋（動畫＋音效＋加分）  
- ✅ 可調整語音語言、語速、音調、音量  

---

## 📦 專案結構一覽

```text
bopomofo-vue-complete-with-speech-and-achievements
├── package.json
├── vite.config.js
├── index.html
├── src
│   ├── main.js
│   ├── App.vue
│   ├── assets/main.css
│   ├── components
│   │   ├── HeaderBar.vue
│   │   ├── HeroSection.vue
│   │   ├── ModeSidebar.vue
│   │   ├── VoicePanel.vue        # 語音設定面板
│   │   └── GameBoard.vue         # 聽音辨位＋圖卡遊戲主畫面
│   └── data
│       └── bopomofo.js           # 37 個注音題庫與 voiceText
```

---

## ⚙️ 安裝與運行

1. 確保你本地已安裝 **Node.js**（官方版即可，Vite 已經內建）。  
2. 將整個專案放到任意資料夾，例如 `bopomofo-vue-complete-with-speech-and-achievements`。  
3. 進入專案根目錄並執行：

```bash
# 安裝依賴
npm install

# 開發伺服器（預設 localhost:5173）
npm run dev

# 打包靜態檔案（輸出在 ./dist）
npm run build

# 用本機預覽打包後內容
npm run preview
```

- 開發時：瀏覽器開啟 `http://localhost:5173`  
- 上線／分享：把 `dist` 資料夾丟到任何靜態主機（GitHub Pages、Netlify、Vercel、一般伺服器）即可直接作為網站使用。[web:345][web:348]

---

## 🎮 遊戲與語音功能說明

### 1. 37 個注音符號題庫

- 在 `src/data/bopomofo.js` 裡，`bopomofoData` 列出了 37 個符號（含 1~5 聲標），每筆都有：  
  - `symbol`: 注音符號（如 `ㄅ`, `ㄆ`）  
  - `word`: 生活詞語（如「包子」「葡萄」）  
  - `voiceText`: 用 Web Speech 朗讀的語句（如 `'ㄅ，包子'`）  

- 你可以自行擴充 `voiceText` 或加入更多生活詞語，讓遊戲更豐富。

### 2. Web Speech 語音發音

- 點擊圖卡時會自動呼叫 `window.speechSynthesis`，朗讀 `voiceText` 的內容。  
- 點擊「喇叭」圖示可以重播當前題目的發音。  
- 適用瀏覽器：Chrome / Edge / Safari / Firefox 等支援 Web Speech API 的現代瀏覽器即可使用。[web:223][web:230]

### 3. 聽音辨位模式（`hearAndSelect`）

- 在「學習模式」中選取 **「聽音辨位」**，電腦會隨機播放發音，玩家點選對應的注音符號圖卡。  
- 答對：加分 10 分，顯示「答對」提示，並觸發 Cheer 動畫與音效。  
- 答錯：不加分，提示正確答案，連擊中斷。

### 4. 語音選項面板（VoicePanel.vue）

- 在畫面下方可調整：  
  - 語言：`zh-TW`（國語）、`zh-CN`（簡體）、`en-US`（英文）  
  - 語速（0.5–2.0）  
  - 音調（0.5–2.0）  
  - 音量（0.1–1.0）  
- 可點「試聽」立即聽到設定後的發音，點「重設」會回到預設值。

### 5. 視覺回饋與 Cheering

- 答對時：  
  - 圖示放大＋彈跳動畫  
  - 小星星／音符動畫飛出  
  - 播放 `cheer.mp3`（歡呼音效）  
  - 分數與正確率統計即時更新  

- 你可自行替換或調整 `src/assets/main.css` 中的動畫關鍵幀，或更換 `src/components/GameBoard.vue` 裡音效檔案路徑。

---

## 💻 開發與擴充建議

- 若想加入更多模式（如「聲調辨識」、「拼音對照」），可直接在 `src/data/bopomofo.js` 的 `modes` 陣列中擴充。  
- 若想切換成不同的語音合成引擎（如雲端 TTS），可將 `speak(text)` 從 `window.speechSynthesis` 改成呼叫 API，保留相同介面即可無痛切換。  
- 未來可串接後端，紀錄孩子在「聽音辨位」模式的答對率，並用圖表呈現進度。

---

## 📄 授權與使用

- 你可以自由將本專案用於：  
  - 個人使用  
  - 家長與孩子在家學習  
  - 學校教學示例（需保留原始註解與來源說明）  
- 若你打算開源或發佈 GitHub，建議保留本 README 內容與作者資訊，方便後續維護。