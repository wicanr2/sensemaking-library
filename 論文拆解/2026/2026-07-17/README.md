# 2026-07-17 論文拆解

今日新增 2 篇。兩篇都屬於 LLM / VLM 與 Robotics / Embodied AI 的交會主題；閱讀邊界均為 arXiv Summary/Abstract + Introduction，未讀全文其他章節。

## 今日選文

1. [Exploratory, Communicative, and Deployable: Vision-Driven Embodied Agents for Open-World Mobile Manipulation](./01-real-open-world-mobile-manipulation.md)
   - arXiv ID：2607.13653v1
   - 選擇理由：這篇把 embodied agent 的部署問題放回真實可取得的感知與互動介面，特別是移除 simulator oracle、要求主動探索與使用者澄清，適合作為 Physical AI 評測設計的參考。
   - 閱讀範圍：Summary/Abstract + Introduction（arXiv HTML 未提供，Introduction 由 PDF 文字抽取）

2. [Generalizable VLA Finetuning via Representation Anchoring and Language-Action Alignment](./02-anchor-align-vla-finetuning.md)
   - arXiv ID：2607.13429v1
   - 選擇理由：這篇直接處理 VLA fine-tuning 中 pretrained VLM 表示被覆寫、language head 與 action head 未同場對齊的問題，對理解 foundation model 如何轉成機器人控制很有價值。
   - 閱讀範圍：Summary/Abstract + Introduction

## 今日防重入狀態

- 開始前 `論文拆解/2026/2026-07-17/` 沒有非 README 的論文筆記。
- 本次新增 2 篇，已達每日上限；同日後續排程不應再新增第 3 篇。

## Commit 狀態

- 本次批次完成後以 git log / remote ref 驗證。
