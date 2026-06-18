# plant-s-api 推到 GitHub

## 問題
plant-s-api 目前只有本機 git，沒有 GitHub remote。
本機損壞就沒有程式碼備份。

## 做法
1. 在 GitHub 新建 private repo `plant-s-api`
2. `git remote add origin <url>`
3. `git push -u origin main`
4. 之後每次改動同步推上去

## 狀態
待辦：晚上處理 S 系統時一起做
