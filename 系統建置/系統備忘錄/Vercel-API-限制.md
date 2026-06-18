# Vercel API 限制

## 問題
Vercel Hobby plan 最多 12 個 Serverless Functions。

## 現狀（2026-06-18）
plant-pos 目前有 10 個 API，剩 2 格空間：

| API | 用途 |
|-----|------|
| upload-photo | 上傳照片到 B2 + 存 DB |
| ocr-weight | 磅秤照片 OCR（Gemini） |
| photo | B2 照片代理 |
| complete-pending | 補登完成 |
| patch-record | 修改已有記錄 |
| data | 通用 Supabase 代理 |
| login | 登入驗證 |
| update-alert | 更新植物 zone/警示 |
| update-rehab-status | 更新 rehab 狀態（plant-profile 呼叫） |
| send-payment-email | 訂單付款 email |

## 解法
s-chat + save-ai-analysis 已拆到獨立專案 `plant-s-api`（2026-06-18 完成）。
