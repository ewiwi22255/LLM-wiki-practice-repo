---
type: paper
title: "On the Same Track: Privacy Leaks in Electric Rail Transport via Magnetic Fields"
authors: [Stefan Czybik, Pia Hanfeld, Konrad Rieck]
year: 2026
venue: CCS 2026
arxiv: "2611.18312"
doi: 10.1145/3830454.3846554
url: https://arxiv.org/abs/2611.18312
source: raw/arxiv-2611.18312.pdf
contribution_type: attack
concepts: [zero-permission-sensor-leakage, transport-mode-detection, colocation-inference, location-privacy, side-channel-attacks]
systems: [android, ios]
cves: []
ingested: 2026-11-16
updated: 2026-11-16
---

# 同一條軌道上：電氣化鐵路的磁場造成乘客隱私洩漏（On the Same Track: Privacy Leaks in Electric Rail Transport via Magnetic Fields）

> 一句話摘要：交流電氣化列車的牽引電流，會在車廂內產生 16.7 Hz 的磁場。任何 App 不需要權限，就能用手機磁力計判斷使用者「正在搭電車」，並比對磁場軌跡推斷「哪些人搭同一班車」。

## 基本摘要

### 研究問題
手機的慣性感測器早已被證實會洩漏隱私，但磁力計（magnetometer）一直被認為主要只量到地球靜態磁場，研究關注很少（p.1）。作者挑戰這個假設：電氣化列車的交流牽引電流穿透車殼，在車廂內形成可量測、且隨加速度、路線與負載變化的磁場「指紋」（p.1）。

### 方法
- **物理基礎**：由安培定律可知，磁通密度與電流成正比；在固定電壓下，電流又與列車瞬時功率成正比，因此磁場振幅反映列車的加速與減速，也受同一條架空線上其他列車影響（p.2–3）。
- **訊號處理**：先取三軸的歐氏範數，讓訊號與手機方向無關；再以 16.7 Hz 帶阻濾波器取出時域軌跡，接著高通、標準化、平滑（p.3–4）。
- **[交通方式偵測（transport mode detection, TMD）](../concepts/transport-mode-detection.md)**：去趨勢、Hann 窗、DFT，計算 16.7 Hz 附近的訊噪比（SNR），超過門檻就判定為搭乘列車，再以多數決穩定結果（p.5）。參數以 Optuna 調整，搜尋範圍由訊號物理推導（p.6）。
- **[同行推論（colocation inference）](../concepts/colocation-inference.md)**：兩種做法，一是以動態時間校正（dynamic time warping, DTW）距離加門檻；二是把兩條軌跡疊成輸入通道，交給輕量 1D CNN 做二元分類，再以多數決彙總（p.5、p.9）。
- **資料集**：482.6 小時、217 次旅程、342 趟、1,979 個站間區段、246 個車站，其中 357 個區段至少有兩支手機同車（p.6）。使用 Sensor Logger App，大多以 100 Hz 記錄；受試者照常使用手機（p.5–6）。

### 主要貢獻
- 指出電氣化列車的磁場是被忽略、且與隱私相關的訊號（p.2）。
- 兩種只需要磁力計的新型隱私攻擊：交通方式偵測與同行推論（p.2）。
- 公開目前已知最大的列車搭乘慣性感測資料集，也是唯一標註是否電氣化的資料集（p.2、p.6）。

### 結果
- **TMD**：不用多數決時 F1 0.94、MCC 0.83；加上多數決後 F1 0.98、MCC 0.93，在相同評估設定下優於既有方法（p.7）。只用 60 s 資料就能達到 MCC 0.91（p.7）。發車後前 300–600 s 的效果最好，因為加速時牽引電流最大（p.7）。
- **同行推論**：重新實作的 Nguyen et al.（DDTW）基準 MCC 只有 0.09；作者的 DTW 方法 MCC 0.50；CNN 方法在發車後前 300 s 達到 F1 0.89、MCC 0.79（p.11）。
- **效率**：CNN 分類一個片段對只要 0.6 ms；整體平均比 DTW 快 16 倍，最長錄音且最高取樣率時快 43 倍（p.11）。
- **列車種類**：區域列車子集的 MCC 達 0.90，但作者認為這是子集較小造成的，沒有證據顯示列車種類有系統性差異（p.10）。

## 資安分析

### 威脅模型
- **攻擊者能力**：控制使用者手機上的某個 App，能在背景持續記錄磁力計資料並上傳伺服器（p.4）。
- **攻擊者位置／存取權限**：例如透過被入侵的第三方函式庫（如廣告商）大規模追蹤使用者，或以被竄改的通訊 App 鎖定特定對象（如威權政府）（p.4）。Android（截至 2026 年 4 月）與 iOS 讀取磁力計都不需要執行期權限；Android 自 API 31 起把感測器取樣率上限設為 200 Hz，iOS 則為 100 Hz，兩者都足以解析 16.7 Hz 訊號（p.3）。
- **前提假設**：受害者搭乘 16.7 Hz 交流供電的列車（奧地利、德國、挪威、瑞典、瑞士）（p.2）。
- **攻擊目標**：在不取得任何位置權限的情況下，推斷受害者是否在搭電車，以及哪些受害者同車。若有一條已知位置的參考軌跡，同行結果就會直接暴露其他使用者的位置（p.4）。
- **不在範圍內**：柴油列車、直流供電的地鐵與輕軌（p.2）；精確定位或辨識特定路線，因為軌跡主要反映時間事件，而非位置（p.4）；目前硬體下的 50 Hz 系統（p.12）。

### 攻擊或防禦類型
攻擊。屬於[免權限感測器洩漏（zero-permission sensor leakage）](../concepts/zero-permission-sensor-leakage.md)，並威脅[位置隱私](../concepts/location-privacy.md)。以環境中物理訊號的副作用推論秘密資訊，可歸入廣義的[旁通道攻擊](../concepts/side-channel-attacks.md)（推論；論文本身定位為「隱私洩漏」）。

### 目標系統
[Android](../systems/android.md) 與 [iOS](../systems/ios.md) 的感測器存取模型。

### 評估方式
- **實驗環境**：CNN 在 NVIDIA A40 上訓練 200 epochs，平均約 45 分鐘（p.10）；執行時間在 AMD EPYC 7713 上只用 CPU 量測（p.10）。
- **資料集／Benchmark**：自行收集的資料集，以旅程為單位切分，30% 作為測試集（p.6）；同行推論重複 10 次獨立切分（p.8）。TMD 的負樣本取自日常活動錄音，以 Google Activity Recognition API 標註（p.6）。
- **比較基準（baseline）**：TMD 與 8 篇既有研究的「train」類別成績比較，並重新實作 Skretting et al.（p.7）；同行推論以 Nguyen et al. 為基準（p.8）。
- **評估指標**：以 MCC 為主（因類別不平衡），並列 F1 方便與既有研究比較（p.7）。

## 實務影響

論文未回報 CVE。其他實務影響：
- **對作業系統的建議**（p.12）：
  1. 讀取磁力計及類似感測器應需要明確權限；
  2. 把開放給 App 的取樣率限制在 35 Hz 以下，就無法擷取 16.7 Hz 成分，但可能影響計步、手勢偵測等功能；
  3. 兩者結合：App 要求高到足以做頻譜分析的取樣率時，才需要特殊權限。
- **地理資訊外洩**：16.7 Hz 只在特定國家使用，所以軌跡中出現這個頻率本身就暴露了使用者所在的區域（p.12）。
- **正面應用**：可作為 TMD 系統的輔助特徵，也可用於車廂內的接觸追蹤（p.12）。
- **資料與倫理**：資料集 137 GB，公開於 Zenodo（10.5281/zenodo.22206115），車站名稱已匿名化、時間戳已轉為相對時間（p.6）。研究機構沒有 IRB，改依循 Menlo Report 原則（p.14）。
- 廠商回應：論文未明述。

## 相關頁面
- 概念：[免權限感測器洩漏](../concepts/zero-permission-sensor-leakage.md)、[交通方式偵測](../concepts/transport-mode-detection.md)、[同行推論](../concepts/colocation-inference.md)、[位置隱私](../concepts/location-privacy.md)、[旁通道攻擊](../concepts/side-channel-attacks.md)
- 系統：[Android](../systems/android.md)、[iOS](../systems/ios.md)
- 會議：[CCS 2026](../venues/ccs-2026.md)
- 相關論文：
  - [CANTraj](2026-wang-cantraj.md)：同樣證明不用 GPS 也能推論行程。兩篇對「降低訊號品質」能否防禦的結論相反，見[位置隱私](../concepts/location-privacy.md)頁的比較（推論）。
