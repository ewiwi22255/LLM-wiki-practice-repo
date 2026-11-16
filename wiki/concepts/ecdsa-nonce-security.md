---
type: concept
title: ECDSA nonce 安全（ECDSA Nonce Security）
aliases: [RFC 6979, deterministic ECDSA, nonce bias, hidden number problem, 確定性 ECDSA]
parent: [digital-signatures]
related: [side-channel-attacks]
paper_count: 1
updated: 2026-11-16
---

# ECDSA nonce 安全（ECDSA Nonce Security）

> ECDSA 每次簽章都需要一個秘密 nonce k；k 只要重複、有偏差或部分外洩，就足以還原簽章私鑰。

## 定義
ECDSA 簽章為 (r, s)，其中 R = kG、r = x(R) mod n、s = k⁻¹(h + rx) mod n（[RFC 6979 論文](../papers/2026-prosianoi-rfc6979-deterministic-ecdsa.md) p.3）。只要知道 k，就能從一個簽章直接算出私鑰 x = (sk − h)·r⁻¹ mod n（p.4）。

## nonce 產生策略
| 策略 | 做法 | 需要的假設 | 部署狀況（RFC 6979 論文 p.15） |
|---|---|---|---|
| 隨機 | k 從 [1, n−1] 均勻抽取 | 可靠的亂數產生器 | python-ecdsa、wolfSSL、Crypto++ 等預設 |
| 確定性（RFC 6979） | k = HMAC_DRBG(x ‖ h) | **A\***（未證明） | secp256k1 生態的 31 個實作中有 28 個預設使用 |
| Hedged | 確定性加額外隨機數 | 隨機數品質好時不需 A* | OpenSSL 3.x 預設；18 個函式庫中有 9 個提供 API，但預設都不啟用 |
| 獨立 nonce 金鑰（RFC 6979 §3.6 / EdDSA） | k = P_z(h)，z 與 x 獨立 | 只需 P 的 PRF 安全性 | 樣本中完全沒被採用 |
| BIP-340 aux | t = d ⊕ H(aux)，再推導 k | A* 的類比，在 ROM 中證明 | 26 個實作中有 15 個的 aux 為零 |
| Low-R grinding | 以計數器重試，直到 r 落在 32 位元組以內 | 擴充輸入域的 A* | Bitcoin Core 自 0.17 起預設 |

## 核心假設 A*
在已知公鑰時，用 Fx 產生 nonce 的簽章紀錄，與用均勻 nonce 的紀錄不可區分。A* 是充分條件但非必要條件，且與 PRF、AI-PRF 都不等價。在理想壓縮函數模型下，A* 可以從「提供簽章 oracle 的 DLP」推得；但目前沒有標準模型的證明，也沒有已知攻擊。作者形容它「可能是 ECDSA 實務中部署最廣的未證明假設」（RFC 6979 論文 p.18）。

## nonce 外洩攻擊（脈絡）
依 RFC 6979 論文第 9 節（p.17）：
- 一位元偏差：Aranha et al. 在 160 位元曲線上用 2^33 個簽章破解，並估計 256 位元需要 2^52 個。
- LadderLeak（CCS 2020）：在 P-192 上用約 2^29 個簽章破解一位元外洩。
- 格方法：256 位元曲線上的兩位元外洩，只需 146 個簽章。
- 作者的模型：256 位元曲線上一個無誤差的外洩位元，約需 2^47.4 個簽章（合理範圍 2^40–2^47）。256 位元曲線上的一位元外洩至今尚未被實際攻破。
- 這類外洩通常來自功耗或時間[旁通道](side-channel-attacks.md)。

## 曲線相依性
直接把 256 位元值 mod n 而不做拒絕抽樣時，與均勻分布的偏差為：secp256k1 為 2^−127.65，實務上無關緊要；P-256 為 2^−32；brainpoolP256r1 偏差大到幾百個簽章就能用格攻擊（RFC 6979 論文 p.15）。

## 相關論文
| 論文 | 年份 | 類型 | 在此概念上的貢獻 |
|---|---|---|---|
| [RFC 6979 確定性 ECDSA](../papers/2026-prosianoi-rfc6979-deterministic-ecdsa.md) | 2026 | analysis | 界定 A*，給出反例與分離結果，並稽核 31 個實作 |

## 開放問題
（皆出自 RFC 6979 論文 p.18）
- 以標準模型證明或推翻 HMAC_DRBG 滿足 A*。
- 移除 Theorem 4.8 的碰撞抗性前提。
- 在原始參數下，只用兩則一般雜湊訊息的簽章達成金鑰還原分離。
- 縮小 low-R 集合 Fourier 上界與啟發式估計之間約 2^11 倍的差距。
- 實際量測（而非用模型推估）256 位元曲線上一位元 nonce 外洩的攻擊成本。

## 相關概念
- [數位簽章](digital-signatures.md)
- [旁通道攻擊](side-channel-attacks.md)

## 相關系統
- [secp256k1 簽章實作](../systems/secp256k1-implementations.md)
