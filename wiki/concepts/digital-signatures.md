---
type: concept
title: 數位簽章（Digital Signatures）
aliases: [signature schemes, 電子簽章]
parent: []
related: [ecdsa-nonce-security, threshold-signatures, information-theoretic-security]
paper_count: 2
updated: 2026-11-16
---

# 數位簽章（Digital Signatures）

> 讓接收者驗證訊息確實來自宣稱的簽署者、且沒有被竄改的密碼學原語。標準安全目標是選擇訊息攻擊下的存在性不可偽造（EUF-CMA）。

## 定義
數位簽章用於軟體更新、支付授權、安全關鍵的控制訊息等（[IT 門檻簽章論文](../papers/2026-wright-it-threshold-dv-signatures.md) p.1）。依安全基礎的不同，可分成幾類。

## 子類型
| 類型 | 安全基礎 | 例子 | wiki 頁面／論文 |
|---|---|---|---|
| 離散對數類 | DLP 等計算假設 | ECDSA、EdDSA、BIP-340 Schnorr | [ECDSA nonce 安全](ecdsa-nonce-security.md) |
| 格類（後量子） | 格問題 | ML-DSA、Threshold ML-DSA | [IT 門檻簽章論文](../papers/2026-wright-it-threshold-dv-signatures.md)的比較基準 |
| 雜湊類 | 雜湊函數 | SPHINCS+ | IT 門檻簽章論文 p.3 |
| 資訊理論類 | 不依賴計算假設 | USS、本文的 IT 門檻簽章 | [資訊理論安全](information-theoretic-security.md) |

依簽署者人數再分：單一簽署者、多重簽章（n-of-n）、[門檻簽章](threshold-signatures.md)（t-of-n）。

## 相關論文
| 論文 | 年份 | 類型 | 在此概念上的貢獻 |
|---|---|---|---|
| [RFC 6979 確定性 ECDSA](../papers/2026-prosianoi-rfc6979-deterministic-ecdsa.md) | 2026 | analysis | 確定性 ECDSA 依賴未證明的假設 A*；稽核 31 個實作 |
| [IT 門檻簽章](../papers/2026-wright-it-threshold-dv-signatures.md) | 2026 | defense | 不依賴計算假設的門檻指定驗證者簽章 |

## 跨論文觀察：安全基礎的兩個方向（推論）
- **RFC 6979 論文**揭露了「實務上部署最廣的方案，安全性其實建立在一個沒人證明過的假設上」，並建議把 nonce 金鑰與簽章私鑰分開，以消除這個假設（p.14–15、p.18）。
- **IT 門檻簽章論文**則完全不依賴計算假設，但必須付出代價：只有預先登記的驗證者能驗證（不具公開可驗證性）、金鑰很長、只能使用一次（p.13）。
- 兩者合起來呈現一個光譜：一端是可公開驗證、但依賴（可能未證明的）計算假設；另一端是無條件安全、但部署模型受限。

## 開放問題
- 用標準模型證明或推翻 HMAC_DRBG 滿足 A*（RFC 6979 論文 p.18）。
- 讓資訊理論簽章在驗證者人數多時仍然實用（IT 門檻簽章論文 p.13）。

## 相關概念
- [ECDSA nonce 安全](ecdsa-nonce-security.md)
- [門檻簽章](threshold-signatures.md)
- [資訊理論安全](information-theoretic-security.md)
