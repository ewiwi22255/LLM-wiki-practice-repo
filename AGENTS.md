# AGENTS.md — 資安論文 LLM Wiki

本文件是這個知識庫的 schema。它定義 wiki 的結構、頁面格式與維護流程。Agent（Claude Code）在每次操作前都必須遵守本文件。

設計參考 Karpathy 的 LLM Wiki 模式：原始資料不可變，由 LLM 持續把知識「編譯」成互相連結的 Markdown 頁面，讓理解隨著閱讀累積，而不是每次查詢都從原文重新推導。

---

## 1. 專案目的

- **用途**：持續追蹤資安研究動態，建立個人的資安研究地圖。
- **範圍**：資安全領域，不預設子領域。分類（概念、系統）隨 ingest 的論文自然長出來。
- **使用者**：一位研究者，主要透過詢問 agent 來使用 wiki，較少直接瀏覽檔案。

---

## 2. 三層架構

| 層 | 位置 | 擁有者 | 規則 |
|---|---|---|---|
| 原始資料 | `raw/` | 使用者 | **不可修改、不可刪除、不可重新命名**。Agent 只能新增（下載 arXiv PDF）與讀取。 |
| Wiki | `wiki/` | Agent | Agent 全權建立與維護。使用者閱讀，必要時提出修改。 |
| Schema | `AGENTS.md` | 共同 | 使用者與 agent 討論後共同演進。Agent 不得自行修改，需先提議並取得同意。 |

---

## 3. 目錄結構與命名規則

```
sec-paper-wiki/
├── AGENTS.md
├── CLAUDE.md                  # 引用本檔
├── raw/                       # 原始 PDF
└── wiki/
    ├── index.md               # 全部頁面目錄
    ├── log.md                 # 操作日誌（append-only）
    ├── overview.md            # 領域綜覽 / 研究地圖
    ├── papers/                # 論文頁
    ├── concepts/              # 概念 / 技術頁
    ├── systems/               # 目標系統頁
    ├── cves/                  # CVE / 漏洞頁
    ├── venues/                # 會議 / 年度頁
    └── queries/               # 查詢結果歸檔
```

### 檔名規則

所有 wiki 檔名一律使用**小寫 ASCII、kebab-case**（CVE 編號除外），不使用中文或空白。

| 類型 | 格式 | 範例 |
|---|---|---|
| raw（arXiv 下載） | `raw/arxiv-<id>.pdf` | `raw/arxiv-2501.12345.pdf` |
| raw（使用者放入） | 保留原檔名，不得更改 | `raw/SyzRetro_final.pdf` |
| 論文 | `papers/<年份>-<第一作者姓氏>-<短標題>.md` | `papers/2025-chen-kernel-fuzzing-retrospective.md` |
| 概念 | `concepts/<英文術語>.md` | `concepts/coverage-guided-fuzzing.md` |
| 系統 | `systems/<系統名>.md` | `systems/linux-kernel.md`、`systems/intel-sgx.md` |
| CVE | `cves/CVE-<年>-<編號>.md` | `cves/CVE-2024-12345.md` |
| 會議 | `venues/<會議>-<年份>.md` | `venues/usenix-security-2025.md`、`venues/arxiv-2025.md` |
| 查詢 | `queries/<YYYY-MM-DD>-<主題>.md` | `queries/2026-11-16-llm-fuzzing-comparison.md` |

- 短標題取 2–5 個英文關鍵字；若論文有系統名稱（如 SyzRetro），優先使用。
- 同名衝突時在結尾加 `-2`、`-3`。
- 尚未正式發表的 arXiv 論文，venue 記為 `arXiv`，歸入 `venues/arxiv-<年份>.md`。日後若正式發表，更新 frontmatter 並移到正確的 venue 頁。

---

## 4. 頁面通則

### 4.1 語言

- 內文使用**繁體中文（台灣用語）**。
- 專有名詞**在每一頁第一次出現時**附英文，格式為「中文（English）」，例如「模糊測試（fuzzing）」、「旁通道攻擊（side-channel attack）」。之後可只用中文。
- 沒有通行中文譯名的名詞（如 Spectre、Rowhammer、syzkaller）直接使用英文，不硬譯。
- 論文標題保留英文原文；頁面 H1 使用「中文意譯（English Title）」。

### 4.2 Frontmatter

每個 wiki 頁面（`index.md`、`log.md` 除外）都必須有 YAML frontmatter，至少包含 `type`、`title`、`updated`。各類型的完整欄位見第 5 節。frontmatter 中的連結欄位（`concepts`、`systems`、`cves`）**一律填寫檔名 slug**（不含副檔名與路徑），方便 grep。

### 4.3 連結

- 使用**標準 Markdown 相對連結**，路徑相對於目前檔案：
  - 從 `papers/` 連到概念：`[模糊測試（fuzzing）](../concepts/fuzzing.md)`
  - 從 `wiki/overview.md` 連到論文：`[SyzRetro](papers/2025-chen-syzretro.md)`
- 不使用 `[[wikilink]]`。
- 每個論文頁至少連到 1 個概念頁與它的 venue 頁。
- 連結必須雙向：A 頁連到 B 時，B 頁的「相關論文」或「相關頁面」區塊也要列出 A。

### 4.4 可追溯性與誠實

- 論文頁中的事實陳述必須能在原文找到，關鍵數字與主張附上原文頁碼，例如「誤報率降低 37%（p.9）」。
- 論文沒有說明的欄位，填「論文未明述」，**不得臆測補齊**。
- Agent 自己的推論或跨論文綜合，標註「（推論）」。
- 使用 wiki 與 raw 以外的資訊（例如網路搜尋 CVE 狀態）時，附上來源 URL 與查詢日期。

### 4.5 矛盾處理

新資訊與既有頁面衝突時，**不得直接覆蓋舊說法**。兩種說法並列，加上提示區塊：

```markdown
> **⚠ 矛盾**：[論文 A](../papers/a.md) 指出 X（p.5），但 [論文 B](../papers/b.md) 的實驗顯示 Y（p.7）。可能原因：評估環境不同（推論）。
```

### 4.6 研究倫理

本 wiki 以理解研究為目的。攻擊技術記錄到論文層級的原理與條件即可，不在 wiki 中撰寫可直接武器化的 exploit 程式碼。

---

## 5. 頁面模板

### 5.1 論文頁 `papers/*.md`

```markdown
---
type: paper
title: "Original English Title"
authors: [First Author, Second Author]
year: 2025
venue: USENIX Security 2025        # 未發表則填 arXiv
arxiv: "2501.12345"                # 無則留空
doi:                               # 無則留空
url: https://arxiv.org/abs/2501.12345
source: raw/arxiv-2501.12345.pdf
contribution_type: attack          # attack | defense | measurement | analysis | sok | tool | other
concepts: [coverage-guided-fuzzing, kernel-fuzzing]
systems: [linux-kernel]
cves: [CVE-2024-12345]
ingested: 2026-11-16
updated: 2026-11-16
---

# 中文意譯標題（Original English Title）

> 一句話摘要：這篇論文做了什麼、為什麼重要。

## 基本摘要

### 研究問題
### 方法
### 主要貢獻
- 條列，2–5 點
### 結果
- 關鍵數字附頁碼

## 資安分析

### 威脅模型
- **攻擊者能力**：
- **攻擊者位置 / 存取權限**：
- **前提假設**：
- **攻擊目標**：
- **不在範圍內**：

### 攻擊或防禦類型
（攻擊 / 防禦 / 量測 / 分析 / SoK，並說明具體手法類別，連到概念頁）

### 目標系統
（連到系統頁，註明版本 / 硬體 / 平台）

### 評估方式
- **實驗環境**：
- **資料集 / Benchmark**：
- **比較基準（baseline）**：
- **評估指標**：

## 實務影響

| CVE | 漏洞摘要 | 廠商回應 | 修補狀態 |
|---|---|---|---|
| [CVE-2024-12345](../cves/CVE-2024-12345.md) | | | |

- 未發現 CVE 時寫「論文未回報 CVE」，並記錄其他實務影響（如漏洞回報數、被採用的程式碼、廠商致謝）。

## 相關頁面
- 概念：
- 系統：
- 會議：
- 相關論文：（延續 / 比較 / 衝突，並各用一句話說明關係）
```

### 5.2 概念 / 技術頁 `concepts/*.md`

```markdown
---
type: concept
title: 中文名稱（English Name）
aliases: [英文縮寫, 其他稱呼]
parent: [上位概念 slug]            # 例如 kernel-fuzzing 的 parent 是 fuzzing
related: [相關概念 slug]
paper_count: 3
updated: 2026-11-16
---

# 中文名稱（English Name）

> 一句話定義。

## 定義
## 運作原理 / 子類型
## 發展脈絡
（依時間排列的演進，每一步連到對應論文）

## 相關論文
| 論文 | 年份 | 類型 | 在此概念上的貢獻 |
|---|---|---|---|

## 開放問題
（研究缺口與尚未解決的挑戰，標註來源論文或「（推論）」）

## 相關概念
```

**建立新概念頁的門檻**（避免頁面爆炸）：
- 該概念是某篇論文的**核心**技術或攻擊類型，或
- 該概念已在 **2 篇以上**論文中出現。

否則只在論文頁內文提及，不另開頁。

### 5.3 目標系統頁 `systems/*.md`

```markdown
---
type: system
title: 系統名稱
category: os | browser | hardware | cloud | mobile | network | ml-model | protocol | other
vendor:
updated: 2026-11-16
---

# 系統名稱

> 一句話簡介，以及它在資安研究中為何重要。

## 攻擊面
## 已知攻擊
| 攻擊 / 論文 | 年份 | 手法概念 | 影響 |
|---|---|---|---|

## 防禦機制
| 防禦 / 論文 | 年份 | 對抗的攻擊 | 部署狀況 |
|---|---|---|---|

## 相關 CVE
## 相關論文
```

### 5.4 CVE 頁 `cves/CVE-*.md`

```markdown
---
type: cve
title: CVE-2024-12345
affected: [system slug]
affected_versions:
severity:                          # CVSS 分數 / 等級，未知則留空
status: patched                    # unpatched | patched | partially-patched | disputed | wontfix | unknown
vendor_response:                   # 一句話
discovered_in: [paper slug]
last_checked: 2026-11-16           # 最後一次確認修補狀態的日期
updated: 2026-11-16
---

# CVE-2024-12345：簡短描述

## 漏洞描述
## 影響範圍
## 時間軸
- YYYY-MM-DD：通報廠商
- YYYY-MM-DD：修補釋出
## 修補狀態與廠商回應
## 來源
（論文頁碼、NVD / 廠商公告 URL 與查詢日期）
```

### 5.5 會議 / 年度頁 `venues/*.md`

```markdown
---
type: venue
title: USENIX Security 2025
venue: USENIX Security
year: 2025
updated: 2026-11-16
---

# USENIX Security 2025

## 已收錄論文
| 論文 | 類型 | 主要概念 |
|---|---|---|

## 本屆觀察
（已讀論文中呈現的主題分布與趨勢，標註「（推論）」）
```

### 5.6 查詢頁 `queries/*.md`

```markdown
---
type: query
title: 問題的簡短標題
question: "使用者的原始問題"
sources: [wiki 頁面路徑]
date: 2026-11-16
updated: 2026-11-16
---

# 問題的簡短標題

## 問題
## 回答
（與對話中的回答相同，保留表格與比較內容）

## 依據
（引用的 wiki 頁面與原文頁碼）

## 延伸問題
（值得後續追問或 ingest 的方向）
```

### 5.7 綜覽頁 `overview.md`

overview 是整個 wiki 的研究地圖，包含：

1. **領域地圖**：依研究方向分群，各群列出核心概念頁連結。分群由 agent 依已讀論文歸納，隨 ingest 調整。
2. **近期趨勢**：從已讀論文觀察到的方向（標註「（推論）」）。
3. **開放問題總表**：從各概念頁彙整的主要研究缺口。
4. **閱讀統計**：論文總數、各類型數量、各年份數量。

---

## 6. index.md 與 log.md

### index.md

全部 wiki 頁面的目錄。Agent 查詢時**先讀 index.md** 來定位相關頁面。每次 ingest 或新增頁面後都要更新。

格式：依類型分節，每行一個頁面。

```markdown
## 論文（N）
- [中文標題](papers/2025-chen-syzretro.md) — 一句話摘要 · USENIX Sec 2025 · attack

## 概念（N）
- [模糊測試（fuzzing）](concepts/fuzzing.md) — 一句話定義 · 5 篇
```

論文依年份新到舊排列，其他類型依字母排列。

### log.md

append-only 的操作日誌，**只能在檔尾新增，不得修改舊紀錄**。每筆以固定前綴開頭，方便用 `grep "^## \[" wiki/log.md | tail -10` 查看最近操作。

```markdown
## [2026-11-16] ingest | SyzRetro: Retrofitting Kernel Fuzzers
- 新增：papers/2025-chen-syzretro.md、concepts/kernel-fuzzing.md
- 更新：concepts/fuzzing.md、systems/linux-kernel.md、overview.md
- 備註：與 2024-xx 論文在覆蓋率指標上有矛盾，已標註
```

操作類型：`init`、`ingest`、`query`、`lint`、`schema`（AGENTS.md 變更）、`fix`（手動修正）。

---

## 7. 操作流程

### 7.1 Ingest

**觸發**：使用者貼 arXiv 連結、說「ingest」、「處理 raw 裡的新論文」等。

**模式**：
- **預設自動模式**：直接處理到完成，最後回報變更摘要。可一次處理多篇（依序逐篇處理）。
- **互動模式**：使用者說「這篇跟我討論」、「討論模式」時啟用。讀完論文後先停下，向使用者報告重點與預計建立 / 更新的頁面，討論並確認後才寫入。

**步驟**：

1. **取得原文**
   - **arXiv 連結**：從 `arxiv.org/abs/<id>`、`arxiv.org/pdf/<id>` 等格式解析出 ID（去掉版本號 `v2` 等，下載最新版）。
     - 先查重：`grep -rl "arxiv: \"<id>\"" wiki/papers/`，已存在就告知使用者並跳過（除非使用者要求重新處理）。
     - 下載：`curl -L -o raw/arxiv-<id>.pdf https://arxiv.org/pdf/<id>`
     - 驗證：檔案開頭必須是 `%PDF`，否則回報失敗，不得繼續。
     - 中繼資料（標題、作者、日期）可從 `https://export.arxiv.org/api/query?id_list=<id>` 取得。
   - **raw/ 中的 PDF**：找出尚未 ingest 的檔案，也就是 `raw/` 中所有沒有出現在任何 `wiki/papers/*.md` 的 `source:` 欄位裡的檔案。
2. **閱讀論文**：用 Read 工具讀 PDF，每次最多 20 頁，長論文分段讀（`pages: "1-20"`、`"21-40"`⋯）。必讀：摘要、introduction、威脅模型、設計、評估、討論 / 限制、結論；appendix 視需要閱讀。
3. **互動模式停在這裡**，報告重點並等待確認。
4. **撰寫論文頁**：依 5.1 模板，填完所有欄位。
5. **更新彙整頁**：
   - 概念頁：依 5.2 的門檻建立新頁，或在既有頁的「發展脈絡」、「相關論文」、「開放問題」加入新內容。
   - 系統頁：更新「已知攻擊」或「防禦機制」表格。
   - CVE 頁：論文回報的每個 CVE 建一頁（或更新既有頁），`last_checked` 設為今天。
   - 會議頁：加入論文列表，沒有就建立。
   - 檢查新論文與既有頁面是否矛盾，依 4.5 標註。
6. **更新 overview.md**：新論文改變領域地圖、趨勢或開放問題時才更新；閱讀統計每次都要更新。
7. **更新 index.md**，並在 **log.md** 新增紀錄。
8. **回報**：列出新增與更新的頁面，用 3–5 行說明這篇論文對 wiki 帶來的新知識，並提供建議的 commit 訊息（例如 `ingest: SyzRetro (2025)`）。

一篇論文通常會觸及 5–15 個頁面。寧可多更新既有頁面、建立好交叉連結，也不要只寫一篇孤立的論文頁。

### 7.2 Query

**觸發**：使用者對 wiki 內容提出問題。

**步驟**：

1. 先讀 `wiki/index.md` 找出相關頁面，再讀這些頁面。
2. wiki 資訊不足時，回到 `raw/` 的原文 PDF 查證。
3. 在對話中回答，引用 wiki 頁面連結；適合時用比較表格呈現。
4. wiki 與 raw 都沒有答案時，明確說明「wiki 中沒有相關資料」。可以補充一般知識，但要標註「（wiki 外知識）」，並建議可以 ingest 哪些論文。
5. **每次查詢都歸檔**：依 5.6 模板寫入 `wiki/queries/`。
6. 回答中若產生新的綜合洞見（例如跨論文比較、新發現的關聯），同步寫回對應的概念頁或 overview，並連回查詢頁。
7. 更新 `index.md`，並在 `log.md` 新增紀錄。

### 7.3 Lint

**觸發**：使用者說「lint」、「健康檢查」。

**檢查項目**：

| # | 項目 | 處理方式 |
|---|---|---|
| 1 | 失效的相對連結 | 自動修正 |
| 2 | 單向連結（A→B 但 B 沒列 A） | 自動補上 |
| 3 | index.md 與實際檔案不一致 | 自動修正 |
| 4 | frontmatter 缺少必填欄位 | 自動補上可確定的欄位，無法確定的列入報告 |
| 5 | `raw/` 中尚未 ingest 的 PDF | 列入報告 |
| 6 | 孤立頁面（沒有任何頁面連入） | 建議連結位置 |
| 7 | 已在 2 篇以上論文出現、但沒有概念頁的概念 | 建議建立 |
| 8 | 頁面間的矛盾或已過時的說法 | 列入報告，由使用者決定 |
| 9 | CVE 頁 `last_checked` 超過 90 天，或 `status` 為 `unpatched` / `unknown` | 列入報告；使用者同意後上網查證並更新 |
| 10 | overview 與現有內容脫節 | 建議更新 |
| 11 | 下一步閱讀建議 | 根據開放問題、被多篇引用但尚未 ingest 的論文，建議 3–5 篇 |

**步驟**：執行檢查 → 套用自動修正 → 在對話中輸出報告（分成「已自動修正」與「需要你決定」兩部分）→ 在 `log.md` 新增紀錄。

---

## 8. Git

- 專案已 `git init`。**Agent 不主動 commit**，由使用者決定什麼時候 commit。每次操作結束時提供建議的 commit 訊息。
- `raw/*.pdf` 已列入 `.gitignore`，PDF 不進版本控制。arXiv 論文可以從 frontmatter 的 `url` 重新下載。
- commit 訊息格式：`<操作>: <摘要>`，例如 `ingest: SyzRetro (2025)`、`query: LLM fuzzing 比較`、`lint: 修正 12 個連結`。

---

## 9. 使用者常用指令範例

| 說法 | 動作 |
|---|---|
| `ingest https://arxiv.org/abs/2501.12345` | 下載並自動 ingest |
| `ingest 這三篇：<連結1> <連結2> <連結3>` | 批次自動 ingest |
| `處理 raw 裡的新 PDF` | ingest 所有尚未處理的 PDF |
| `ingest <連結>，這篇跟我討論` | 互動模式 ingest |
| `目前 kernel fuzzing 有哪些主要方法？` | Query，並歸檔 |
| `比較 A 跟 B 的威脅模型` | Query，以比較表回答，並歸檔 |
| `lint` | 健康檢查 |
| `最近做了什麼？` | 讀 log.md 最近的紀錄並摘要 |
