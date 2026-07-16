# 2026-07-16 論文拆解

今日排程選文 2 篇；皆屬 LLM / VLM + Robotics / Embodied AI 交會主題。排程選文只讀 arXiv Summary/Abstract 與 Introduction，未讀全文其他章節。第一篇 PDF 於擷取時回傳 HTML 頁面，因此 Introduction 取自 arXiv HTML；第二篇 Introduction 由 PDF 文字擷取。兩篇結論皆依有限閱讀邊界保守處理。

## 今日選文

1. [PhysClaw-0: A Symbiotic Agentic System for Robot Autonomy via Language Corrections](./01-physclaw-0-language-corrections.md)
   - arXiv ID：2607.14047v1
   - 分類：Robotics (cs.RO)；Human-Computer Interaction (cs.HC)；Systems and Control (eess.SY)
   - 選擇理由：它把 LLM 放在機器人資料收集迴圈的控制層：自然語言修正會被解析成可重用的 Corrective Memory，補上 VLA / Physical AI 從 demonstration 到資料飛輪的實務瓶頸。
   - 閱讀範圍：Summary/Abstract + Introduction（Introduction 取自 arXiv HTML）

2. [Semantic Anchoring for Robotic Action Representations](./02-semantic-anchoring-action-representations.md)
   - arXiv ID：2607.13597v1
   - 分類：Robotics (cs.RO)；Artificial Intelligence (cs.AI)；Computer Vision (cs.CV)
   - 選擇理由：它把 VLA 的泛化問題轉成 action representation 是否保留 pretrained VLM semantic structure 的問題，對判讀 robot foundation model 的中間表示與 fine-tuning 退化很有參考價值。
   - 閱讀範圍：Summary/Abstract + Introduction

## commit 狀態

- 本日筆記建立於排程任務執行期間。
- 實際 commit SHA 以 git log 為準。
