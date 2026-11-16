---
type: system
title: 車載 CAN 匯流排（Vehicle CAN Bus）
category: other
vendor:
updated: 2026-11-16
---

# 車載 CAN 匯流排（Vehicle CAN Bus）

> 現代汽車用控制器區域網路（controller area network, CAN）廣播輪速、方向盤角、踏板位置、yaw rate 等訊號，可以透過車上的診斷（OBD）連接埠讀取。這些資料被大量收集與分享，因此成為位置隱私的攻擊面。

## 攻擊面
- **資料收集者**：用量計費保險、車隊遠端資訊系統、售後駕駛輔助系統、公開駕駛資料集，以及量產駕駛輔助系統的研究（[CANTraj](../papers/2026-wang-cantraj.md) p.1）。
- **錯誤的隱私假設**：以為刪除 GNSS 座標就能保護位置，但剩下的車輛訊號仍會透露車輛去過哪裡（CANTraj p.1）。
- **訊號可得性**：幾乎每趟行程都有車速與方向盤角；連續油門踏板只有部分品牌提供；煞車欄位在不同品牌間混合了位置、壓力與扭矩（CANTraj p.4）。

## 已知攻擊
| 攻擊／論文 | 年份 | 手法概念 | 影響 |
|---|---|---|---|
| Dewri et al. | 2013 | [路徑推論](../concepts/route-inference-attack.md)（速度） | 推論目的地 |
| Elastic Pathing | 2014 | 路徑推論（速度） | 只靠速度追蹤 |
| SPy | 2020 | 路徑推論（方向盤對道路曲率） | 原論文 58 趟中成功 41 趟；在 CANTraj 上只還原 20% |
| BBLT | 2020 | 路徑推論（煞車） | 保險情境中的追蹤 |
| OPD-II | 2025 | 自行車模型加 Meili 地圖匹配 | CANTraj 上 5 分鐘中位誤差 42 m |
| CAN-Trace | 2025 | 速度與油門事件的子圖同構 | CANTraj 上幾乎都配對失敗 |
| [CANTraj](../papers/2026-wang-cantraj.md) 基準評估 | 2026 | 統一比較加粒子濾波器與集束搜尋 | 5 分鐘還原 87–88% 的路徑，中位誤差約 40 m |

## 防禦機制
| 防禦／論文 | 年份 | 對抗的攻擊 | 部署狀況 |
|---|---|---|---|
| 刪除 GPS 與起始航向 | — | 路徑推論 | 廣泛使用；CANTraj 證明無效（p.12） |
| 訊號量化與降取樣 | — | 路徑推論 | 在 CANTraj 的設定下幾乎無效（p.11） |
| 合成或擾動位置（TransProtect、Olympus 等） | — | 位置推論 | 論文未明述部署狀況（CANTraj p.2） |

## 相關 CVE
目前沒有。

## 相關論文
- [CANTraj](../papers/2026-wang-cantraj.md)
