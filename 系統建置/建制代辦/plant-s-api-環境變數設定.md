# plant-s-api 環境變數設定

## 問題
plant-s-api 是新專案，Vercel 上還沒有任何環境變數，s-chat 和 save-ai-analysis 無法正常運作。

## 需要設定的變數
| 變數名 | 說明 |
|--------|------|
| INTERNAL_KEY | 跟 plant-pos 相同，用於 API 驗證 |
| SUPABASE_SERVICE_KEY | 跟 plant-pos 相同，用於存取 DB |
| GEMINI_API_KEY | Gemini AI Key（另一個代辦） |

## 步驟
- [ ] Vercel → plant-s-api → Settings → Environment Variables
- [ ] 逐一設入以上三個變數
- [ ] Redeploy

## 狀態
待辦
