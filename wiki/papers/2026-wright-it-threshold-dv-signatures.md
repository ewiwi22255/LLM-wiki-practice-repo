---
type: paper
title: "Information-Theoretic Threshold Designated-Verifier Digital Signatures for Resource-Constrained Devices"
authors: [Andrew Wright, Aydin Abadi, Ittoop Vergheese Puthoor]
year: 2026
venue: arXiv
arxiv: "2611.18355"
doi:
url: https://arxiv.org/abs/2611.18355
source: raw/arxiv-2611.18355.pdf
contribution_type: defense
concepts: [threshold-signatures, information-theoretic-security, digital-signatures]
systems: [resource-constrained-devices]
cves: []
ingested: 2026-11-16
updated: 2026-11-16
---

# 適用於資源受限裝置的資訊理論安全門檻指定驗證者數位簽章（Information-Theoretic Threshold Designated-Verifier Digital Signatures for Resource-Constrained Devices）

> 一句話摘要：把 USS（Unconditionally Secure Signature）這類無條件安全簽章，從單一簽署者擴展為 n-of-n 多重簽章，以及有或沒有 dealer 的 t-of-n 門檻簽章。安全性不依賴任何計算困難度假設，並在 Raspberry Pi 4 上實測可用。

## 基本摘要

### 研究問題
目前部署的簽章（包括 NIST 正在標準化、以格為基礎的後量子簽章）都是計算安全，隨著演算法與硬體進步可能被削弱（p.1）。[資訊理論安全（information-theoretic security）](../concepts/information-theoretic-security.md)的簽章（如 USS）目前只有單一簽署者版本，還沒有在標準門檻簽章語法與安全定義下的門檻變體（p.1）。作者以無人機群搜救任務為例：多個控制站需要共同簽署指令，避免單點被入侵（p.1–2）。核心問題是：能否以對資源受限裝置仍實用的成本，達成資訊理論安全的門檻簽章（p.2）？

### 方法
- **通用雜湊（universal hashing）**：把 USS 的雜湊族改為 (am + b) mod p，讓它具有可線性組合的同態性質（Proposition 1，p.3）。
- **Shamir 秘密分享**：把每個簽章函數的係數 (a, b) 拆成 t−1 次多項式的分享值，交給各個受託者（trustee）（p.3、p.6）。
- **五個演算法**：Gen、Split、Sign、Combine（以 Lagrange 插值組合部分簽章）、Ver（至少要有 Δ 比例的標籤正確才接受）（p.4、p.6）。
- **三個協定**：Protocol 1 為 n-of-n 多重簽章、Protocol 2 為有 dealer 的 t-of-n、Protocol 3 為無 dealer 的 t-of-n（每個受託者各自扮演 dealer）（p.6–8）。
- **安全定義**：以遊戲（game）形式定義抗偽造、可轉移性（transferability）、不可否認性（non-repudiation）與門檻安全（p.4–5）。
- **實作**：Python 3.13.2，參數 k = 100、Δ = 1（最壞情況），每組設定執行 200 次（p.10）。

### 主要貢獻
- 以標準語法與遊戲式安全定義，形式化資訊理論門檻簽章（p.2）。
- 三個具體構造，只用通用雜湊與 Shamir 秘密分享（p.2）。
- 各構造在無限算力攻擊者下的安全證明（p.2）。
- 在 MacBook Pro 與 Raspberry Pi 4 上實測，並與 Threshold ML-DSA-87 比較（p.2）。

### 結果
- **安全界**：腐化最多 t−1 個受託者與一半驗證者時，偽造成功機率 ≤ 1/p^{Δk}（p.9）。
- **有 dealer（Protocol 2）**：在 Raspberry Pi 4 上，簽章時間 0.085–45.3 ms，驗證時間 0.8–140 ms（2 到 20 個驗證者）。效能只跟驗證者人數有關，跟受託者人數無關（p.11）。
- **無 dealer（Protocol 3）**：在 MacBook 上約比有 dealer 慢 4–5 倍（p.11）；在 Raspberry Pi 4 上有 20 個驗證者時，驗證時間從 t = 2 的 530.7 ms 增加到 t = 5 的 2,014.0 ms（p.11）。
- **最壞情況**：每個受託者的簽章時間／每個驗證者的驗證時間，MacBook 約 25 ms / 216 ms，Raspberry Pi 4 約 232 ms / 2 s（p.2）。
- **與 Threshold ML-DSA-87 比較**：受託者與驗證者都多時，Protocol 2 在 MacBook 上簽章時間不到 7 ms，對方約 40–60 ms；Raspberry Pi 上 Protocol 3 不超過 250 ms，對方約 560 ms（p.13）。
- **長度成本**：每個函數需要 2·log2(p) 位元；dealer 與每位驗證者之間需要 rk·2·log2(p) 位元的金鑰；每個部分簽章為 r²k·log2(p) 位元（p.12）。

## 資安分析

### 威脅模型
- **攻擊者能力**：算力無上限，包含量子攻擊者（p.1、p.5）。
- **攻擊者位置／存取權限**：偽造遊戲中可腐化最多一半的驗證者，以及最多 t−1 個受託者；可轉移性遊戲中可腐化任意數量的受託者，以及一半的驗證者（p.5）。
- **前提假設**：發放階段所有通訊都走秘密通道（p.6）；Protocol 1、2 需要可信 dealer；驗證者必須預先登記，金鑰素材只能用一次（或有限次數）（p.1、p.13）。
- **攻擊目標**：存在性偽造、破壞可轉移性或不可否認性、以少於 t 個受託者產生有效簽章。
- **不在範圍內**：公開可驗證性；除非使用可信組合者（trusted combiner），否則不保證受託者匿名（p.3）；效能實驗沒有實作參與者之間的通訊加密（p.10）。

### 攻擊或防禦類型
防禦：新的密碼學構造，屬於[門檻簽章（threshold signatures）](../concepts/threshold-signatures.md)與[資訊理論安全](../concepts/information-theoretic-security.md)的交集。作者也承認，這類方案或許更適合稱為「可轉移 MAC（transferable MAC）」，而不是傳統意義的[數位簽章](../concepts/digital-signatures.md)（p.2）。

### 目標系統
[資源受限裝置](../systems/resource-constrained-devices.md)與封閉生態，例如無人機群、工業控制、支付基礎設施（p.1）。實測平台為 Raspberry Pi 4（4 GB）與 2025 M5 MacBook Pro（p.10–11）。

### 評估方式
- **實驗環境**：Raspberry Pi 4（Raspberry Pi OS Lite，Linux 6.12）、M5 MacBook Pro（16 GB）（p.11）。
- **資料集／Benchmark**：訊息 m = 1764，參數 k = 100、Δ = 1（p.10）。
- **比較基準（baseline）**：Threshold ML-DSA-87（Celi et al.，USENIX Security '26），使用其官方 benchmark 工具（p.13）。
- **評估指標**：每個受託者的簽章時間、每個驗證者的驗證時間（平均與標準差），以及金鑰與簽章長度。

## 實務影響

論文未回報 CVE。其他實務影響：
- 程式碼放在匿名 repo，作者表示接受後會送交 artifact evaluation（p.14）。
- **作者自述的限制**：簽章與金鑰長度大，無 dealer 版本尤其明顯；屬於一次性簽章，每次都要更新簽章函數；不具公開可驗證性；驗證者越多，驗證成本越高（p.13）。

## 相關頁面
- 概念：[門檻簽章](../concepts/threshold-signatures.md)、[資訊理論安全](../concepts/information-theoretic-security.md)、[數位簽章](../concepts/digital-signatures.md)
- 系統：[資源受限裝置](../systems/resource-constrained-devices.md)
- 會議：[arXiv 2026](../venues/arxiv-2026.md)
- 相關論文：
  - [RFC 6979 確定性 ECDSA](2026-prosianoi-rfc6979-deterministic-ecdsa.md)：處理簽章安全基礎的另一個方向，見[數位簽章](../concepts/digital-signatures.md)頁的比較（推論）。
