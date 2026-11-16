---
type: system
title: LLVM
category: other
vendor: LLVM Project
updated: 2026-11-16
---

# LLVM

> 開源編譯器基礎設施。C、C++、Rust 等語言都經由 LLVM IR 產生機器碼。在本 wiki 中，主要關注它在 Spectre 防禦與常數時間保持上的角色。

## 攻擊面
- **編譯器可能破壞原始碼層的安全性質**：例如把 select 轉成分支、在 x86 上做條件移動轉換（[VellSSLH](../papers/2026-priya-vellsslh.md) p.14）。
- **暫存器溢出（spill）**：IR 檢查之後才插入的溢出與重新載入，可能讓公開值在推測時變成秘密；如果錯誤推測旗標被溢出，之後的保護會全部失效（p.4）。
- **LLVM 的值語意**：poison、未定值與指標 provenance 讓 IR 層的保護更難推理（p.4）。
- **IMPLICIT_DEF**：若從殘留秘密內容的暫存器解析未定值，即使通過 MSF 檢查仍可能洩漏（p.20）。

## 已知攻擊
| 攻擊 | 手法概念 | 備註 |
|---|---|---|
| Spectre v1 | [推測執行](../concepts/spectre.md) | 經 LLVM 編譯的常數時間 C 程式仍可能洩漏 |

## 防禦機制
相關概念：[推測載入強化](../concepts/speculative-load-hardening.md)

| 防禦／論文 | 年份 | 對抗的攻擊 | 部署狀況 |
|---|---|---|---|
| LLVM SLH | — | Spectre v1 | LLVM 內建選項；VellSSLH 的效能基準 |
| Ultimate SLH | — | Spectre v1 加上執行時間取決於運算元的指令 | 學術實作（LLVM 16） |
| [VellSSLH](../papers/2026-priya-vellsslh.md) | 2026 | Spectre v1 | 學術實作（LLVM 23.0.0git），檢查器以 Rocq 證明健全 |

## 相關 CVE
目前沒有。

## 相關論文
- [VellSSLH](../papers/2026-priya-vellsslh.md)
