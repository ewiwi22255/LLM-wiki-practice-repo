# Wiki 目錄

> 全部 wiki 頁面的目錄。查詢時先讀這裡。最後更新：2026-11-16

- [綜覽 / 研究地圖](overview.md)
- [操作日誌](log.md)

## 論文（5）
- [同一條軌道上：電氣化鐵路的磁場造成乘客隱私洩漏](papers/2026-czybik-train-magnetometer-privacy.md) — 免權限的磁力計偵測 16.7 Hz 牽引電流，推論是否搭電車與同行者 · CCS 2026 · attack
- [RFC 6979 確定性 ECDSA 的安全假設與實作現況](papers/2026-prosianoi-rfc6979-deterministic-ecdsa.md) — RFC 6979 依賴未證明的假設 A*；給出反例與分離結果，並稽核 31 個 secp256k1 實作 · arXiv 2026 · analysis
- [VellSSLH：可證明健全的 LLVM IR 選擇性 SLH](papers/2026-priya-vellsslh.md) — 以型別推論只在必要處插入 SLH，Rocq 證明健全，比 LLVM SLH 快 4.03% · arXiv 2026 · defense
- [CANTraj：從 CAN 訊號還原車輛軌跡](papers/2026-wang-cantraj.md) — CAN–GNSS 資料集與基準；速度＋方向盤＋道路圖可在 5 分鐘內還原 88% 的路徑 · arXiv 2026 · measurement
- [資訊理論安全門檻指定驗證者簽章](papers/2026-wright-it-threshold-dv-signatures.md) — 無計算假設的 n-of-n 與 t-of-n 簽章，在 Raspberry Pi 4 上實測 · arXiv 2026 · defense

## 概念（13）
- [同行推論（colocation inference）](concepts/colocation-inference.md) — 比對感測器軌跡，判斷兩支裝置是否同行 · 1 篇
- [數位簽章（digital signatures）](concepts/digital-signatures.md) — 簽章方案的上位概念；計算安全與資訊理論安全的光譜 · 2 篇
- [ECDSA nonce 安全](concepts/ecdsa-nonce-security.md) — nonce 的產生策略、RFC 6979 與 A*、nonce 外洩攻擊 · 1 篇
- [資訊理論安全（information-theoretic security）](concepts/information-theoretic-security.md) — 面對無限算力攻擊者仍成立的安全性 · 1 篇
- [位置隱私（location privacy）](concepts/location-privacy.md) — 從非位置訊號推論行蹤；比較劣化防禦的效果 · 2 篇
- [路徑推論攻擊（route inference attack）](concepts/route-inference-attack.md) — 以車輛訊號加道路圖還原行駛路徑 · 1 篇
- [旁通道攻擊（side-channel attacks）](concepts/side-channel-attacks.md) — 從實作或環境的副作用推論秘密；上位概念 · 3 篇
- [推測常數時間（speculative constant-time）](concepts/speculative-constant-time.md) — 常數時間性質在推測執行下的延伸 · 1 篇
- [推測載入強化（speculative load hardening）](concepts/speculative-load-hardening.md) — 以錯誤推測旗標遮罩載入值的 Spectre v1 防禦 · 1 篇
- [Spectre（推測執行攻擊）](concepts/spectre.md) — 分支預測錯誤造成的微架構洩漏 · 1 篇
- [門檻簽章（threshold signatures）](concepts/threshold-signatures.md) — t-of-n 分散式簽章 · 1 篇
- [交通方式偵測（transport mode detection）](concepts/transport-mode-detection.md) — 從感測器判斷使用者的交通方式 · 1 篇
- [免權限感測器洩漏（zero-permission sensor leakage）](concepts/zero-permission-sensor-leakage.md) — 免權限的手機感測器成為推論管道 · 1 篇

## 系統（6）
- [Android](systems/android.md) — 感測器存取不需要權限，取樣率上限 200 Hz
- [iOS](systems/ios.md) — 感測器存取不需要權限，取樣率上限 100 Hz
- [LLVM](systems/llvm.md) — 編譯器基礎設施；Spectre 防禦與常數時間的保持
- [資源受限裝置](systems/resource-constrained-devices.md) — IoT、無人機、Raspberry Pi 等封閉生態裝置
- [secp256k1 簽章實作](systems/secp256k1-implementations.md) — Bitcoin 與 Ethereum 的 ECDSA 實作生態；31 個實作的稽核結果
- [車載 CAN 匯流排](systems/vehicle-can-bus.md) — 車輛訊號的收集與分享，以及路徑推論攻擊

## CVE（0）

## 會議（2）
- [arXiv 2026](venues/arxiv-2026.md) — 4 篇
- [ACM CCS 2026](venues/ccs-2026.md) — 1 篇

## 查詢（2）
- [資安知識圖譜簡報](queries/2026-11-16-security-graph-slides.md) — 11 張投影片，以知識圖譜的視角走訪三個問題群、對立的邊與缺少的邊 · [開啟簡報](queries/2026-11-16-security-graph-slides.html)
- [當前資安問題簡報](queries/2026-11-16-security-issues-slides.md) — 13 張 HTML 投影片，整理三個問題群、趨勢、開放問題與實務建議 · [開啟簡報](queries/2026-11-16-security-issues-slides.html)
