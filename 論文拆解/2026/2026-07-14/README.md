# 2026-07-14 論文拆解

今日排程選文 2 篇；排程選文只讀 arXiv Summary/Abstract 與 Introduction，未讀全文其他章節。另依使用者指定，補讀 1 篇經典 prompt optimization 論文，該篇閱讀範圍為全文 PDF 與 arXiv source。使用者指定補充不計入每日排程選文上限。

## 今日選文

1. [CLAP: Direct VLM-to-VLA Adaptation via Language-Action Grounding](./01-clap-vlm-to-vla-language-action-grounding.md)
   - arXiv ID：2607.08974v1
   - 分類：Robotics (cs.RO)；Artificial Intelligence (cs.AI)
   - 選擇理由：它用最小架構變動討論 VLM-to-VLA adaptation，核心問題是 language distribution 與 numeric action token 的落差，對 compact VLA 與能力轉移分析有直接價值。
   - 閱讀範圍：Summary/Abstract + Introduction

2. [GenVid2Robot: From Video Generation to Robot Manipulation via Rigid-Geometric Consistency](./02-genvid2robot-rigid-geometric-consistency.md)
   - arXiv ID：2607.09191v1
   - 分類：Robotics (cs.RO)；Machine Learning (cs.LG)
   - 選擇理由：它把 generated video 視為不確定的 motion hypothesis，而不是 robot demonstration；用 sparse rigid `SE(3)` consistency 作為 Physical AI 進入實體執行前的幾何閘門。
   - 閱讀範圍：Summary/Abstract + Introduction

## 使用者指定補充閱讀

3. [RLPrompt: Optimizing Discrete Text Prompts with Reinforcement Learning](./03-rlprompt-discrete-prompt-optimization.md)
   - arXiv ID：2205.12548v3
   - 分類：Computation and Language (cs.CL)；Machine Learning (cs.LG)
   - 選擇理由：把 discrete prompt 搜尋改寫成 reinforcement learning，讓 prompt 從人工撰寫的自然語言指令，變成可用 reward 最佳化、可跨模型測試的黑箱控制介面。
   - 閱讀範圍：全文 PDF + arXiv source；包含方法、實驗、分析、限制與附錄重點。

## commit 狀態

- 本日筆記建立於排程任務執行期間。
- 實際 commit SHA 以 git log 為準。
