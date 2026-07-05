# Learning to Move Before Learning to Do: Task-Agnostic pretraining for VLAs

## 原文資訊
- 論文：Learning to Move Before Learning to Do: Task-Agnostic pretraining for VLAs
- 作者：Junhao Shi, Siyin Wang, Xiaopeng Yu, Li Ji, Jingjing Gong, Xipeng Qiu
- arXiv ID：2607.02466v1
- 分類：cs.RO, cs.AI
- 發表 / 更新：2026-07-02 / 2026-07-02
- 連結：[abs](https://arxiv.org/abs/2607.02466v1) / [pdf](https://arxiv.org/pdf/2607.02466v1)
- 本次閱讀範圍：Summary/Abstract + Introduction
- 擷取日期：2026-07-05

## 為什麼選這篇

這篇直接切中 VLA scaling 的資料問題：如果 VLA 的訓練資料必須是 observation、language instruction、action 三者對齊的專家示範，那資料收集成本會很高，而且機器人本身幾乎只是人類 teleoperation 的記錄器。作者提出的角度是，把「怎麼動」與「要做什麼」拆開，先用便宜、無語言標註、甚至任務無關的互動資料學 motor priors，再用少量 expert data 做語言 grounding。

它值得放入資料庫，是因為這個問題不只是一個模型技巧，而是 Physical AI 的資料經濟學：哪些資料真的需要人類語言與專家示範？哪些資料可以由機器人自我探索取得？如果 task-agnostic robot play 能提供可轉移的物理表徵，那 VLA 的 scaling path 會和目前以昂貴 teleoperation 為主的想像很不同。

在「LLM + Robotics / Embodied AI」交會上，這篇偏 robotics/VLA，但它處理的是語言條件行動模型的前訓練分解問題：語言不是完全不重要，而是被放到第二階段；第一階段先學 sensorimotor dynamics。這對理解 VLA 是否一定要用大規模 language-action paired data 很有參考價值。

## 一句話理解

這篇研究主張，VLA 不必一開始就用昂貴的語言標註專家示範學所有東西，可以先從任務無關互動中學會「怎麼動」，再用少量資料學會「聽懂要做什麼」。

## Summary / Abstract 說了什麼

摘要把 VLA 的主要瓶頸定義為 expert demonstrations 的稀缺：高品質資料通常是 observation、instruction、action 三元組，收集成本高。作者認為瓶頸來自把兩個目標混在一起：physical competence（how to move）與 semantic alignment（what to do）。其中只有後者真正需要 language supervision。

基於這個 Decomposition Hypothesis，作者提出 Task-Agnostic Pretraining（TAP）：第一階段用便宜、未標註的 interaction data 學可轉移 motor priors，資料來源包含 discarded off-task trajectories 與 autonomous robot play；訓練目標是 self-supervised inverse dynamics。第二階段再用少量 expert data 把這些 motor priors 對齊到語言任務。

摘要宣稱，在 SIMPLER benchmark 上，TAP 能用少很多 labeled data 接近使用大量 expert trajectories 的模型，並比 standard behavior cloning 有提升；在 WidowX real-world platform 上，面對 camera perturbations 時也保留部分成功率。不過本次只讀 abstract 與 introduction，這些數字仍屬作者宣稱，尚未檢查實驗細節。

## Introduction 的問題設定

Introduction 先指出，VLA 為 general-purpose robots 帶來希望，但訓練需要大量高品質 robot data。主流資料收集方式仰賴 human teleoperation：人類操作者控制機器人並提供任務語言標註。作者批評這個模式昂貴、勞力密集，而且從機器人角度看很被動；機器人只是接收人類示範，沒有主動探索自己的 embodiment。

作者接著用嬰兒學習作對比：人類嬰兒不是靠專家示範每一個抓取或操作，而是透過自發、任務無關的探索——伸手、觸碰、掉落、觀察結果——建立對物理、affordance、sensorimotor contingency 的理解。這個段落是 Introduction 的核心 framing：先學「how to move」，再學「what to do」。

在這個 framing 下，作者主張 robots 也應該從 active、task-agnostic data collection 受益。這類資料可以透過 random play 自動生成，不需要人類監督或任務標註；但當前 VLA pipeline 常把沒有明確 task instruction 的 trajectory 丟掉。作者認為，language instruction 對學「what to do」必要，對學「how actions change the world」不一定必要。

作者提出 TAP 來利用這些資料：任務無關 trajectory 與 autonomous random play 先透過 inverse dynamics objective 學習。形式上，模型要從觀察狀態 `o_t` 與未來狀態 `o_{t+1}` 推回造成轉移的 action `a_t`；直觀地說，這迫使模型注意 end-effector motion、object displacement 等動態因素，而不是背景雜訊。之後再用少量 expert demonstrations 做語言任務對齊。

## 研究的第一性問題

- **基本問題**：VLA 要規模化，不能所有能力都依賴昂貴的語言標註專家示範；必須分清哪些能力需要語言，哪些能力只是 embodied interaction 的物理基礎。
- **約束**：robot data 收集慢、專家 teleoperation 昂貴、真實世界干擾多、語言-action 對齊資料稀缺。
- **既有方法卡點**：把 observation-instruction-action 三元組視為主要資料單位，會讓沒有任務標註但含有物理互動資訊的資料被低估或丟棄。
- **作者試圖移動的邊界**：把 VLA training 分成 task-agnostic physical grounding 與 task-specific semantic alignment，讓 robot play / off-task trajectories 也能變成可用的前訓練資源。

## 可能的貢獻與限制（只基於 summary + introduction）

### 論文自稱

- 提出 Decomposition Hypothesis：physical competence 與 semantic alignment 應該拆開學。
- 提出 TAP，先用 task-agnostic data 做 inverse dynamics pretraining，再用少量 expert data 做語言對齊。
- 主張 discarded off-task trajectories 與 autonomous random play 可用來學 transferable motor priors。
- 摘要宣稱在 SIMPLER 與 real-world WidowX experiments 中提升 sample efficiency 與 generalization。

### 我的保守判讀

- 這篇的核心貢獻可能是資料觀念：不是所有 VLA 資料都必須是 language-action paired expert demos。這對降低資料成本有潛在重要性。
- Introduction 的嬰兒類比有啟發性，但類比不能直接證明 robot random play 的分布足以支撐各種 downstream tasks；這要看實驗與資料覆蓋。
- Inverse dynamics objective 很合理，因為它迫使模型關注狀態轉移與 action 的關係；但它學到的 motor priors 是否能跨 embodiment、跨相機、跨物體穩定轉移，仍需要讀後文。
- 摘要中的 benchmark 數字不能在本筆記中當成已驗證結論，因為尚未閱讀 methods/experiments/results。

## 可放進資料庫的筆記

1. VLA scaling 可以拆成兩種資料需求：語言任務對齊資料，與不含語言但含有物理互動的 sensorimotor data。
2. 「how to move」與「what to do」分離，是降低 expert demonstration 依賴的一種重要設計原則。
3. Task-agnostic robot play 的價值在於提供 affordance、動態、接觸、物體位移等底層物理訊號。
4. Inverse dynamics pretraining：從 `o_t` 與 `o_{t+1}` 預測 `a_t`，可被視為迫使模型理解「什麼行動造成什麼變化」的自監督任務。
5. 沒有語言標註的 trajectory 不等於沒價值；它可能只是不能直接教「任務語義」，但仍能教「行動物理」。
6. VLA 資料經濟學的關鍵問題：昂貴的人類示範應該用在語義對齊與任務定義，而不是所有低階運動能力。
7. 評估這類方法時要特別看資料分布：random play 是否真的覆蓋 downstream manipulation 所需的狀態轉移。

## 後續想追的問題

1. TAP 的 inverse dynamics pretraining 用的是哪種模型架構？與 downstream VLA backbone 如何銜接？
2. Task-agnostic data 的品質與多樣性如何衡量？random play 是否容易產生大量無效或偏態資料？
3. 它對跨 embodiment 的轉移效果如何？還是主要限於同一 robot / similar setup？
4. 摘要中的 SIMPLER 與 WidowX 結果，baseline 是否公平控制了資料量與模型容量？
5. 如果加入語言之前已學 motor priors，第二階段少量 expert data 的最小需求量大約是多少？
