# 根莖葉 / rễ , thân , lá studio
## 植物生長追蹤系統 · 開發路線圖

> 核心目標：**用任何手段達到預測並控制生長**
> 展覽截止：2026 年 10 月

---

## 系統架構

```
手機拍照（upload.html）
    ↓
Vercel API（upload-photo.js）
    ├─ 存圖 → Supabase Storage
    ├─ 存資料 → photos 表
    └─ AI 分析 → Gemini 1.5 Flash → ai_analysis 表
                                          ↓
                              plant.html 展示頁顯示
```

**兩個 Repo：**
- `plant-pos` → 上傳工具（手機用）`https://plant-pos.vercel.app`
- `plant-profile` → 展示頁（客人、展覽用）`https://plant-profile.vercel.app`

**Supabase 專案：** `edvklxnzhuhmijdwyzrl`

---

## ✅ 已完成

### 拍照上傳（plant-pos）
- Ghost 相機：疊上一張照片半透明輔助對齊構圖
- 雙角度上傳：正面（必填）+ 細節（選填），共用同一個 session_date
- ArUco marker 自動對位框架：偵測到裁切，偵測失敗跳警告問重拍或直接上傳
  - marker 放背景板左上角，偵測後以 marker 為錨點做 crop（不變形）
  - 裁切參數（CROP_OFFSET_X/Y、CROP_W/H_RATIO）等物理設定後調整
- photos 表新增 `angle` + `session_date` 欄位

### 展示頁（plant-profile）
- plant.html：大圖瀏覽、縮略圖帶、生長日記時間軸、重量折線圖
- 雙角度顯示：同一天有正面 + 細節 → 左上角出現「🔄 細節角度」切換按鈕
- 時間軸有兩角度的日期顯示「正+細」金色標示
- 全局導航列（兩排）：所有頁面統一
- 縮時影片：**MP4 格式**（Web Codecs H.264，iPhone 原生播放）
  - 有 watermark：rễ , thân , lá studio + 根莖葉工作室
  - 支援下載 + 上傳到雲端（videos 表）
  - 瀏覽器不支援時自動 fallback WebM

### AI 分析
- Gemini 1.5 Flash 自動分析每張正面照片
- 輸出：health_score、growth_score、beauty_score（0-100）+ 繁體中文摘要
- 結果存 ai_analysis 表，plant.html 自動顯示

---

## 📋 計畫中（優先順序）

### 🔴 近期要做

**1. AI Prompt 精緻化**
現在的 prompt 太籠統。要根據鹿角蕨實際評分標準改寫：
- 忽略褐色營養葉（正常現象），專注綠色孢子葉
- 白毛分佈與覆蓋度
- 挺立度
- 新生長點偵測（新臉、新手、側芽）
- 明確的扣分規則（焦邊、黃化面積）

**2. ArUco 物理校正**
- 印出 marker（chev.me，4x4 字典，ID=3，size=80mm）
- 貼到背景板左上角，留白邊 1cm
- 上傳測試照片，調整 upload.html 裡的 4 個 CROP 常數到裁切正確

**3. plants.html 植物展示牆**
- 做成畫廊風格，每株植物一張卡片
- 掃 QR 進去看個別 plant.html

### 🟡 中期

**4. 多圖連貫分析（這個本來就要做）**
這是預測生長的核心功能：
```
同株植物過去 7 天照片（全部正面）
    → 一次送 Gemini（支援大 context window）
    → 輸出：生長動能、趨勢方向、預測下週狀態
    → 存 ai_analysis 表（新增 period_days 欄位）
```
觸發時機：每累積 7 張正面照片自動觸發一次，或手動在後台觸發

**5. 縮時影片選圖 UI**
現在後台要一張張點「改為縮時」，改成 checkbox 批次選取

**6. 重量記錄協議整合**
三個重量點：
- A 點：澆水後 5 分鐘（最重，base）
- B 點：每日早晨（追蹤日常蒸散）
- C 點：下次澆水前（最輕，自動識別）
→ 在上傳頁標記是哪個點，在折線圖上用不同顏色顯示

### 🟢 長期

**7. 生長預測圖**
重量時間序列 + AI 生長分 → 簡單趨勢線預測

**8. 客人 QR 掃描體驗優化**
客人買植物後掃 QR，進入個人化植物頁面

---

## 資料收集協議

**每次拍照記錄（每天最多一次）：**
- 正面照（必填）
- 細節照（選填，同一 session_date）
- 重量（g）
- 備註（澆水、換板、異常等）

**植物專攻：鹿角蕨（Platycerium）**
- 臉（營養葉）：褐色正常，不計入健康扣分
- 手（孢子葉）：主要評估對象，看白毛、挺立度、新展開
- 胞（孢子囊群）：4 階段追蹤
- 側芽：記錄出現與發展

---

## 環境變數

| 變數 | 位置 | 說明 |
|------|------|------|
| `GEMINI_API_KEY` | Vercel plant-pos | Google AI Studio 取得，免費 |

---

## 技術備忘

- **ArUco 不變形原理**：只做 crop，不做 perspective warp。腳架+背景板固定，marker 在背景板上，偏差只有植物擺放位置差幾公分 → affine 範圍內 → 不拉伸
- **MP4 vs WebM**：Web Codecs API（VideoEncoder + mp4-muxer）輸出真正的 H.264 MP4，iPhone Safari 原生播放。舊瀏覽器 fallback MediaRecorder WebM
- **AI 分析只觸發正面照**：細節照不分析，避免重複計分同一天
- **多圖分析等資料量**：至少 30 張同株正面照才有意義的趨勢
