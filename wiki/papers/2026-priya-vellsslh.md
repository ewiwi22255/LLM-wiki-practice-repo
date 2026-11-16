---
type: paper
title: "VellSSLH: Sound Type-Guided Selective Speculative Load Hardening for LLVM IR"
authors: [Swarn Priya]
year: 2026
venue: arXiv
arxiv: "2611.18463"
doi:
url: https://arxiv.org/abs/2611.18463
source: raw/arxiv-2611.18463.pdf
contribution_type: defense
concepts: [speculative-load-hardening, speculative-constant-time, spectre, side-channel-attacks]
systems: [llvm]
cves: []
ingested: 2026-11-16
updated: 2026-11-16
---

# VellSSLH：以型別引導、可證明健全的 LLVM IR 選擇性推測載入強化（VellSSLH: Sound Type-Guided Selective Speculative Load Hardening for LLVM IR）

> 一句話摘要：程式設計者只要標註 Public／Secret 輸入，VellSSLH 就在 LLVM IR 上推論哪些值會在推測執行時變成機密，只在必要的地方插入 SLH 保護，並以 Rocq 機械化證明檢查器的健全性。在 24 個密碼學 API 上，平均執行時間比 LLVM SLH 低 4.03%。

## 基本摘要

### 研究問題
用 C 寫的常數時間（constant-time）密碼學程式，在[推測執行（speculative execution）](../concepts/spectre.md)下仍可能洩漏秘密：分支預測錯誤會繞過邊界檢查並讀到秘密資料（p.1）。[推測載入強化（speculative load hardening, SLH）](../concepts/speculative-load-hardening.md)可以阻擋這類洩漏，但對不需要的值也加保護，會帶來顯著額外負擔（p.1）。既有工作的缺口在於：Ultimate SLH 在 LLVM 中實作，但轉換本身沒有機械化驗證；Jasmin 有經驗證的選擇性強化，但只適用於自己的語言，不適用於 C（p.2）。

### 方法
- **VellSCT 型別系統**：每個值有一對型別（正常執行，推測執行），共三種——P =（公開，公開）、T =（公開，秘密）、S =（秘密，秘密）。只有 T 型別的值需要在可觀察的使用前加保護；秘密運算本身不需遮罩（p.1、p.7）。
- **錯誤推測旗標（misspeculation flag, MSF）typestate**：追蹤旗標狀態（unknown / ready / pending），處理 SSA 匯合點的 PHI（包括迴圈回邊），避免使用過期的旗標或把旗標重設為零（p.4、p.8）。
- **擴充 Vellvm 語意**：加入攻擊者主導的前向推測、記憶體存取重導向與洩漏觀察，並處理 LLVM 的 poison、未定值、指標來源（provenance）與錯誤（p.4–6）。
- **Rocq 機械化證明**：
  - Theorem 1：檢查器接受的程式滿足[推測常數時間（speculative constant-time, SCT）](../concepts/speculative-constant-time.md)（p.10）。
  - Theorem 2：考慮明確宣告的解密（declassification）與可信外部服務後，仍保有對應的安全性（p.11）。
  - Theorem 3：參考插入演算法保持架構行為，也就是強化不會改變原程式在正常執行下的定義行為（p.12）。
  - 新增 337,040 行 Rocq 程式碼與證明（p.2）。
- **後端**：MSF 被標記為不可溢出（nonspillable），但不永久保留任何實體暫存器；x86-64 上 init_msf 編成 LFENCE 加清零，protect 編成 MOV 加 OR（p.4、p.13–14）。

### 主要貢獻
- 針對 LLVM IR 的 SCT 型別系統，以及完整的 Rocq 健全性證明（p.2）。
- 由 Public／Secret 標註自動推論並插入保護，同時證明參考插入演算法保持架構行為（p.2）。
- 在 LLVM 中實作，包括不溢出的 MSF 與溢出後重新載入的檢查（p.2）。
- 在 24 個密碼學 API 與元件上評估（p.2）。

### 結果
- **適用性**：24 個強化後的模組全部通過 VellSCT 與功能測試，涵蓋 TweetNaCl、libsodium、HACL*、Kyber Round 3（p.14）。
- **與 LLVM SLH 比較**：幾何平均執行時間低 4.03%；13 個比較顯著較快、8 個較慢、3 個無定論（p.1、p.15）。
- **X25519**：比 LLVM SLH 快 22.69%，相對未保護程式只多 1.86% 負擔（p.15）。IR 中只有 11 個保護點，LLVM SLH 則強化 129 個載入結果（p.17）。
- **與 Ultimate SLH 比較**：執行時間低 19.65%，但兩者使用不同 LLVM 版本（LLVM 16 對 23.0.0git），且在不同批次實驗中量測（p.16）。
- **選擇性的效益**：在三個固定核心上，保護點從 149、85、85 個降到各 3 個，執行時間快 2.3–5.3%（p.2、p.17）。但這三個約 80 ns 的短函數仍比 LLVM SLH 慢 7–10%，因為入口的 LFENCE 是固定成本（p.17）。
- **二進位驗證**：用 Spectector 做有界分析，8 個 VellSSLH 二進位檔通過全部查詢；另外 16 個因資源上限或指令模型不支援而沒有結論，這並不代表偵測到洩漏（p.19）。
- **分析成本**：Kyber512 decapsulation 的插入花了 769 s，重新檢查花了 339 s（p.15）。

## 資安分析

### 威脅模型
- **攻擊者能力**：可以強制分支走錯方向，並把不安全的推測記憶體存取重導向到其他可讀位址（p.5）；可以觀察分支結果、記憶體位址、公開回傳值與經授權的解密（p.4）。也就是 [Spectre v1](../concepts/spectre.md) 模型。
- **攻擊者位置／存取權限**：論文未明述具體部署位置，以觀察序列（observation trace）抽象化。
- **前提假設**：前向推測，帶有一旦設定就不清除的錯誤推測位元，不考慮回滾（p.20）；配置版面與連結環境滿足執行期假設（p.10）；生成的二進位檔是否安全，還取決於後端與硬體假設（p.1、p.14）。
- **攻擊目標**：在推測執行下，讓常數時間密碼學程式洩漏秘密。
- **不在範圍內**：間接分支攻擊、store bypass、RSB 與其他時間通道；SIMD、inline assembly、原子操作；機械化保證只到 LLVM IR 層（p.20）。

### 攻擊或防禦類型
防禦：編譯器層級的選擇性 [SLH](../concepts/speculative-load-hardening.md) 加上形式化驗證，目標是 [SCT](../concepts/speculative-constant-time.md)，對抗微架構[旁通道](../concepts/side-channel-attacks.md)。

### 目標系統
[LLVM](../systems/llvm.md)（自建 LLVM 23.0.0git），以 x86-64 後端為主要評估對象；AArch64 已實作但沒有量測（p.14、p.20）。

### 評估方式
- **實驗環境**：bare-metal Linux，Intel Core Ultra 5 335，固定使用同一個效能核心；分析成本在 Apple M4（16 GB）上量測（p.14–15）。
- **資料集／Benchmark**：24 個密碼學操作，包括 MAC、串流加密、Secretbox、SHA/SHAKE、X25519、Kyber512/768、Salsa/ChaCha 核心（p.14）。
- **比較基準（baseline）**：未保護程式、LLVM SLH、Ultimate SLH（p.14）。
- **評估指標**：執行時間（9 次隨機化程式碼佈局的試驗，取中位數與 95% 信賴區間）、靜態保護點數量、分析時間與記憶體、Spectector 有界驗證結果（p.14–19）。

## 實務影響

論文未回報 CVE。其他實務影響：
- 說明選擇性 SLH 可以套用在既有的 C 密碼學程式上，對 X25519 這類載入密集的程式效益最明顯（p.15）。
- 入口 barrier 的固定成本、暫存器壓力與重新載入的保護，可能抵銷選擇性帶來的好處；短小的函數反而比 LLVM SLH 慢（p.17）。
- 論文未提供 artifact 連結（論文未明述）。

## 相關頁面
- 概念：[推測載入強化](../concepts/speculative-load-hardening.md)、[推測常數時間](../concepts/speculative-constant-time.md)、[Spectre](../concepts/spectre.md)、[旁通道攻擊](../concepts/side-channel-attacks.md)
- 系統：[LLVM](../systems/llvm.md)
- 會議：[arXiv 2026](../venues/arxiv-2026.md)
- 相關論文：
  - [RFC 6979 確定性 ECDSA](2026-prosianoi-rfc6979-deterministic-ecdsa.md)：都在分析真實密碼學函式庫的實作層安全（推論）。
