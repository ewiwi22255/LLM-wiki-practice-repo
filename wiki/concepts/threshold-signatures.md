---
type: concept
title: 門檻簽章（Threshold Signatures）
aliases: [t-of-n signatures, multi-signatures, 多重簽章, 門限簽章]
parent: [digital-signatures]
related: [information-theoretic-security]
paper_count: 1
updated: 2026-11-16
---

# 門檻簽章（Threshold Signatures）

> 把簽章能力分散給 n 個受託者（trustee），至少要 t 個合作才能產生有效簽章；少於 t 個就無法簽章。多重簽章是 t = n 的特例。

## 定義
門檻簽章源自 Shamir 秘密分享：私鑰被拆成 n 份，任意 t 份可以重建，t−1 份則完全不透露資訊（[IT 門檻簽章論文](../papers/2026-wright-it-threshold-dv-signatures.md) p.3–4）。它能強制多方授權、消除單點故障，在部分節點被入侵時保持完整性，在部分節點離線時保持可用性（p.1）。

## 運作原理／設計面向
- **有 dealer 或無 dealer**：可信 dealer 負責產生並分發分享值，但 dealer 本身是單點攻擊目標。無 dealer 的做法是讓每個受託者各自當 dealer，產生並分發自己的分享值（IT 門檻簽章論文 p.7）。
- **受託者匿名性**：多重簽章通常會暴露參與者身分；門檻簽章要透過可信組合者（trusted combiner）才能隱藏是哪 t 個人簽的（p.4）。
- **權限分級**：給重要的參與者多幾份分享值（p.3）。
- **安全性質**：抗偽造、門檻安全；在資訊理論版本中還要加上可轉移性與不可否認性（p.4–5）。

## 方案光譜（依 IT 門檻簽章論文的相關研究，p.2、p.12–13）
| 方案 | 安全基礎 | 備註 |
|---|---|---|
| Threshold ML-DSA（Celi et al.，USENIX Security '26） | 格（後量子） | 需要可信第三方；超過 6 個受託者時通訊成本快速增加 |
| Threshold EdDSA（RAID '22）、橢圓曲線門檻簽章 | 離散對數 | 用於區塊鏈與 DeFi |
| Compact multi-signatures（ASIACRYPT 2018） | 配對 | 用於區塊鏈 |
| Threshold signatures from OWF（TCC 2025）、Coalition hash-based | 單向函數或雜湊 | |
| **IT 門檻指定驗證者簽章**（2026） | 無計算假設 | 指定驗證者、一次性、金鑰長 |

## 相關論文
| 論文 | 年份 | 類型 | 在此概念上的貢獻 |
|---|---|---|---|
| [IT 門檻簽章](../papers/2026-wright-it-threshold-dv-signatures.md) | 2026 | defense | 第一個在標準門檻語法下的資訊理論門檻簽章，含 dealer 與無 dealer 版本 |

## 開放問題
- 減少無 dealer 版本中，每個受託者需要產生的函數數量（IT 門檻簽章論文 p.11）。
- 驗證者人數多或事先未知時，資訊理論方案不如計算安全方案實用（p.13）。

## 相關概念
- [數位簽章](digital-signatures.md)
- [資訊理論安全](information-theoretic-security.md)
