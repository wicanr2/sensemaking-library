# 2026-07-04 論文拆解

本日依「最多 2 篇、品質優先」規則新增 2 篇自動選文。兩篇都屬 LLM / VLM / VLA 與 Robotics / Embodied AI 的交會主題：一篇聚焦 action-chunked VLA 的 adaptive action horizon 與 inference-time correction，另一篇聚焦由自然語言意圖驅動的 active camera motion。本次閱讀範圍皆為 Summary/Abstract + Introduction，未讀 methods / experiments / results 章節。

## 今日選文

1. [VLA-Corrector: Lightweight Detect-and-Correct Inference for Adaptive Action Horizon](./01-vla-corrector-adaptive-action-horizon.md)
   - arXiv：2607.01804v1
   - 選擇理由：處理 VLA action chunk 在實體控制中的 open-loop blind spot，把固定 action horizon 改成可監控、可中止、可修正的 adaptive horizon；對 Physical AI 部署層的 latency / reactivity trade-off 很有參考價值。
   - 閱讀範圍：Summary/Abstract + Introduction。

2. [LIME: Learning Intent-aware Camera Motion from Egocentric Video](./02-lime-intent-aware-camera-motion.md)
   - arXiv：2607.02417v1
   - 選擇理由：把 camera motion 視為 language-conditioned embodied action，補上「為了取得更有用觀測而主動移動感測器」的介面；和 VLA / active perception / robotics 任務前端高度相關。
   - 閱讀範圍：Summary/Abstract + Introduction。

## Commit 狀態

- 本次筆記已完成寫入；commit / push 狀態以本次執行結束後的 git 記錄為準。
