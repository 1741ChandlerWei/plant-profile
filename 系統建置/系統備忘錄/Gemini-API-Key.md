# Gemini API Key

## 問題
plant-pos 和 plant-s-api 都沒有設 GEMINI_API_KEY，以下功能全部無法使用：

| 功能 | 位置 |
|------|------|
| 上傳照片自動健康分析 | upload-photo.js |
| 磅秤批次 OCR 讀重量 | ocr-weight.js |
| S 系統 AI 對話 | s-chat.js（plant-s-api） |
| 植物履歷頁 AI 問答 | s-chat.js（plant-s-api） |
| 儲存 AI 評分 | save-ai-analysis.js（plant-s-api） |

## 待辦
- [ ] 申請 Gemini API Key（Google AI Studio）
- [ ] 在 Vercel plant-pos 設：GEMINI_API_KEY
- [ ] 在 Vercel plant-s-api 設：GEMINI_API_KEY
