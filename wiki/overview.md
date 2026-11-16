---
type: overview
title: 資安研究綜覽
updated: 2026-11-16
---

# 資安研究綜覽

> 個人資安研究地圖，隨著每次 ingest 持續更新。分群由 agent 依已讀論文歸納，不是預設的分類。

## 領域地圖

### 1. 非位置訊號的隱私推論
沒有拿到 GPS 或位置權限，不代表沒有位置資訊。
- [位置隱私](concepts/location-privacy.md)：上位議題
  - [路徑推論攻擊](concepts/route-inference-attack.md)：以車輛 CAN 訊號推論路徑。論文：[CANTraj](papers/2026-wang-cantraj.md)
  - [免權限感測器洩漏](concepts/zero-permission-sensor-leakage.md)：手機慣性感測器。論文：[列車磁場隱私洩漏](papers/2026-czybik-train-magnetometer-privacy.md)
    - [交通方式偵測](concepts/transport-mode-detection.md)
    - [同行推論](concepts/colocation-inference.md)
- 相關系統：[車載 CAN 匯流排](systems/vehicle-can-bus.md)、[Android](systems/android.md)、[iOS](systems/ios.md)

### 2. 數位簽章的安全基礎
- [數位簽章](concepts/digital-signatures.md)：上位概念
  - [ECDSA nonce 安全](concepts/ecdsa-nonce-security.md)：RFC 6979 依賴未證明的假設 A*。論文：[RFC 6979 確定性 ECDSA](papers/2026-prosianoi-rfc6979-deterministic-ecdsa.md)
  - [門檻簽章](concepts/threshold-signatures.md)與[資訊理論安全](concepts/information-theoretic-security.md)。論文：[IT 門檻簽章](papers/2026-wright-it-threshold-dv-signatures.md)
- 相關系統：[secp256k1 簽章實作](systems/secp256k1-implementations.md)、[資源受限裝置](systems/resource-constrained-devices.md)

### 3. 微架構旁通道與編譯器防禦
- [旁通道攻擊](concepts/side-channel-attacks.md)：上位概念，也連結第 1、2 群
  - [Spectre](concepts/spectre.md)，以及兩種防禦手段：[推測載入強化（SLH）](concepts/speculative-load-hardening.md)、[推測常數時間（SCT）](concepts/speculative-constant-time.md)。論文：[VellSSLH](papers/2026-priya-vellsslh.md)
- 相關系統：[LLVM](systems/llvm.md)

### 跨群連結
- [旁通道攻擊](concepts/side-channel-attacks.md)串起三群：微架構（Spectre）、nonce 外洩（ECDSA）、環境物理訊號（列車磁場）。
- 「密碼學實作安全」同時出現在第 2 群（nonce 產生）與第 3 群（推測執行），兩篇都以真實密碼學函式庫為評估對象（推論）。

## 近期趨勢
以下皆為推論，目前樣本只有 5 篇，都在 2026-11 上傳。
- **大規模統一基準取代各說各話的評估**：CANTraj 在同一批片段上重新實作並比較 6 種攻擊，並把失敗計入指標；列車磁場論文則刻意固定軌跡長度，避免高估基準方法。重新檢驗既有攻擊的方法論正受到重視。
- **形式化方法進入實務工具鏈**：VellSSLH 寫了 33 萬行 Rocq 證明，套用到 LLVM；RFC 6979 論文則替已部署十多年的標準補上缺少的安全假設分析。
- **重新檢視「被認為無害」的資料**：GPS 已刪除的 CAN 紀錄、免權限的磁力計，原本都被視為安全，結果都能推論位置。
- **超越計算假設**：後量子與資訊理論安全的方案開始比較在資源受限裝置上的實測成本。
- **生成式 AI 進入研究流程**：五篇中有三篇宣告用 LLM 協助實作、證明或排版，另有一篇把 LLM agent 當成評估對象。

## 開放問題總表
| 問題 | 來源概念 |
|---|---|
| 起點未知的車輛追蹤；保護機制要對抗適應性攻擊者 | [路徑推論攻擊](concepts/route-inference-attack.md) |
| 訊號劣化防禦何時有效、何時無效（取決於頻帶與外部先驗） | [位置隱私](concepts/location-privacy.md) |
| 行動作業系統的感測器權限與取樣率要如何取捨 | [免權限感測器洩漏](concepts/zero-permission-sensor-leakage.md) |
| 用標準模型證明或推翻 HMAC_DRBG 滿足 A* | [ECDSA nonce 安全](concepts/ecdsa-nonce-security.md) |
| 256 位元曲線上一位元 nonce 外洩的實際攻擊成本 | [ECDSA nonce 安全](concepts/ecdsa-nonce-security.md) |
| 資訊理論簽章在驗證者人數多時的實用性 | [門檻簽章](concepts/threshold-signatures.md) |
| IR 層的 SCT 保證能否延伸到機器碼（後端與硬體假設） | [推測常數時間](concepts/speculative-constant-time.md) |
| 短函數的入口屏障固定成本 | [推測載入強化](concepts/speculative-load-hardening.md) |

## 閱讀統計
- 論文總數：5
- 依類型：attack 1 · defense 2 · measurement 1 · analysis 1 · sok 0 · tool 0 · other 0
- 依年份：2026（5）
- 依發表狀態：CCS 2026（1）· arXiv 預印本（4）
