---
type: query
title: 當前資安問題簡報
question: "用目前專案資料夾的 AGENTS.md，根據 LLM wiki 的內容，製作現在資安的問題，做成一個簡報式的投影片，並做一個 html 放到資料夾中"
sources: [overview.md, concepts/location-privacy.md, concepts/side-channel-attacks.md, concepts/digital-signatures.md, concepts/ecdsa-nonce-security.md, concepts/speculative-load-hardening.md, papers/2026-wang-cantraj.md, papers/2026-czybik-train-magnetometer-privacy.md, papers/2026-prosianoi-rfc6979-deterministic-ecdsa.md, papers/2026-wright-it-threshold-dv-signatures.md, papers/2026-priya-vellsslh.md, systems/secp256k1-implementations.md]
date: 2026-11-16
updated: 2026-11-16
---

# 當前資安問題簡報

## 問題
根據 wiki 的內容，把目前的資安問題整理成簡報式投影片，輸出為 HTML。

## 回答
簡報檔：[2026-11-16-security-issues-slides.html](2026-11-16-security-issues-slides.html)，共 13 張，以瀏覽器開啟。操作方式：← → 換頁、F 全螢幕、可切換深淺色、列印時每頁一張投影片，可輸出 PDF。

| # | 投影片 | 重點 |
|---|---|---|
| 1 | 封面 | 「看起來安全」的四個假設正在失效 |
| 2 | 研究地圖 | 三個問題群，由旁通道串起 |
| 3 | 刪除 GPS ≠ 保護位置 | CANTraj：加入道路圖後誤差從 921 m 降到 43 m；方向盤讓路徑還原率從 56% 升到 87%；最嚴苛劣化下仍有 ≥ 82.6% |
| 4 | 免權限磁力計 | 16.7 Hz；偵測搭電車 MCC 0.93、同行推論 MCC 0.79；讀取不需要權限 |
| 5 | 劣化防禦的相反結論 | 取決於頻帶與外部先驗（推論） |
| 6 | RFC 6979 的未證明假設 A* | 31 個實作中 28 個預設確定性簽章；0 個採用獨立 nonce 金鑰；0 個測試 h ≥ n |
| 7 | 安全基礎光譜 | 計算安全與資訊理論安全；IT 門檻簽章的成本 |
| 8 | Spectre 與選擇性 SLH | VellSSLH：比 LLVM SLH 快 4.03%；X25519 快 22.7%；短函數慢 7–10% |
| 9 | 旁通道的三種載體 | 微架構、nonce 外洩、環境物理訊號 |
| 10 | 五個趨勢 | 統一基準、形式化方法、重新檢視無害資料、超越計算假設、生成式 AI（推論） |
| 11 | 開放問題 | 6 項 |
| 12 | 給實務者 | 資料發布者、行動作業系統、密碼學函式庫、編譯器 |
| 13 | 資料來源 | 5 篇論文 |

## 依據
- 所有數字取自論文頁，並附原文頁碼；跨論文綜合的內容標註「推論」。
- 主要依據 [overview.md](../overview.md) 的領域地圖、趨勢與開放問題總表。

## 延伸問題
- 樣本只有 5 篇，趨勢判斷的可信度有限。之後 ingest 更多論文時，可以重新產生這份簡報並比較差異。
- 第 12 張的角色建議，可以延伸成每個角色一頁的行動檢查清單。
