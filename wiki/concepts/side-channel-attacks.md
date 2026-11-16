---
type: concept
title: 旁通道攻擊（Side-Channel Attacks）
aliases: [side channel, 側通道]
parent: []
related: [spectre, zero-permission-sensor-leakage, ecdsa-nonce-security]
paper_count: 3
updated: 2026-11-16
---

# 旁通道攻擊（Side-Channel Attacks）

> 不攻擊演算法的邏輯輸出，而是從實作或環境產生的副作用（時間、功耗、電磁、微架構狀態、物理訊號）推論秘密。

## 定義
系統在正常運作時產生的可觀察副作用，與秘密資料有關聯，攻擊者因此可以推論秘密。本頁是上位概念頁，彙整 wiki 中各種旁通道相關的子概念。

## 子類型（依目前收錄論文）
| 子類型 | 洩漏載體 | wiki 頁面 | 論文 |
|---|---|---|---|
| 微架構／推測執行 | 推測執行時的快取與記憶體存取 | [Spectre](spectre.md) | [VellSSLH](../papers/2026-priya-vellsslh.md)（防禦） |
| 密碼學 nonce 外洩 | 功耗、時間造成 nonce 部分位元外洩 | [ECDSA nonce 安全](ecdsa-nonce-security.md) | [RFC 6979 確定性 ECDSA](../papers/2026-prosianoi-rfc6979-deterministic-ecdsa.md)（脈絡討論） |
| 環境物理訊號／感測器 | 手機磁力計量到的牽引電流磁場 | [免權限感測器洩漏](zero-permission-sensor-leakage.md) | [列車磁場隱私洩漏](../papers/2026-czybik-train-magnetometer-privacy.md)（攻擊） |

註：把列車磁場論文歸為旁通道是 wiki 的分類（推論），論文本身定位為「隱私洩漏」。

## 防禦思路
- **常數時間程式設計**，以及它在推測執行下的延伸 [SCT](speculative-constant-time.md)。
- **編譯器自動強化**，例如 [SLH](speculative-load-hardening.md)。
- **協定層移除對秘密隨機性的依賴**，例如 nonce 金鑰與簽章私鑰分離（RFC 6979 論文 p.14–15）。
- **作業系統層的存取控制與取樣率上限**（列車磁場論文 p.12）。

## 相關論文
| 論文 | 年份 | 類型 | 在此概念上的貢獻 |
|---|---|---|---|
| [VellSSLH](../papers/2026-priya-vellsslh.md) | 2026 | defense | 經形式化驗證的選擇性 SLH，防禦 Spectre v1 |
| [RFC 6979 確定性 ECDSA](../papers/2026-prosianoi-rfc6979-deterministic-ecdsa.md) | 2026 | analysis | 把一位元 nonce 外洩攻擊的資料門檻放進脈絡（2^40–2^47 個簽章） |
| [列車磁場隱私洩漏](../papers/2026-czybik-train-magnetometer-privacy.md) | 2026 | attack | 以環境磁場推論行為 |

## 開放問題
- 目前收錄論文中的旁通道研究，分屬密碼學、系統、行動隱私三個社群，評估方式與用語差異很大（推論）。

## 相關概念
- [Spectre](spectre.md)
- [ECDSA nonce 安全](ecdsa-nonce-security.md)
- [免權限感測器洩漏](zero-permission-sensor-leakage.md)
