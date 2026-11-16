---
type: concept
title: 路徑推論攻擊（Route Inference Attack）
aliases: [trajectory recovery, path inference, 軌跡還原]
parent: [location-privacy]
related: [zero-permission-sensor-leakage]
paper_count: 1
updated: 2026-11-16
---

# 路徑推論攻擊（Route Inference Attack）

> 不使用 GPS，而是用車速、方向盤角、煞車與踏板等車輛訊號，加上道路圖，還原車輛的行駛路徑或每秒位置。

## 定義
攻擊者取得已刪除位置欄位的駕駛紀錄（例如保險公司收集的 OBD 資料，或開放資料集），透過運動模型與道路網路約束，重建車輛去過哪裡。輸出可能是排序後的目的地、候選路徑，或沿途的位置估計（[CANTraj](../papers/2026-wang-cantraj.md) p.1）。

## 運作原理／子類型
依 [CANTraj](../papers/2026-wang-cantraj.md) 的分類（p.1–2）：

| 子類型 | 利用的訊號 | 代表方法 |
|---|---|---|
| 速度型 | 停車點、行駛距離、可行轉彎 | Dewri et al.（2013）、Elastic Pathing（2014）、Zhou et al. |
| 事件型 | 煞車、油門踏板、轉彎事件 | BBLT（2020）、CAN-Trace（2025）、Waltereit et al. |
| 方向盤型 | 方向盤角與道路曲率，或運動模型加地圖匹配 | SPy（2020）、MacroTracking、OPD-II（2025） |

**解碼方式**：
- 無地圖的航位推算（dead reckoning）：5 分鐘內就漂移到數百公尺以上。
- 線上：粒子濾波器（particle filter）、地圖匹配。
- 離線：集束搜尋（beam search）、最佳優先搜尋。

## 發展脈絡
- 2013：Dewri et al. 用駕駛習慣資料推論目的地（CANTraj p.13）。
- 2014：Elastic Pathing 指出光靠速度就能追蹤（UbiComp）。
- 2020：SPy 用方向盤比對道路曲率（PoPETs）；BBLT 用煞車訊號（IPSN）。
- 2025：OPD-II（EuroS&P）、CAN-Trace（IEEE T-ITS）。
- 2026：[CANTraj](../papers/2026-wang-cantraj.md) 首次在大規模資料上，以統一協定與納入失敗的指標比較 6 種攻擊。

## 關鍵發現（截至目前收錄的論文）
- 道路圖讓誤差下降超過一個數量級；在已知訊號中，方向盤的貢獻最大（CANTraj p.6、p.9）。
- 離線搜尋比線上濾波更不依賴起始航向（CANTraj p.9）。
- 不同方法在不同片段失敗，逐片段挑選最佳方法可還原 98% 的路徑（CANTraj p.10）。
- 輕度量化幾乎無效（CANTraj p.11）。

## 相關論文
| 論文 | 年份 | 類型 | 在此概念上的貢獻 |
|---|---|---|---|
| [CANTraj](../papers/2026-wang-cantraj.md) | 2026 | measurement | 資料集與基準，統一比較 6 種攻擊，並分析訊號貢獻與失敗原因 |

## 開放問題
- 起點未知、不限區域的追蹤（CANTraj p.12）。
- 針對劣化訊號重新調整的適應性攻擊，以及保護機制的下游效用評估（CANTraj p.12）。
- 結果以北美道路為主；換到其他地區後，方法排名可能改變（CANTraj p.8、p.12）。
- 只有 MacroTracking 與 OPD-II 用原作者樣本驗證過重新實作；其餘四種沒有相同強度的證據（CANTraj p.12）。

## 相關概念
- [位置隱私](location-privacy.md)
- [免權限感測器洩漏](zero-permission-sensor-leakage.md)：同樣以非位置訊號推論行程，載體從車輛變成手機（推論）

## 相關系統
- [車載 CAN 匯流排](../systems/vehicle-can-bus.md)
