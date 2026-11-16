---
type: system
title: 資源受限裝置（Resource-Constrained Devices）
category: hardware
vendor:
updated: 2026-11-16
---

# 資源受限裝置（Resource-Constrained Devices）

> 算力、記憶體與能源都有限的嵌入式裝置，例如 IoT 節點、無人機、Raspberry Pi 等級的平台。它們常部署在封閉生態中，需要輕量但長期安全的密碼學。

## 簡介
這類裝置常見於無人機群、工業控制、支付基礎設施等封閉系統，成員與驗證者事先已知，也已經有安全的設定流程（[IT 門檻簽章論文](../papers/2026-wright-it-threshold-dv-signatures.md) p.1）。

## 攻擊面
- **指令完整性**：無人機群若被惡意指令誤導，任務效用會大幅下降（IT 門檻簽章論文 p.1）。
- **單點授權**：單一控制站被入侵，就能發出有效指令；門檻簽章可以要求多方共同授權（p.1）。
- **長期安全**：裝置壽命長，計算安全的簽章可能在生命週期內被量子或其他演算法進展削弱（p.1）。

## 已知攻擊
目前 wiki 沒有收錄針對此類裝置的攻擊論文。

## 防禦機制
| 防禦／論文 | 年份 | 對抗的攻擊 | 部署狀況 |
|---|---|---|---|
| [IT 門檻指定驗證者簽章](../papers/2026-wright-it-threshold-dv-signatures.md) | 2026 | 偽造、單點入侵、量子攻擊者 | 學術原型；Raspberry Pi 4 上簽章 ≤ 232 ms、驗證 ≤ 2 s |
| Threshold ML-DSA | 2026 | 偽造（後量子計算安全） | 學術原型；作為 IT 門檻簽章論文的比較基準 |

## 相關 CVE
目前沒有。

## 相關論文
- [IT 門檻簽章](../papers/2026-wright-it-threshold-dv-signatures.md)
