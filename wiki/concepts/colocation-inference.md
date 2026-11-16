---
type: concept
title: 同行推論（Colocation Inference）
aliases: [co-location detection, colocation prediction, 共處偵測]
parent: [zero-permission-sensor-leakage]
related: [location-privacy, transport-mode-detection]
paper_count: 1
updated: 2026-11-16
---

# 同行推論（Colocation Inference）

> 比較兩支裝置的感測器軌跡，判斷它們是否在同一個地點或同一輛交通工具上，進而揭露社交關係與行蹤。

## 定義
同一環境中的裝置會量到同步變化的環境訊號，例如磁場；比較這些軌跡的相似度，就能推論裝置是否在一起。這類研究最早多來自疫情時期的接觸追蹤，但同樣的能力也是隱私攻擊：它揭露誰和誰同行；若其中一條軌跡的位置已知，其他人的位置也會跟著曝光（[列車磁場論文](../papers/2026-czybik-train-magnetometer-privacy.md) p.4）。

## 運作原理／子類型
| 做法 | 相似度方法 | 代表研究 |
|---|---|---|
| 距離加門檻 | DDTW、DTW、Pearson 相關係數 | Nguyen et al.（IPIN 2017）、Jeong et al.（2019） |
| 學習式 | 卷積自編碼器、1D CNN | Kosasih et al.（2020–2021）、列車磁場論文（2026） |

**評估陷阱**：如果比較的軌跡長度不同，長度本身就是強烈線索，因為同行的裝置通常錄音時長相同。列車磁場論文固定比較等長軌跡，讓判斷只能依靠訊號形狀，因此重新實作的 Nguyen et al. 基準只拿到 MCC 0.09（p.8–9、p.11）。

## 發展脈絡
- 2017：Nguyen et al. 在倫敦大眾運輸上，用磁場強度加 DDTW 做接觸追蹤（列車磁場論文 p.12）。
- 2019：Jeong et al. 做室內同行偵測，使用 Pearson 相關係數。
- 2020–2021：Kosasih et al. 使用卷積自編碼器，F1 0.65–0.89，但資料量只有約 24 分鐘。
- 2026：[列車磁場論文](../papers/2026-czybik-train-magnetometer-privacy.md)先抽出 16.7 Hz 成分再交給 CNN，在數百趟真實列車上達到 MCC 0.79。

## 相關論文
| 論文 | 年份 | 類型 | 在此概念上的貢獻 |
|---|---|---|---|
| [列車磁場隱私洩漏](../papers/2026-czybik-train-magnetometer-privacy.md) | 2026 | attack | 抽出鐵路牽引頻率後做同行推論；CNN MCC 0.79，比 DTW 快 16 倍 |

## 開放問題
- 在 1:n 大規模比對時，配對數量呈二次方成長，偽陽性的控制還沒研究清楚（列車磁場論文 p.9）。
- 這項技術可以反過來用在車廂內的接觸追蹤，但需要隱私保護的部署方式（列車磁場論文 p.12）。

## 相關概念
- [免權限感測器洩漏](zero-permission-sensor-leakage.md)
- [交通方式偵測](transport-mode-detection.md)
- [位置隱私](location-privacy.md)
