# Supabase 備份 — GitHub Actions

## 目標
每週自動把 Supabase 所有 table 匯出成 JSON，存到 private GitHub repo，保留最近 30 份。

## 需要準備
- [ ] 建立 private GitHub repo（建議命名：`plant-backup`）
- [ ] 確認 Supabase table 清單
- [ ] 由 Claude 撰寫 GitHub Actions workflow

## 預計方式
1. GitHub Actions 排程觸發（每週一次）
2. 呼叫 Supabase REST API 取出所有 table 資料
3. 存成 `backup-YYYY-MM-DD.json`
4. commit 到 plant-backup repo
5. 超過 30 份自動刪最舊

## 狀態
等待：建立 plant-backup repo 後由 Claude 實作
