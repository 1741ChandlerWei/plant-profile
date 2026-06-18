# B2 File Versioning 開啟

## 目標
防止誤刪 B2 照片無法救回（上次清空整個 bucket 的教訓）。

## 做法
在 Backblaze B2 console → bucket 設定 → 開啟 File Versioning
刪除的檔案保留舊版本一段時間，可手動還原。

## 費用
只計算保留版本的儲存費，一般用量影響極小。

## 狀態
待辦：進 Backblaze 後台開啟
