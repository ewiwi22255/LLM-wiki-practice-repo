---
type: system
title: iOS
category: mobile
vendor: Apple
updated: 2026-11-16
---

# iOS

> Apple 的行動作業系統。在本 wiki 中，主要關注它的感測器存取模型：與 Android 一樣，讀取慣性感測器不需要使用者權限。

## 攻擊面
- **免權限感測器**：iOS 目前允許讀取磁力計，最高取樣率 100 Hz（[列車磁場論文](../papers/2026-czybik-train-magnetometer-privacy.md) p.3），足以解析 16.7 Hz 的鐵路牽引訊號。
- 列車磁場論文指出，兩個攻擊在 Android 與 iOS 上都能隱蔽地執行（p.1）。

## 已知攻擊
| 攻擊／論文 | 年份 | 手法概念 | 影響 |
|---|---|---|---|
| [列車磁場隱私洩漏](../papers/2026-czybik-train-magnetometer-privacy.md) | 2026 | [免權限感測器洩漏](../concepts/zero-permission-sensor-leakage.md)（磁力計） | 偵測是否搭電車、同行推論 |

## 防禦機制
| 防禦 | 對抗的攻擊 | 部署狀況 |
|---|---|---|
| 取樣率上限 100 Hz | 高頻感測器攻擊 | 已部署，但無法阻擋 16.7 Hz 的擷取 |
| 磁力計權限，或把取樣率限制在 35 Hz 以下 | 頻譜分析類攻擊 | 列車磁場論文的提議（p.12） |

## 相關 CVE
目前沒有。

## 相關論文
- [列車磁場隱私洩漏](../papers/2026-czybik-train-magnetometer-privacy.md)
