# 2026-07-22 論文拆解

今日新增 2 篇。兩篇都屬於 VLA / Physical AI 交會主題，且都把問題放在「模型如何保留可用的物理歷史狀態」：一篇從 force memory 切入接觸型操作，另一篇從 persistent 3D object tokens 切入 humanoid loco-manipulation 的閉環驗證。

## 今日選文

1. [FM-VLA: Force-based Memory for Vision-Language-Action Models in Contact-Rich Manipulation](./01-fm-vla-force-memory-contact-rich-manipulation.md)
   - arXiv：2607.18231v1
   - 選擇理由：這篇把 VLA 的記憶問題從影像／語言推到力覺歷史，特別適合用來思考 contact-rich manipulation 中的非 Markovian 任務進度。
   - 本次閱讀範圍：Summary/Abstract + Introduction。

2. [Closing the Loop in Humanoid VLA: Persistent 3D Object Tokens for Verifiable Loco-Manipulation](./02-pot-vla-persistent-3d-object-tokens.md)
   - arXiv：2607.18016v1
   - 選擇理由：這篇把 humanoid VLA 的核心瓶頸定義為 object-state divergence，並提出 action generation 與 predicate verification 共享的持久 3D 物件記憶。
   - 本次閱讀範圍：Summary/Abstract + Introduction。

## 今日筆記狀態

- 防重入檢查：開始前 `論文拆解/2026/2026-07-22/` 無既有非 README 論文筆記。
- 今日自動選文數量：2 / 2。
- 未讀全文其他章節；所有判讀只基於 arXiv summary/abstract 與 Introduction。
- Commit 狀態：已於本次工作流完成 commit / push。
