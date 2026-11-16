---
type: concept
title: 免權限感測器洩漏（Zero-Permission Sensor Leakage）
aliases: [permissionless sensor side channel, 慣性感測器隱私洩漏, motion sensor leakage]
parent: [side-channel-attacks]
related: [location-privacy, transport-mode-detection, colocation-inference]
paper_count: 1
updated: 2026-11-16
---

# 免權限感測器洩漏（Zero-Permission Sensor Leakage）

> 手機的慣性與環境感測器（加速度計、陀螺儀、磁力計）讀取時不需要使用者授權，任何 App 都能在背景持續讀取，因此成為推論敏感資訊的管道。

## 定義
Android 與 iOS 都沒有把慣性感測器放在明確權限之後，因為螢幕旋轉、計步、手勢辨識、導航、防手震等功能都需要它們（[列車磁場論文](../papers/2026-czybik-train-magnetometer-privacy.md) p.1）。結果是 App 可以在使用者不知情的情況下取得這些讀數。

## 運作原理／子類型
已知的洩漏方向（依[列車磁場論文](../papers/2026-czybik-train-magnetometer-privacy.md)的相關研究整理，p.1、p.13–14）：

| 感測器 | 可推論的資訊 | 代表研究 |
|---|---|---|
| 加速度計 | 搭乘地鐵的路線 | Hua et al.（IEEE TIFS 2017） |
| 加速度計／陀螺儀等 | 路徑與位置 | Narain et al.（IEEE S&P 2016） |
| 加速度計 | 附近鍵盤輸入、密碼 | (sp)iPhone（CCS 2011）、ACCessory（HotMobile 2012） |
| 陀螺儀／加速度計 | 語音 | Gyrophone（USENIX Security 2014）、AccEar（IEEE S&P 2022） |
| 磁力計 | 手指觸控 | iSTELAN（PoPETs 2023） |
| 磁力計 | 是否搭電車、同車者 | [列車磁場論文](../papers/2026-czybik-train-magnetometer-privacy.md)（CCS 2026） |

**磁力計的特殊性**：過去普遍認為它主要只量到地球靜態磁場、對攻擊者用處有限；但交流供電的基礎設施（例如 16.7 Hz 的鐵路架空線）會產生帶有特徵頻率的磁場，讓訊號很容易從頻譜中分離出來（列車磁場論文 p.1–3）。

## 防禦機制
- **取樣率上限**：Android 自 API 31 起限制為 200 Hz，iOS 為 100 Hz，但這不足以阻擋 16.7 Hz 的擷取（p.3）。
- **提議**：讓磁力計需要專屬權限；把取樣率限制在 35 Hz 以下；或依權限等級決定可用的取樣率（p.12）。

## 相關論文
| 論文 | 年份 | 類型 | 在此概念上的貢獻 |
|---|---|---|---|
| [列車磁場隱私洩漏](../papers/2026-czybik-train-magnetometer-privacy.md) | 2026 | attack | 首次把磁力計擷取到的鐵路牽引電流磁場，當作攻擊面 |

## 開放問題
- 提高取樣率限制會影響良性功能（計步、手勢偵測），兩者的取捨缺少量化研究（列車磁場論文 p.12）。
- 未來感測器硬體提升後，50 Hz 等更高頻的基礎設施訊號也可能被擷取（p.12）。
- 其他交流供電的基礎設施（例如電車線、高壓電纜、工業設備）是否也會造成類似洩漏，尚未研究（推論）。

## 相關概念
- [旁通道攻擊](side-channel-attacks.md)（上位概念，推論）
- [位置隱私](location-privacy.md)
- [交通方式偵測](transport-mode-detection.md)
- [同行推論](colocation-inference.md)
- [路徑推論攻擊](route-inference-attack.md)：同樣以非位置訊號推論行程，載體是車輛（推論）

## 相關系統
- [Android](../systems/android.md)、[iOS](../systems/ios.md)
