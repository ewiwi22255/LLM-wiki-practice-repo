---
type: concept
title: 交通方式偵測（Transport Mode Detection）
aliases: [TMD, transportation mode recognition]
parent: [zero-permission-sensor-leakage]
related: [colocation-inference, location-privacy]
paper_count: 1
updated: 2026-11-16
---

# 交通方式偵測（Transport Mode Detection）

> 從手機感測器資料判斷使用者目前是步行、開車、搭公車還是搭火車。這既是一種實用功能，也是一種隱私推論。

## 定義
交通方式偵測（TMD）一般被視為多類別分類問題。從隱私的角度來看，它讓 App 在沒有位置權限的情況下，也能掌握使用者的移動行為。

## 運作原理／子類型
- **多感測器機器學習**：結合加速度計、陀螺儀、磁力計、氣壓計，使用 LSTM、TCN、CNN 等模型區分 7–13 種交通方式（[列車磁場論文](../papers/2026-czybik-train-magnetometer-privacy.md) p.7、p.12）。
- **單一物理特徵**：只用磁力計偵測 16.7 Hz 的鐵路牽引頻率，做「是否搭電車」的二元判斷（列車磁場論文 p.5）。

既有方法在「train」類別上的成績，依列車磁場論文表 4（p.7）；各研究使用的資料集不同，只能當參考：

| 研究 | 資料集 | F1 | MCC |
|---|---|---|---|
| Hemminki et al.（2013） | 自有 | 0.70 | 0.68 |
| Iskanderov & Guvensan（2020） | HTC | 0.83 | 0.79 |
| Wang & Jiang（2022） | HTC | 0.79–0.83 | 0.77–0.82 |
| Skretting et al.（2025） | NOR-TMD | 0.93 | 0.93 |
| 列車磁場論文（2026，加多數決） | 自有 | 0.98 | 0.93 |

## 發展脈絡
- 2013：Hemminki et al. 首次只用加速度計做 TMD（列車磁場論文 p.12）。
- 2020–2025：深度學習與多感測器融合成為主流。
- 2026：[列車磁場論文](../papers/2026-czybik-train-magnetometer-privacy.md)證明單一頻譜特徵就能達到同等水準，而且只需要 60 s 資料（MCC 0.91）。

## 相關論文
| 論文 | 年份 | 類型 | 在此概念上的貢獻 |
|---|---|---|---|
| [列車磁場隱私洩漏](../papers/2026-czybik-train-magnetometer-privacy.md) | 2026 | attack | 用 16.7 Hz 磁場做電車偵測，F1 0.98 / MCC 0.93 |

## 開放問題
- 既有多感測器模型難以區分火車與汽車、公車等平穩的交通方式；16.7 Hz 指標可以當輔助特徵補上（列車磁場論文 p.8）。
- 同一個訊號既能用來追蹤使用者，也能提供有用的功能，要怎麼負責任地部署（列車磁場論文 p.12）。

## 相關概念
- [免權限感測器洩漏](zero-permission-sensor-leakage.md)
- [同行推論](colocation-inference.md)：列車磁場論文把 TMD 當成兩階段攻擊的第一階段，先篩出正在搭電車的受害者
- [位置隱私](location-privacy.md)
