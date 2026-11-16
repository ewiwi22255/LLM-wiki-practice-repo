---
type: paper
title: "CANTraj: Recovering Vehicle Trajectories from CAN Signals in Naturalistic Driving"
authors: [Yuhang Wang, Jingxin Yang, Chuheng Wei, Suyang Chen, Haozhuang Chi, Hao Zhou]
year: 2026
venue: arXiv
arxiv: "2611.18442"
doi:
url: https://arxiv.org/abs/2611.18442
source: raw/arxiv-2611.18442.pdf
contribution_type: measurement
concepts: [route-inference-attack, location-privacy]
systems: [vehicle-can-bus]
cves: []
ingested: 2026-11-16
updated: 2026-11-16
---

# CANTraj：從自然駕駛的 CAN 訊號還原車輛軌跡（CANTraj: Recovering Vehicle Trajectories from CAN Signals in Naturalistic Driving）

> 一句話摘要：移除 GPS 不等於移除位置資訊。本文釋出第一個大規模、CAN 與 GNSS 時間對齊的駕駛資料集與基準，在統一條件下重新實作並比較 6 種既有路徑推論攻擊，顯示「速度＋方向盤＋道路圖」可在 5 分鐘內把車輛軌跡還原到數十公尺的誤差。

## 基本摘要

### 研究問題
開放駕駛資料集與用量計費保險（usage-based insurance, UBI）常以「刪除 GPS 紀錄」宣稱保護隱私，但車輛訊號本身仍攜帶位置資訊（p.1）。既有的[路徑推論攻擊（route inference attack）](../concepts/route-inference-attack.md)各自使用不同的先驗假設、評估指標與小型資料集（例如 SPy 58 趟、OPD-II 59 條軌跡、Elastic Pathing 945 條），結果無法互相比較（p.1）。本文提出三個研究問題（p.1）：
- **RQ1**：既有方法能多準確地還原軌跡？哪些 CAN 訊號有幫助？
- **RQ2**：起始航向、線上／離線存取、行程長度如何影響準確度？
- **RQ3**：失敗原因為何？準確度、計算成本與對劣化訊號的抵抗力如何取捨？

### 方法
- **資料集**：CANTraj 含 3,940 趟、454 位駕駛的 CAN 解碼訊號與時間對齊的 GNSS 軌跡；CANTraj-Bench 使用其中 3,675 趟（431 位駕駛、266 種車輛平台、27 個品牌、1,380 小時、85,919 km），定義 97,340 個評估片段（p.1、p.3）。資料由駕駛自己車上的售後駕駛輔助系統記錄，並經去識別化（p.3）。
- **評估協定**：三個先驗獨立切換——線上（O）／離線（F）、是否提供 OpenStreetMap 道路圖（M/N）、是否提供起始航向（H+/H−）；起點位置一律提供（p.2）。
- **重新實作 6 種已發表攻擊**：MacroTracking、OPD-II、Elastic Pathing、CAN-Trace、Dewri et al.、SPy，並用原作者的樣本資料驗證 MacroTracking 與 OPD-II 的實作（p.4）。
- **基準方法**：航位推算（dead reckoning）、道路圖粒子濾波器（particle filter，2,000 個粒子）、離線集束搜尋（beam search，B=200、K=10）、學習式 GRU 運動模型（p.6）。
- **LLM 候選選擇**：以 Qwen3-14B agent 在多個重建工具的候選路徑中挑選（p.11）。
- **指標**：平均位移誤差（ADE）、100 m 內覆蓋率（c100）、路徑還原（path recall@50 m ≥ 0.8）；沒有輸出的片段計為失敗（ADE = ∞），不從分母剔除（p.3）。

### 主要貢獻
- 釋出 CAN–GNSS 配對資料集與基準，涵蓋數百位駕駛與車款（p.1）。
- 在相同片段上以配對比較統一評估 6 種攻擊與多種基準方法，並記錄每種方法的資訊需求（p.2）。
- 失敗分析：方向盤訊號貢獻最大；不同方法在不同片段失敗；輕度量化幾乎無法阻擋攻擊（p.2）。

### 結果
- **沒有道路圖時**全部方法都會在 5 分鐘內嚴重漂移：方向盤航位推算的中位 ADE 為 921 m（測試集），不比用速度畫直線（735 m）好（p.6）。
- **加入道路圖後**誤差降低超過一個數量級：線上粒子濾波器 43 m、離線集束搜尋 37 m，路徑還原率 87% 與 88%（p.6）。
- **方向盤是關鍵訊號**：在已知起點與航向下，速度加上方向盤讓粒子濾波器的 5 分鐘路徑還原率從 56% 升到 87%（p.1）；只用速度時中位誤差為 286 m（p.9）。連續油門踏板訊號幾乎沒有額外幫助（p.9）。
- **起始航向**：不給航向時，粒子濾波器中位誤差從 43 m 升到 138 m，離線集束搜尋只從 37 m 升到 41 m，路徑還原率從 88% 降到 81%（p.9）。
- **已發表攻擊**：OPD-II 42 m（與粒子濾波器的配對中位差異不顯著）、MacroTracking 84 m、Elastic Pathing 129 m；CAN-Trace 在 245 個測試片段中只配對成功 2 個；SPy 只還原 20% 的路徑（p.6–7）。
- **失敗互補**：逐片段挑選十種道路圖方法中最好的一種，可還原 98% 的路徑（集束搜尋為 88%），全部方法都失敗的只有 2.3%（p.10）。
- **訊號劣化幾乎無效**：方向盤角量化到 10°、加 5° 偏移或降到 1 Hz，路徑還原率最多只變動 1.3 個百分點；即使在最嚴苛的劣化下，攻擊仍能還原至少 82.6% 的路徑（p.11）。
- **LLM 選擇器**：在測試駕駛上，集束搜尋 69%、監督式邏輯迴歸選擇器 75%、Qwen3-14B agent 75%，但 agent 每個片段的串行成本為 36.4 s，選擇器只要 1.1 s（p.12）。延伸版 agent 在測試集達 78%、few-shot 81%（p.12）。摘要寫的「79% / 83%」與表 IV 數字略有出入（論文未說明原因）。
- **推論成本低**：5 分鐘片段的中位推論時間，集束搜尋 0.24 s、粒子濾波器 0.57 s（p.11）。
- **排名不穩定**：在另一組 12 位駕駛、單一車款、單一都會區的控制資料上，MacroTracking 反而最好（19 m），作者提醒不要把排名普遍化（p.8）。

## 資安分析

### 威脅模型
- **攻擊者能力**：取得已刪除 GPS 的 CAN 訊號紀錄（車速、方向盤角、踏板、yaw rate），並能使用公開的 OpenStreetMap 道路圖（p.1–2）。
- **攻擊者位置／存取權限**：駕駛資料的接收方，例如開放資料集的使用者、UBI 保險公司、車隊遠端資訊系統、售後駕駛輔助系統業者（p.1）。
- **前提假設**：已知起點位置；起始航向依協定提供或不提供；「區域」變體只給以起點為中心的 5 km 正方形（p.2）。
- **攻擊目標**：還原時間窗內的行駛路徑與每秒位置。
- **不在範圍內**：起點未知的追蹤（區域變體不是這種測試）、針對防禦機制調整過的適應性攻擊者；需要交通或道路先驗的 BBLT 與 Zhou et al. 未重新實作（p.5、p.12）。

### 攻擊或防禦類型
量測與攻擊評估（benchmark）。統一評估[路徑推論攻擊](../concepts/route-inference-attack.md)對[位置隱私（location privacy）](../concepts/location-privacy.md)的威脅，手法涵蓋航位推算、地圖匹配（map matching）、粒子濾波與集束搜尋。

### 目標系統
[車載 CAN 匯流排](../systems/vehicle-can-bus.md)，透過 OBD 連接埠擷取；涵蓋 266 種車輛平台、27 個品牌，資料以北美道路為主（p.3、p.12）。

### 評估方式
- **實驗環境**：Intel Core i9-12900K 工作站；LLM 在另一台 H100 主機上執行（p.6）。
- **資料集／Benchmark**：CANTraj-Bench；主要結果來自測試集（43 位駕駛、302 趟）與 few-shot 集（51 位駕駛、474 趟），兩者的駕駛都不曾出現在訓練集（p.4）。
- **比較基準（baseline）**：6 種已發表攻擊，以及航位推算、粒子濾波器、集束搜尋、GRU。
- **評估指標**：ADE、c100、路徑還原率、尾段遺失（tail-lost）、終端偏離（terminal excursion）；以「重抽駕駛」的 bootstrap 計算信賴區間（p.3）。

## 實務影響

論文未回報 CVE。其他實務影響：
- **對資料發布者**：刪除 GPS 或起始航向都無法移除路徑資訊；把方向盤量化到 10° 或把速度量化到 1 m/s，仍無法阻擋這些攻擊。評估保護機制時，必須同時衡量對下游任務的影響，並測試針對修改後訊號調整過的攻擊者（p.12）。
- **已發表程式碼的 bug**：MacroTracking 比較航向時未在正北處做角度環繞；OPD-II 對負角度 b 寫成 360 − b 而非 360 + b。主結果保留這兩個 bug，以忠實重現原始程式碼的行為（p.5）。
- **開放資料**：資料與程式碼公開於 Hugging Face（HenryYHW/CANTraj）與 GitHub（WangYuHang-cmd/GPS--CAN）（p.12）。

## 相關頁面
- 概念：[路徑推論攻擊](../concepts/route-inference-attack.md)、[位置隱私](../concepts/location-privacy.md)
- 系統：[車載 CAN 匯流排](../systems/vehicle-can-bus.md)
- 會議：[arXiv 2026](../venues/arxiv-2026.md)
- 相關論文：
  - [電氣化鐵路磁場的隱私洩漏](2026-czybik-train-magnetometer-privacy.md)：同樣證明「不使用顯式位置資料」不等於保護位置隱私；兩篇都用非位置訊號推論行程。不過對訊號劣化防禦的結論不同，見[位置隱私](../concepts/location-privacy.md)頁的比較（推論）。
