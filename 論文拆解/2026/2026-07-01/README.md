# 2026-07-01 論文拆解

本日依「最多 2 篇、品質優先」規則新增 2 篇自動選文。另依使用者指定，額外加入 1 篇 speculative decoding 專欄；此專欄不計入每日自動選文上限。兩篇自動選文屬 LLM / VLM / VLA 與 Robotics / Embodied AI 交會主題；專欄聚焦 LLM inference / speculative decoding 趨勢。本次閱讀範圍皆明確標示，未讀 methods / experiments / results 章節。

## 今日選文

1. [Training Vision-Language-Action Models with Dense Embodied Chain-of-Thought Supervision](./01-zr-0-dense-ecot-vla.md)
   - arXiv：2606.30552v1
   - 選擇理由：聚焦 VLA 的 cross-embodiment representation alignment，把 embodied chain-of-thought 作為訓練監督，而非推論時輸出；對 robot foundation model 的中間表示設計有參考價值。
   - 閱讀範圍：Summary/Abstract + Introduction。

2. [Sequential Planning via Anchored Robotic Keypoints](./02-spark-anchored-robotic-keypoints.md)
   - arXiv：2606.30613v1
   - 選擇理由：用 training-free neurosymbolic manipulation system 對照端到端 VLA 與 Code-as-Policy，提出把 test-time compute 優先花在 perception grounding 而非重寫 plan 的設計判斷。
   - 閱讀範圍：Summary/Abstract + Introduction。

## 使用者指定專欄

3. [專欄｜Speculative Speculative Decoding：LLM 推論加速正在從「模型更小」轉向「時間軸重排」](./03-speculative-speculative-decoding-column.md)
   - arXiv：2603.03251v3
   - 收錄理由：使用者指定整理 speculative decoding 趨勢；已擴充 SSD、EAGLE / MTP、PARD、異質 vocabulary、LayerSkip / early exiting 的技術地圖；本篇作為額外專欄，不計入每日自動選文上限。
   - 閱讀範圍：arXiv metadata、Abstract、Introduction，並額外快速掃描 Related Work 題名層級、arXiv metadata 趨勢與相關方向摘要。

## Commit 狀態

- 本次筆記已完成寫入；commit / push 狀態以本次執行結束後的 git 記錄為準。
