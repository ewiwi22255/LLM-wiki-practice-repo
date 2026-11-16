---
type: concept
title: Spectre（推測執行攻擊）
aliases: [Spectre v1, bounds check bypass, speculative execution attack, 推測執行攻擊]
parent: [side-channel-attacks]
related: [speculative-load-hardening, speculative-constant-time]
paper_count: 1
updated: 2026-11-16
---

# Spectre（推測執行攻擊）

> 處理器為了效能會先依預測執行指令；預測錯誤時，結果雖然會被丟棄，但存取過的資料會在微架構狀態留下可觀察的痕跡。

## 定義
Spectre v1（邊界檢查繞過，bounds check bypass）：條件分支預測錯誤時，處理器會推測執行邊界檢查之後的載入指令，讀到原本不該讀的秘密資料，並透過快取等管道洩漏出去。即使程式在正常執行下是常數時間，也可能在推測執行下洩漏（[VellSSLH](../papers/2026-priya-vellsslh.md) p.1）。

## 運作原理／子類型
VellSSLH 的形式化模型（p.4–6）：
- **攻擊者指令**：`step`（照正確方向走）、`force`（強制走錯誤分支）、`load(a)` / `store(a)`（把不合法的推測存取重導向到其他可讀位址）。
- **觀察**：分支結果、記憶體位址、公開回傳值、錯誤，以及經授權的解密。
- **錯誤推測位元**：一旦設定就保持不變，不模擬回滾（前向推測）。

目前收錄的論文都**沒有涵蓋**的變體：間接分支（Spectre v2）、store bypass（v4）、RSB（VellSSLH p.20）。

## 防禦方式
- **LFENCE 等序列化屏障**：停止後續推測，成本高。
- **[推測載入強化（SLH）](speculative-load-hardening.md)**：追蹤錯誤推測狀態，並遮罩載入的值。
- **型別系統或靜態分析**：證明程式滿足 [SCT](speculative-constant-time.md)。

## 相關論文
| 論文 | 年份 | 類型 | 在此概念上的貢獻 |
|---|---|---|---|
| [VellSSLH](../papers/2026-priya-vellsslh.md) | 2026 | defense | 擴充 Vellvm，把 Spectre v1 模型形式化到 LLVM IR，並提供經證明的選擇性強化 |

## 開放問題
- LLVM IR 層級的保證能否完整延伸到機器碼，還取決於後端與硬體假設（VellSSLH p.14、p.20）。
- 涵蓋 Spectre-RSB、間接分支等其他變體（VellSSLH p.19–20）。

## 相關概念
- [旁通道攻擊](side-channel-attacks.md)
- [推測載入強化](speculative-load-hardening.md)
- [推測常數時間](speculative-constant-time.md)

## 相關系統
- [LLVM](../systems/llvm.md)
