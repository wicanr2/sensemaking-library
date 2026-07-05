# 2026-07-05 論文拆解

本日依照「最多 2 篇、品質優先、不硬湊」規則，從近期 arXiv 論文中選 2 篇。兩篇都屬於 Physical AI / LLM + Robotics 交會附近：一篇處理 embodied model 的部署 runtime，一篇處理 VLA 的 task-agnostic pretraining 與資料成本問題。

本日所有單篇筆記都只根據 arXiv Summary/Abstract 與 Introduction 撰寫，未讀 methods / experiments / results 全文；文中已把「論文自稱」與「我的保守判讀」分開。

## 今日選文

1. [Embodied.cpp: A Portable Inference Runtime of Embodied AI Models on Heterogeneous Robots](./01-embodied-cpp-portable-inference-runtime.md)
   - arXiv ID：2607.02501v1
   - 選擇理由：把 VLA / WAM 的部署問題從一般 LLM/VLM serving 中切出來，明確討論 multi-rate、closed-loop latency、batch-1、embodied I/O 與 adapter/plugin 架構。適合補足 Physical AI stack 中「runtime / deployment」這一層。
   - 閱讀範圍：Summary/Abstract + Introduction。

2. [Learning to Move Before Learning to Do: Task-Agnostic pretraining for VLAs](./02-task-agnostic-pretraining-vlas.md)
   - arXiv ID：2607.02466v1
   - 選擇理由：針對 VLA scaling 的資料瓶頸，提出先用無語言標註、任務無關互動資料學 motor priors，再用少量 expert data 做語言 grounding。這對理解 VLA 的資料經濟學與 robot play 的價值很有參考性。
   - 閱讀範圍：Summary/Abstract + Introduction。

## 今日 commit 狀態

- 本次執行將新增上述 2 篇筆記並更新索引。
- 實際 commit SHA 以 git log 為準。
