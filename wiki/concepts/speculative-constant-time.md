---
type: concept
title: 推測常數時間（Speculative Constant-Time）
aliases: [SCT, constant-time programming, 常數時間程式設計]
parent: [side-channel-attacks]
related: [spectre, speculative-load-hardening]
paper_count: 1
updated: 2026-11-16
---

# 推測常數時間（Speculative Constant-Time）

> 常數時間（constant-time）程式不會依秘密決定分支或記憶體位址；推測常數時間（SCT）則要求這個性質在推測執行下也成立。

## 定義
在 [VellSSLH](../papers/2026-priya-vellsslh.md) 的定義中（p.10）：對任意兩個只有秘密輸入不同、但符合入口契約的初始狀態，以及任意（可自適應的）攻擊者策略，兩次執行能產生的有限觀察序列集合必須相同。之所以比較「集合」，是為了容納 LLVM 本身的非確定性，例如配置位址不同；這些差異本身不洩漏秘密。

## 運作原理
- **三種安全型別**：P =（公開，公開）、T =（公開，秘密）、S =（秘密，秘密），順序為 P ≤ T ≤ S（VellSSLH p.7）。
- 載入至少是 T，因為不合法的推測存取可能讀到秘密記憶體（p.8）。
- 分支條件與位址必須是 P；T 可以用 protect 修復成 P，S 不能（p.9）。
- **解密**：只在正常執行時讓值變成公開，推測時仍保持 T（p.9）。

## 驗證方式
| 方式 | 代表 | 來源 |
|---|---|---|
| 型別系統加機械化證明 | Jasmin、VellSCT | VellSSLH p.1–2 |
| 有界符號分析組合語言 | Spectector（IEEE S&P 2020） | VellSSLH p.19–20 |
| 編譯過程中的安全保持 | CompCertCT、SNIP、Arranz Olmos et al. | VellSSLH p.20 |
| SCT 的形式化起源 | Cauligi et al.（Pitchfork） | VellSSLH p.19 |

## 相關論文
| 論文 | 年份 | 類型 | 在此概念上的貢獻 |
|---|---|---|---|
| [VellSSLH](../papers/2026-priya-vellsslh.md) | 2026 | defense | 讓 SCT 適用於 LLVM 的非確定性語意（poison、未定值、provenance），並證明檢查器健全 |

## 開放問題
- 後端解析未定值時，必須與秘密無關。僅有架構上的 refinement 不夠，因為解析非確定性選擇時，可能讓兩組觀察序列以不同方式縮小，進而破壞 SCT（VellSSLH p.11）。
- 編譯器後段的暫存器溢出與 IMPLICIT_DEF 等，可能讓 IR 層的保證失效（VellSSLH p.20）。

## 相關概念
- [Spectre](spectre.md)
- [推測載入強化](speculative-load-hardening.md)
- [旁通道攻擊](side-channel-attacks.md)
