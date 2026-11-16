---
type: concept
title: 推測載入強化（Speculative Load Hardening）
aliases: [SLH, selective SLH, SSLH, Ultimate SLH, USLH]
parent: [spectre]
related: [speculative-constant-time]
paper_count: 1
updated: 2026-11-16
---

# 推測載入強化（Speculative Load Hardening）

> 編譯器插入的 Spectre v1 防禦：用一個「錯誤推測旗標」追蹤目前是否走在錯誤的分支上，在錯誤推測時把載入的值或位址遮罩成固定值，讓秘密無法外洩。

## 定義
LLVM SLH 以一個 predicate 追蹤錯誤推測，並引入資料相依性來阻擋洩漏（[VellSSLH](../papers/2026-priya-vellsslh.md) p.1）。維持這個 predicate 會增加暫存器壓力，強化也會多出指令；**選擇性強化（selective hardening）**只保護真的可能造成推測洩漏的值（p.1）。

## 運作原理
三個基本操作（以 VellSSLH 為例，p.6、p.13–14）：
- `init_msf()`：初始化旗標為 0（乾淨）。x86-64 上是 LFENCE 加清零，會停止錯誤推測。
- `set_msf(m, c, b)`：在每條分支邊上，若實際條件 c 與所走的邊 b 不一致，就把旗標設為全 1，且之後永遠保持。
- `protect(v, m)`：v OR m。旗標乾淨時保留原值，設定時變成固定的全 1。

**選擇性的依據**：值分成 P、T、S 三種型別，只有 T（正常執行公開、推測時可能秘密）在可觀察的使用前需要 protect；S（秘密）的運算不需要遮罩（[推測常數時間](speculative-constant-time.md)）。

## 變體與發展脈絡
| 方案 | 特點 | 來源 |
|---|---|---|
| LLVM SLH | 正式上線的基準；在載入後強化暫存器 | VellSSLH p.19 |
| Spectre Declassified | 提出選擇性 SLH，以及解密與推測的互動 | VellSSLH p.1、p.19 |
| Jasmin typing（Typing High-Speed Crypto against Spectre v1） | P/T/S 標籤、MSF typestate、三個強化原語 | VellSSLH p.1、p.19 |
| Blade | transient/stable 型別加最小割，用於 Cranelift/WebAssembly | VellSSLH p.20 |
| Ultimate SLH | 在 LLVM 中實作，也保護執行時間取決於運算元的指令，但轉換未經機械化驗證 | VellSSLH p.2、p.19 |
| FSLH | 統一選擇性與 ultimate 強化，提供機械化的相對安全保證 | VellSSLH p.19–20 |
| LightSLH | 以抽象解釋做強化感知的不動點分析 | VellSSLH p.20 |
| **VellSSLH**（2026） | 用於 LLVM IR、Rocq 證明健全性、MSF 不可溢出 | [VellSSLH](../papers/2026-priya-vellsslh.md) |

## 效能取捨（VellSSLH 的實測）
- 比 LLVM SLH 的幾何平均執行時間低 4.03%；X25519 快 22.69%（p.15）。
- 選擇性與保守放置相比，保護點從 149 個降到 3 個，執行時間快 2.3–5.3%（p.17）。
- **反例**：約 80 ns 的短函數因為入口 LFENCE 的固定成本，比 LLVM SLH 慢 7–10%（p.17）。LLVM SLH 透過堆疊指標傳遞 poison 狀態，不需要 LFENCE（p.17）。

## 相關論文
| 論文 | 年份 | 類型 | 在此概念上的貢獻 |
|---|---|---|---|
| [VellSSLH](../papers/2026-priya-vellsslh.md) | 2026 | defense | 型別引導、可證明健全的選擇性 SLH |

## 開放問題
- 在相同 LLVM 設定下與 LightSLH 比較，以及與最佳化過的 libjade 比較（VellSSLH p.20）。
- 依程式點追蹤記憶體標籤，以減少不必要的保護（VellSSLH p.17）。
- 入口屏障的固定成本，對短小函數是否有更便宜的替代方案（推論）。

## 相關概念
- [Spectre](spectre.md)
- [推測常數時間](speculative-constant-time.md)
- [旁通道攻擊](side-channel-attacks.md)

## 相關系統
- [LLVM](../systems/llvm.md)
