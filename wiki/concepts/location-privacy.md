---
type: concept
title: 位置隱私（Location Privacy）
aliases: [location privacy, 行蹤隱私]
parent: []
related: [route-inference-attack, zero-permission-sensor-leakage, colocation-inference]
paper_count: 2
updated: 2026-11-16
---

# 位置隱私（Location Privacy）

> 保護一個人或一輛車「在哪裡、去過哪裡、跟誰在一起」，不被未經授權地取得或推論。

## 定義
位置隱私涵蓋兩種威脅：一是直接取得顯式位置資料（GPS 或網路定位）；二是從看似與位置無關的訊號**間接推論**位置或行程。目前 wiki 收錄的論文都屬於第二種：資料發布者或作業系統以為「沒給位置就安全」，但其他訊號仍攜帶足夠資訊。

## 運作原理／子類型
| 推論來源 | 推論結果 | 代表論文 |
|---|---|---|
| 車輛 CAN 訊號（速度、方向盤角）＋道路圖 | 行駛路徑與每秒位置 | [CANTraj](../papers/2026-wang-cantraj.md) |
| 手機磁力計量到的列車牽引電流磁場 | 是否搭電車、哪些人同車 | [列車磁場隱私洩漏](../papers/2026-czybik-train-magnetometer-privacy.md) |

常見的防禦方向：
- **刪除顯式位置**：例如資料集去掉 GPS。CANTraj 證明這樣不夠（p.12）。
- **訊號劣化**：量化、加雜訊、降取樣。
- **權限控管**：讓非位置感測器也需要權限（列車磁場論文 p.12）。
- **合成或擾動位置**：RAoPT、VehiTrack、TransProtect 等（CANTraj 相關研究，p.2）。

## 發展脈絡
- **車輛訊號推論**：2013 年 Dewri et al. 用速度推論目的地，2014 年 Elastic Pathing 指出「光靠速度就能追蹤你」；2020 年 SPy 用方向盤、BBLT 用煞車；2025 年出現 OPD-II、CAN-Trace（以上依 [CANTraj](../papers/2026-wang-cantraj.md) 的整理，p.1–2）。到 2026 年，CANTraj 首次在大規模資料上統一比較這些方法。
- **手機感測器推論**：慣性感測器已被證實能推論路徑、交通方式、輸入文字與語音（列車磁場論文 p.1）；2026 年[列車磁場論文](../papers/2026-czybik-train-magnetometer-privacy.md)補上原本被忽略的磁力計。

## 相關論文
| 論文 | 年份 | 類型 | 在此概念上的貢獻 |
|---|---|---|---|
| [CANTraj](../papers/2026-wang-cantraj.md) | 2026 | measurement | 刪除 GPS 後，速度＋方向盤＋道路圖仍能在 5 分鐘內把路徑還原到約 40 m 誤差 |
| [列車磁場隱私洩漏](../papers/2026-czybik-train-magnetometer-privacy.md) | 2026 | attack | 免權限的磁力計可以推論搭乘電車與同行者（MCC 0.93 / 0.79） |

## 跨論文觀察：訊號劣化能否防禦？（推論）
兩篇論文對「降低訊號品質」的結論相反：
- **CANTraj**：方向盤量化到 10°、速度量化到 1 m/s，路徑還原率幾乎不變；最嚴苛的劣化下仍有 82.6% 以上（p.11）。
- **列車磁場論文**：只要作業系統把取樣率限制在 35 Hz 以下，就無法擷取 16.7 Hz 成分，攻擊的基礎直接消失（p.12）。

可能原因（推論）：CANTraj 利用的是低頻運動學資訊，再加上道路圖的強約束，量化只增加小幅雜訊；列車磁場攻擊依賴一個特定頻率，降取樣會讓它直接混疊消失。這表示劣化防禦是否有效，取決於攻擊用的是**哪一段頻帶**，以及**有沒有外部先驗**（例如道路圖）。

## 開放問題
- 起點未知時的車輛追蹤，仍缺乏系統性評估（CANTraj p.12）。
- 防禦評估必須納入「針對劣化後訊號重新調整」的適應性攻擊者（CANTraj p.12）。
- 25 Hz 與 50 Hz 鐵路系統的可行性；50 Hz 會與電網頻率混淆（列車磁場論文 p.12）。
- 行動作業系統缺少「衍生位置資訊」的權限模型：沒有拿到位置權限，不代表無法推論位置（推論）。

## 相關概念
- [路徑推論攻擊](route-inference-attack.md)
- [免權限感測器洩漏](zero-permission-sensor-leakage.md)
- [同行推論](colocation-inference.md)
- [交通方式偵測](transport-mode-detection.md)
