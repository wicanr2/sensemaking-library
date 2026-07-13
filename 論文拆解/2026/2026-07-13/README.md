# 2026-07-13 論文拆解

本日新增 2 篇。兩篇都屬於 LLM + Robotics / Embodied AI 交會主題；一篇偏 VLA 的資料效率與 hindsight relabeling，另一篇偏多 LLM 具身代理的協調架構。閱讀邊界皆為 arXiv Summary/Abstract + Introduction，未讀全文其他章節。

## 今日選文

1. [Learning More from Less: Reinforcement Learning from Hindsight](./01-learning-more-from-less-rl-from-hindsight.md)
   - arXiv：2607.09042v1
   - 主題：VLA、robot manipulation、RL post-training、hindsight relabeling
   - 選擇理由：機器人 rollout 成本高且 sparse reward 會浪費大量失敗軌跡；這篇把 VLM 用作語意重標器，讓失敗 rollout 變成另一個任務的成功資料，對 VLA 資料效率問題有直接參考價值。
   - 閱讀範圍：Summary/Abstract + Introduction。

2. [Communication-Efficient Digital-Twin Coordination for Heterogeneous LLM Embodied Agents over Computing Power Networks](./02-ldt-coord-heterogeneous-llm-embodied-agents.md)
   - arXiv：2607.09330v1
   - 主題：heterogeneous LLM embodied agents、digital twin、多代理協調、Physical AI control plane
   - 選擇理由：它把 LLM agent team 放進工廠、倉儲、服務機器人等 Physical AI 場景，討論多輪自然語言協商的通訊量、弱模型短板與延遲問題；適合作為多具身代理系統架構線索。
   - 閱讀範圍：Summary/Abstract + Introduction。

## 今日取捨

今日 arXiv 近期 robotics / embodied AI 結果不少，但多數偏單點控制、感測或傳統機器人方法。本日只選兩篇交會性較高的論文：一篇聚焦 VLA policy training，另一篇聚焦 LLM embodied agent coordination。未再加入其他純 robotics paper，以避免超過每日上限，也避免為了湊數加入關聯較弱的文章。

## Commit 狀態

- 本次任務完成後已 commit / push。
