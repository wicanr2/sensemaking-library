# 2026-06-29 論文拆解

今日選文 2 篇；本次只讀 arXiv Summary/Abstract 與 Introduction，未讀全文其他章節。兩篇都屬於 VLA / Robotics / Embodied AI 交會方向，且 arXiv ID 未在 repo 中重複。

## 今日選文

1. [E-TTS: A New Embodied Test-Time Scaling Framework for Robotic Manipulation](./01-e-tts-embodied-test-time-scaling.md)
   - arXiv ID：2606.27268v1
   - 主題：Embodied test-time scaling、Vision-Language-Action、robot manipulation、verifier、history-aware refinement
   - 發表 / 更新：2026-06-25 / 2026-06-25
   - 選文理由：這篇把 LLM/VLM 世界的 test-time scaling 轉到實體機器人操作，並強調 reasoning 與 action 要一起 scaling；對 VLA 部署、低資料成本改進與 closed-loop inference architecture 有參考價值。
   - 閱讀範圍：Summary/Abstract + Introduction

2. [In-Context World Modeling for Robotic Control](./02-in-context-world-modeling-robotic-control.md)
   - arXiv ID：2606.26025v2
   - 主題：VLA、in-context adaptation、system identification、world modeling、robot control
   - 發表 / 更新：2026-06-24 / 2026-06-25
   - 選文理由：這篇把 VLA 在新相機視角 / 新機器人配置下的泛化失敗重寫成 test-time system identification 問題；對「機器人如何先校準世界再執行任務」這條 Physical AI 主線很有啟發。
   - 閱讀範圍：Summary/Abstract + Introduction

## Commit 狀態

- 建立日期：2026-06-29
- 狀態：已納入本次 cron 提交
