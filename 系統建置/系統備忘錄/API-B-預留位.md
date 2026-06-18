# API B 預留位（S 系統）

## 說明
S 系統設定頁內有「API B — 預留」的位置，目前未設定任何服務。
程式碼中 `s-chat.js` 只接 Gemini（API A），API B 的邏輯尚未實作。

## 考慮選項
- Claude API（Anthropic）作為第二 AI 引擎
- 或其他服務

## 現狀
UI 上已有預留入口，但後端 s-chat.js 沒有對應的分支邏輯。
實作前需先決定服務，再在 plant-s-api 新增 API 端點。
