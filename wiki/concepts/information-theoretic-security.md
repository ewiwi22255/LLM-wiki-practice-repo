---
type: concept
title: 資訊理論安全（Information-Theoretic Security）
aliases: [unconditional security, 無條件安全, IT security]
parent: []
related: [threshold-signatures, digital-signatures]
paper_count: 1
updated: 2026-11-16
---

# 資訊理論安全（Information-Theoretic Security）

> 安全性不依賴「某個數學問題很難算」，即使面對算力無上限的攻擊者（包括量子電腦）也成立。

## 定義
計算安全的方案（包括以格為基礎的後量子方案）依賴困難度假設，這些假設可能隨著演算法與硬體進步而失效；資訊理論安全則被視為密碼學安全的「黃金標準」（[IT 門檻簽章論文](../papers/2026-wright-it-threshold-dv-signatures.md) p.1）。

## 運作原理／代表構造
- **一次性密碼本**：完美保密要求金鑰至少和訊息一樣長（IT 門檻簽章論文 p.12）。
- **Wegman–Carter 通用雜湊 MAC**：以 ε-ASU2 雜湊族產生短標籤（p.3）。
- **Shamir 秘密分享**：少於 t 份不透露任何資訊（p.3）。
- **USS（Unconditionally Secure Signature）**：以通用雜湊建構無條件安全簽章，已在 QKD 網路上實作（p.2–3）。

## 典型代價
- 需要預先發放秘密的驗證素材，因此只能由事先登記的接收者驗證（p.1）。
- 金鑰素材通常只能用一次（或有限次數）（p.1、p.13）。
- 金鑰與簽章長度大（p.12–13）。
- 這些假設比較適合封閉生態，例如裝置群、工業控制、無人機群、支付基礎設施（p.1）。

## 相關論文
| 論文 | 年份 | 類型 | 在此概念上的貢獻 |
|---|---|---|---|
| [IT 門檻簽章](../papers/2026-wright-it-threshold-dv-signatures.md) | 2026 | defense | 把資訊理論簽章擴展到門檻與多重簽章設定 |

## 開放問題
- 降低金鑰長度與一次性限制，讓資訊理論方案能在開放系統中使用（推論）。
- 秘密通道的建立方式（例如 QKD）本身的成本與可得性（推論）。

## 相關概念
- [門檻簽章](threshold-signatures.md)
- [數位簽章](digital-signatures.md)
