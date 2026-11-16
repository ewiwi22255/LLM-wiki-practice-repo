---
type: paper
title: "Deterministic ECDSA on secp256k1: the RFC 6979 assumption, a counterexample, and the state of deployed implementations"
authors: [Ilia Prosianoi]
year: 2026
venue: arXiv
arxiv: "2611.18449"
doi:
url: https://arxiv.org/abs/2611.18449
source: raw/arxiv-2611.18449.pdf
contribution_type: analysis
concepts: [ecdsa-nonce-security, digital-signatures, side-channel-attacks]
systems: [secp256k1-implementations]
cves: []
ingested: 2026-11-16
updated: 2026-11-16
---

# secp256k1 上的確定性 ECDSA：RFC 6979 的安全假設、反例與已部署實作現況（Deterministic ECDSA on secp256k1: the RFC 6979 assumption, a counterexample, and the state of deployed implementations）

> 一句話摘要：RFC 6979 確定性 nonce 的安全性，其實依賴一個從未被證明的假設 A*。它既不是 HMAC 的 PRF 安全性，也不是輔助輸入 PRF（AI-PRF）。本文給出反例與雙向分離結果，並稽核 31 個 secp256k1 實作：28 個預設都使用確定性簽章，且沒有加入額外熵。

## 基本摘要

### 研究問題
ECDSA 的每次簽章 nonce k 只要重複、有偏差或部分外洩，就會洩漏簽章私鑰（p.1）。RFC 6979 以 HMAC_DRBG 從私鑰 x 與訊息雜湊 h 推導 k，移除了對執行期亂數產生器的依賴，已是 Bitcoin 與 Ethereum 生態的事實標準（p.1）。但 RFC 的安全論證並不正式：它的兩句話分別描述「HMAC 是 PRF」與「已知公鑰時 HMAC_DRBG 仍像 random oracle」兩種不同假設，作者指出兩者都不是真正需要的假設（p.1）。

### 方法
- **定義 A\***：在已知公鑰 Q = xG 時，用 Fx(·) 產生 nonce 的簽章紀錄，與用均勻隨機 nonce 的簽章紀錄不可區分（p.3）。
- **Theorem 3.1**：A* 足以讓確定性 ECDSA 與隨機化 ECDSA 一樣安全（p.3）。
- **反例（Theorem 4.5）**：F′(x, h) = P_Ext(xG)(h) 在標準假設下是 PRF，但一個簽章就能還原 x（p.4）。進一步把反例下推到 SHA-256 壓縮函數：在碰撞抗性前提下，存在一個保有 HMAC 證明所需 PRF 與 dual-PRF 性質的 f*，使未修改的 RFC 6979 只用一個簽章就被完全破解（Theorem 4.8，p.6）。
- **A* 與 AI-PRF 互不蘊含**（Section 5，p.7–14）：
  - A* 不蘊含 AI-PRF，條件是一位元隱藏數問題（hidden number problem, HNP）困難（p.7）。
  - AI-PRF 不蘊含 A*：植入碰撞後，兩個自選 digest 的簽章即可還原 x（Theorem 5.7）；在 decisional square Diffie–Hellman 假設下，兩則一般雜湊訊息的簽章即可還原 x（Theorem 5.9）（p.9–11）。
  - A* 是充分條件但非必要條件：違反 A* 的「排序位元」構造仍然不可偽造（Remark 5.12，p.12）。
- **可證明的部分**：把壓縮函數視為理想函數時，A* 可在提供隨機化 ECDSA 簽章 oracle 的條件下，緊密歸約到 DLP（Theorem 6.1，p.14）；若 nonce 金鑰與 x 獨立，就完全不需要 A*（Theorem 6.3，p.14）。
- **實作稽核**：審查 31 個 secp256k1 實作的 nonce 預設、bits2octets 合規性、重試與 grinding 邏輯，以及測試覆蓋率（Section 8，p.15–17）。
- 文中每個主張都標註來源：本文證明 [T]、文獻 [L]、原始碼檢視 [S]、估計或詮釋 [O]（p.2）。

### 主要貢獻
- 精確界定確定性 ECDSA 所需的假設 A*，並證明 AI-PRF 撐不起這個歸約（p.2）。
- 反例證明「PRF + DLP + 隨機化 ECDSA 安全」推不出 RFC 6979 安全（p.2）。
- A* 與 AI-PRF 的雙向分離，以及多種標準模型構造（p.2、p.13 Table 3）。
- 稽核 31 個實作，並為從未被測試的 h ≥ n 分支提供測試向量（p.2、p.19）。

### 結果
- RFC 6979 第一個 HMAC 區塊中的秘密尾端，與均勻分布的距離：secp256k1 為 2^−127.65、P-256 為 2^−32、brainpoolP256r1 約為 0.336（p.1、p.21 Table 5）。
- **nonce 預設**：31 個實作中有 28 個（90%）預設使用確定性簽章且不加額外熵；18 個函式庫中雖有 9 個提供 hedged API，但沒有一個預設使用；BIP-340 的 26 個實作中，15 個的輔助隨機數為零；可以免除 A* 的 RFC 6979 §3.6 獨立 nonce 金鑰構造，在樣本中完全沒被採用（p.15）。
- **bits2octets 偏差**：libsecp256k1 在 0.2.0 之前沒有對訊息雜湊做 mod n；更早移植的 NBitcoin、dcrd、btcec v2（進而影響 LND、btcd、go-ethereum 的非 cgo 版本）仍沿用這個行為。只有在 h ≥ n（機率 2^−127.65）時才觀察得到，沒有安全影響，屬於相容性 bug（p.16）。同一個 go-ethereum 程式，cgo 版與非 cgo 版在 h ≥ n 時會產生不同簽章（p.16）。
- Ledger SDK 有兩個發生機率 2^−256 的偏差：bits2int(h) 恰好等於 n 時未化簡，且沒有拒絕 k = 0（p.16）。
- **low-R grinding**（Bitcoin Core 自 0.17 起預設）只需要擴充輸入域的 A*（Theorem 8.1）；low-R nonce 集合的所有非零 Fourier 係數無條件地小於 2^−116.9，利用這個偏差的攻擊需要超過 2^233 個簽章（p.16–17）。
- **h ≥ n 分支**：受檢視的實作都沒有測試這個分支（p.17）。
- **一位元 nonce 偏差**：作者的模型估計，256 位元曲線上一個無誤差的外洩位元需要 2^47.4 個簽章，合理範圍為 2^40–2^47；先前發表的外推值為 2^52（p.17）。
- GLV 分解不會讓均勻 nonce 產生偏差（p.18）。

## 資安分析

### 威脅模型
- **攻擊者能力**：知道公鑰 Q，可以向簽章 oracle 提交自選 digest，或在較弱設定下只能提交一般訊息的雜湊（p.3）。
- **攻擊者位置／存取權限**：能取得同一把私鑰的多個簽章，例如鏈上公開交易，或可以請求簽章的服務介面（論文未明述具體部署情境）。
- **前提假設**：DLP 困難；隨機化 ECDSA 具 EUF-CMA 安全性。
- **攻擊目標**：偽造簽章（EUF-CMA）、還原私鑰，或區分簽章紀錄。
- **不在範圍內**：本文的分離結果都針對刻意構造的 nonce 函數，沒有回答 HMAC_DRBG 本身是否滿足 A*（p.14）。實體旁通道與故障注入只當作背景脈絡（論文未深入分析）。

### 攻擊或防禦類型
可證明安全性分析（provable security），加上實作稽核。核心概念為 [ECDSA nonce 安全](../concepts/ecdsa-nonce-security.md)；第 9 節把[旁通道（side-channel）](../concepts/side-channel-attacks.md)造成的 nonce 外洩攻擊放進脈絡討論。

### 目標系統
[secp256k1 簽章實作](../systems/secp256k1-implementations.md)：共 31 個（18 個函式庫、7 個錢包與節點、6 個硬體錢包），以 2026-10-18 的預設分支為準；libsecp256k1 以 commit 04b91ce 為準（p.15）。

### 評估方式
- **實驗環境**：以理論證明為主。在 16 把 secp256k1 金鑰上實際執行 Theorem 5.9 的金鑰還原，16 個偽造簽章全部通過獨立驗證器（p.10）；Theorem 5.13 的各變體也做了類似驗證（p.14）。
- **資料集／Benchmark**：31 個實作的原始碼快照。
- **比較基準（baseline）**：論文未明述（不適用）。
- **評估指標**：歸約的緊密度、統計距離、Fourier 係數上界、合規性檢查結果。

## 實務影響

論文未回報 CVE。其他實務影響：
- **相容性 bug**：NBitcoin、dcrd、btcec v2 沿用未化簡的原始訊息雜湊，與 RFC 6979 不一致（p.16）。
- **測試缺口**：沒有任何實作測試 h ≥ n 分支，附錄 B 提供能區分合規版本與原始雜湊版本的測試向量（p.17、p.19）。
- **設計建議**：把 nonce 金鑰與簽章私鑰分開（RFC 6979 §3.6 或 EdDSA 的做法）就不需要 A*，但這與既有金鑰不相容（p.18）。
- 廠商回應：論文未明述。

## 相關頁面
- 概念：[ECDSA nonce 安全](../concepts/ecdsa-nonce-security.md)、[數位簽章](../concepts/digital-signatures.md)、[旁通道攻擊](../concepts/side-channel-attacks.md)
- 系統：[secp256k1 簽章實作](../systems/secp256k1-implementations.md)
- 會議：[arXiv 2026](../venues/arxiv-2026.md)
- 相關論文：
  - [資訊理論安全門檻指定驗證者簽章](2026-wright-it-threshold-dv-signatures.md)：都在處理簽章的安全基礎。本文指出廣泛部署的方案依賴未證明的計算假設；該文則完全捨棄計算假設，代價是只能由指定驗證者驗證，且金鑰很長（推論）。
  - [VellSSLH](2026-priya-vellsslh.md)：都關心真實密碼學函式庫的實作安全。本文處理 nonce 的產生，該文處理推測執行造成的外洩（推論）。
