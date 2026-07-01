# 2026-07-01 論文拆解

本日依「最多 2 篇、品質優先」規則新增 2 篇。兩篇皆屬 LLM / VLM / VLA 與 Robotics / Embodied AI 交會主題；本次閱讀範圍皆限 arXiv Summary/Abstract + Introduction，未讀 methods / experiments / results 章節。

## 今日選文

1. [Training Vision-Language-Action Models with Dense Embodied Chain-of-Thought Supervision](./01-zr-0-dense-ecot-vla.md)
   - arXiv：2606.30552v1
   - 選擇理由：聚焦 VLA 的 cross-embodiment representation alignment，把 embodied chain-of-thought 作為訓練監督，而非推論時輸出；對 robot foundation model 的中間表示設計有參考價值。
   - 閱讀範圍：Summary/Abstract + Introduction。

2. [Sequential Planning via Anchored Robotic Keypoints](./02-spark-anchored-robotic-keypoints.md)
   - arXiv：2606.30613v1
   - 選擇理由：用 training-free neurosymbolic manipulation system 對照端到端 VLA 與 Code-as-Policy，提出把 test-time compute 優先花在 perception grounding 而非重寫 plan 的設計判斷。
   - 閱讀範圍：Summary/Abstract + Introduction。

## Commit 狀態

- 本次筆記已完成寫入；commit / push 狀態以本次執行結束後的 git 記錄為準。
