# 操作日誌

> append-only。只能在檔尾新增紀錄。查看最近操作：`grep "^## \[" wiki/log.md | tail -10`

## [2026-11-16] init | 建立知識庫
- 建立 AGENTS.md、CLAUDE.md 與目錄結構
- 新增：wiki/index.md、wiki/log.md、wiki/overview.md

## [2026-11-16] ingest | 批次 5 篇（arXiv 2611.18442 / 18449 / 18355 / 18312 / 18463）
- 下載：raw/arxiv-2611.18442.pdf、arxiv-2611.18449.pdf、arxiv-2611.18355.pdf、arxiv-2611.18312.pdf、arxiv-2611.18463.pdf（皆驗證 %PDF）
- 新增論文：papers/2026-wang-cantraj.md、2026-prosianoi-rfc6979-deterministic-ecdsa.md、2026-wright-it-threshold-dv-signatures.md、2026-czybik-train-magnetometer-privacy.md、2026-priya-vellsslh.md
- 新增概念（13）：location-privacy、route-inference-attack、zero-permission-sensor-leakage、transport-mode-detection、colocation-inference、side-channel-attacks、spectre、speculative-load-hardening、speculative-constant-time、digital-signatures、ecdsa-nonce-security、threshold-signatures、information-theoretic-security
- 新增系統（6）：vehicle-can-bus、secp256k1-implementations、resource-constrained-devices、android、ios、llvm
- 新增會議：venues/arxiv-2026.md、venues/ccs-2026.md
- 更新：overview.md（建立三群領域地圖、趨勢、開放問題、統計）、index.md
- 備註：這台電腦沒有 poppler，Read 工具無法讀 PDF，改用 pdftotext 抽取文字並加上頁碼標記。五篇都沒有回報 CVE，因此沒有建立 CVE 頁。沒有發現跨論文矛盾；CANTraj 與列車磁場論文對「訊號劣化防禦」的結論相反，已記錄在 location-privacy 頁（推論）。CANTraj 摘要與表 IV 的 LLM 數字略有出入，已在論文頁註明。

## [2026-11-16] query | 當前資安問題簡報（HTML 投影片）
- 新增：queries/2026-11-16-security-issues-slides.md、queries/2026-11-16-security-issues-slides.html（13 張投影片）
- 更新：index.md
- 備註：沒有產生新的跨論文洞見，因此沒有回寫概念頁；簡報內容全部取自既有 wiki 頁面。

## [2026-11-16] query | 資安知識圖譜簡報（graph 式 HTML 投影片）
- 先提出流程圖與大綱，使用者確認後才生成
- 新增：queries/2026-11-16-security-graph-slides.md、queries/2026-11-16-security-graph-slides.html（11 張）
- 更新：index.md
- 備註：圖譜統計為 26 個節點、68 條超連結；樞紐依連結數計算。沒有回寫概念頁。
