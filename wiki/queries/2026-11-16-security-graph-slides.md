---
type: query
title: 資安知識圖譜簡報
question: "以 graph 的方式做投影片：先畫流程圖、整理大綱，確認後再生成"
sources: [index.md, overview.md, concepts/side-channel-attacks.md, concepts/location-privacy.md, concepts/digital-signatures.md, papers/2026-wang-cantraj.md, papers/2026-czybik-train-magnetometer-privacy.md, papers/2026-prosianoi-rfc6979-deterministic-ecdsa.md, papers/2026-wright-it-threshold-dv-signatures.md, papers/2026-priya-vellsslh.md]
date: 2026-11-16
updated: 2026-11-16
---

# 資安知識圖譜簡報

## 問題
把 wiki 當成知識圖譜，以走訪圖的方式製作簡報。先畫流程圖與大綱，經使用者確認後再生成。

## 回答
簡報檔：[2026-11-16-security-graph-slides.html](2026-11-16-security-graph-slides.html)，共 11 張。左側是知識圖譜，右側是說明文字；換頁時，圖會平移、縮放到下一個視角。點擊節點可以開啟對應的 wiki 頁面。

**圖譜規格**：顯示 24 個節點（5 篇論文、13 個概念、6 個系統；2 個會議頁省略），另有 6 個「缺少的節點」代表開放問題。邊分為歸屬（實線）、上位（箭頭）、相關（虛線）、對立（紅色虛線），以及缺少的邊（灰色點線）。

| # | 視角 | 圖上的焦點 |
|---|---|---|
| 1 | 全圖總覽 | 全部節點，加上圖例 |
| 2 | 樞紐節點 | 連結數徽章：免權限感測器 9、列車磁場 9、旁通道 8、位置隱私 7… |
| 3 | 隱私推論子圖 | 放大 ① 群 |
| 4 | 對立的邊 | CANTraj 與列車磁場之間的紅色虛線 |
| 5 | 簽章子圖 | 放大 ② 群 |
| 6 | 光譜的兩端 | 數位簽章底下的兩支：計算安全與無條件安全 |
| 7 | 微架構子圖 | 放大 ③ 群 |
| 8 | 防禦的取捨 | VellSSLH、SLH、LLVM |
| 9 | 橋接：旁通道 | 指向旁通道攻擊的 5 條邊亮起 |
| 10 | 缺少的邊 | 6 個虛線節點與開放問題 |
| 11 | 系統節點 → 角色 | 6 個系統節點，推出 4 個角色的建議 |

## 依據
- 節點數與連結數：以 wiki 頁面間的超連結計算，共 26 個節點、68 條連結（含會議頁）。
- 樞紐的連結數也依超連結計算；圖上畫的是語意關係，不是全部超連結。
- 其餘數字取自論文頁並附頁碼；跨論文綜合的內容標註「推論」。

## 延伸問題
- 圖譜節點與邊目前寫死在 HTML 裡。之後 ingest 新論文時，可以改成由 wiki 的 frontmatter 自動產生圖譜資料。
