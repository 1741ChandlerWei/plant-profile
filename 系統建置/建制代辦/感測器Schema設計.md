# 感測器 Schema 設計

## 目標
在 Supabase 設計資料表，存放溫濕度感測器資料，並與植物紀錄連結。

## 待釐清
- [ ] 使用什麼裝置？（ESP32、Raspberry Pi 等）
- [ ] 感測器是全場域一個，還是每區/每株各一個？
- [ ] 資料只存歷史查詢，還是需要即時警報？
- [ ] 警報條件是什麼？（溫度超過幾度、濕度低於幾%）

## 初步 Table 設計方向
```
sensor_logs
- id
- sensor_id（感測器編號）
- zone（對應的架區，如 A1、B2）
- temperature_c
- humidity_pct
- recorded_at
```

## 狀態
規劃中，等裝置和需求確認後設計
