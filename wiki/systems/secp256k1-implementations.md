---
type: system
title: secp256k1 簽章實作（secp256k1 ECDSA Implementations）
category: protocol
vendor: Bitcoin Core（libsecp256k1）等
updated: 2026-11-16
---

# secp256k1 簽章實作（secp256k1 ECDSA Implementations）

> secp256k1 是 Bitcoin 與 Ethereum 使用的橢圓曲線，libsecp256k1 是主導的函式庫。這個生態幾乎全面採用 RFC 6979 確定性 ECDSA。

## 簡介
曲線為 y² = x³ + 7，定義在 F_p 上，群階 n 為質數（[RFC 6979 論文](../papers/2026-prosianoi-rfc6979-deterministic-ecdsa.md) p.2–3）。在 RFC 6979 論文稽核的 31 個實作中，有 18 個函式庫、7 個錢包與節點、6 個硬體錢包（p.15）。

## 攻擊面
- nonce 產生方式：隨機、確定性或 hedged，見 [ECDSA nonce 安全](../concepts/ecdsa-nonce-security.md)。
- 是否符合 bits2octets 規範（訊息雜湊是否先 mod n）。
- 重試（step H.3）與 low-R grinding 的邏輯；grinding 會多出故障注入的攻擊面，以及兩倍的 Fx 計算（p.17）。
- 罕見分支的測試覆蓋率（k 不在 [1, n−1] 內、h ≥ n）。

## 稽核發現（RFC 6979 論文，2026-10 快照）
| 實作 | 發現 | 嚴重性 |
|---|---|---|
| 31 個中的 28 個 | 預設使用確定性簽章，不加額外熵 | 依賴 A* |
| libsecp256k1 < 0.2.0 | 訊息雜湊未 mod n（0.2.0 已修正） | 相容性問題 |
| NBitcoin、dcrd、btcec v2（影響 LND、btcd、go-ethereum 的非 cgo 版本） | 沿用未化簡的原始雜湊 | 相容性 bug；只有 h ≥ n 時才觀察得到 |
| go-ethereum | cgo 版與非 cgo 版在 h ≥ n 時簽章不同 | 相容性問題 |
| Ledger SDK | bits2int(h) = n 時未化簡；未拒絕 k = 0 | 機率 2^−256 |
| Bitcoin Core、libwally、rust-secp256k1、Jade、Coldcard | low-R grinding | 已證明只需 A*；偏差可忽略 |
| 全部實作 | 沒有測試 h ≥ n 分支 | 測試缺口 |
| pythonecdsa、LDK、BitBox02 | 例外：預設隨機、hedged 或 anti-klepto | — |

## 防禦機制
| 防禦 | 對抗的問題 | 部署狀況 |
|---|---|---|
| Hedged 簽章 API | 消除對 A* 的依賴（隨機數良好時） | 18 個函式庫中有 9 個提供，但預設都不啟用 |
| RFC 6979 §3.6 獨立 nonce 金鑰 | 完全不需要 A* | 樣本中沒有實作採用；與既有金鑰不相容 |
| 附錄 B 的 h ≥ n 測試向量 | 測試缺口 | 論文提供（p.19） |

## 相關 CVE
目前沒有。論文回報的都是相容性問題與機率可忽略的偏差，沒有 CVE。

## 相關論文
- [RFC 6979 確定性 ECDSA](../papers/2026-prosianoi-rfc6979-deterministic-ecdsa.md)
