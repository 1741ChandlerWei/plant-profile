# API 拆分後系統驗證

## 背景
2026-06-18 將 s-chat、save-ai-analysis 從 plant-pos 移至 plant-s-api。
需確認各頁面呼叫新 URL 正常，沒有功能被悄悄打壞。

## 驗證清單
- [ ] plant-pos deploy 成功（確認 Vercel 顯示 Ready）
- [ ] upload.html 正常載入，上傳功能正常
- [ ] s-system.html AI 對話可以送出（設 Gemini key 後測）
- [ ] plant.html 履歷頁 AI 問答可以送出
- [ ] plant-profile admin.html 的 update-rehab-status 仍正常

## 狀態
待辦
