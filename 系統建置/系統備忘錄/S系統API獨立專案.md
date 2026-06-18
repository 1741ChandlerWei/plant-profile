# S 系統 API 獨立專案

## 說明
s-system 的 AI 相關 API 已從 plant-pos 拆出，獨立成 `plant-s-api` 專案。

## 專案資訊
- Vercel 網址：https://plant-s-api.vercel.app
- 本機路徑：/Users/chendlerwei/plant-s-api
- 目前無 GitHub remote（待補）

## 包含的 API
| API | 用途 |
|-----|------|
| s-chat | Gemini AI 對話（s-system + plant 履歷頁用） |
| save-ai-analysis | 儲存 AI 評分結果到 DB |

## 呼叫方
- `s-system.html` → s-chat、save-ai-analysis
- `plant.html` → s-chat

## 待辦
- [ ] 在 Vercel plant-s-api 專案設環境變數：INTERNAL_KEY、SUPABASE_SERVICE_KEY、GEMINI_API_KEY
- [ ] 建 GitHub remote 並推上去
