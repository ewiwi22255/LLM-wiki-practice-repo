---
type: system
title: Android
category: mobile
vendor: Google
updated: 2026-11-16
---

# Android

> Google 的行動作業系統。在本 wiki 中，主要關注它的感測器存取模型：慣性感測器讀取不需要執行期權限。

## 攻擊面
- **免權限感測器**：截至 2026 年 4 月，讀取磁力計不需要明確的執行期權限（[列車磁場論文](../papers/2026-czybik-train-magnetometer-privacy.md) p.3）。
- **取樣率**：自 API level 31 起，所有慣性感測器的取樣率上限為 200 Hz，已超過大多數市售磁力計硬體的能力（p.3），足以解析 16.7 Hz 的鐵路牽引訊號。
- **第三方函式庫**：App 內嵌的廣告或分析函式庫可以在背景記錄並上傳感測器資料（p.4）。

## 已知攻擊
| 攻擊／論文 | 年份 | 手法概念 | 影響 |
|---|---|---|---|
| [列車磁場隱私洩漏](../papers/2026-czybik-train-magnetometer-privacy.md) | 2026 | [免權限感測器洩漏](../concepts/zero-permission-sensor-leakage.md)（磁力計） | 偵測是否搭電車（MCC 0.93）、同行推論（MCC 0.79） |

## 防禦機制
| 防禦 | 對抗的攻擊 | 部署狀況 |
|---|---|---|
| 慣性感測器取樣率上限 200 Hz（API 31+） | 高頻感測器攻擊 | 已部署，但無法阻擋 16.7 Hz 的擷取 |
| 磁力計專屬權限 | 免權限感測器攻擊 | 列車磁場論文的提議（p.12） |
| 依權限等級決定取樣率（< 35 Hz 免權限） | 頻譜分析類攻擊 | 列車磁場論文的提議（p.12） |

## 相關 CVE
目前沒有。

## 相關論文
- [列車磁場隱私洩漏](../papers/2026-czybik-train-magnetometer-privacy.md)
