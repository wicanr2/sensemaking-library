# 2026-07-23 論文拆解

今日新增 2 篇。兩篇都屬於 Physical AI / LLM + Robotics / Embodied AI 交會附近：一篇聚焦 embodied intermediate representations 如何連接 VLM、VLA 與 world modeling；另一篇聚焦用 visual action interface 讓 video world model 服務 robot manipulation。閱讀邊界均為 arXiv Summary/Abstract + Introduction，未讀 methods / experiments / results。

## 今日選文

1. [RoboInter1.5: A Holistic Intermediate Representation Suite for Embodied World Modeling and Robotic Manipulation](./01-robointer15-intermediate-representations.md)
   - arXiv：2607.18709v2
   - 選擇理由：它把 Physical AI 的資料瓶頸轉成 intermediate representation 與 interface 設計問題，對 VLM reasoning、VLA execution、embodied world model 三者都有連接價值。
   - 閱讀範圍：Summary/Abstract + Introduction（PDF 文字抽取）。

2. [Masked Visual Actions for Unified World Modeling](./02-masked-visual-actions-world-modeling.md)
   - arXiv：2607.19343v1
   - 選擇理由：它把 action 表示為 pixel-space partially revealed trajectory，提供一個把 video model 從 passive observer 轉成 controllable world model 的清楚介面。
   - 閱讀範圍：Summary/Abstract + Introduction（PDF 文字抽取）。

## 今日取捨

今日近期 arXiv 也有多篇 robot planning、UAV navigation、sim-to-real、VLM navigation 等論文；但為了維持「最多 2 篇、品質優先、不硬湊」規則，本日只選兩篇更能累積 Physical AI / embodied world model 核心概念的論文。已避開 repo 中已存在的 arXiv ID（例如 2607.18231v1 已於 2026-07-22 收錄）。

## Commit 狀態

- Commit 狀態：已納入本次 arXiv 論文拆解提交。
