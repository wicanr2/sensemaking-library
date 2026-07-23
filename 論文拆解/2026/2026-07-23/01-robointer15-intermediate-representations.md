# RoboInter1.5: A Holistic Intermediate Representation Suite for Embodied World Modeling and Robotic Manipulation

## 原文資訊
- 論文：RoboInter1.5: A Holistic Intermediate Representation Suite for Embodied World Modeling and Robotic Manipulation
- 作者：Ziqin Wang, Hao Li, Weijun Wang, Junhao Cai, Jia Zeng, Yilun Chen, Jiangmiao Pang, Si Liu
- arXiv ID：2607.18709v2
- 分類：Robotics (cs.RO)
- 發表 / 更新：Submitted on 2026-07-21 (v1) / last revised 2026-07-22 (v2)
- 連結：[abs](https://arxiv.org/abs/2607.18709) / [pdf](https://arxiv.org/pdf/2607.18709)
- 本次閱讀範圍：Summary/Abstract + Introduction（Introduction 由 PDF 文字抽取；未讀 methods / experiments / results）
- 擷取日期：2026-07-23

## 為什麼選這篇

這篇很貼近「LLM / VLM 如何進入 robotics」的中間層問題。它不是只把語言模型直接接到 action head，而是把 embodied task 裡的 subtask、primitive skill、object / gripper grounding、segmentation、affordance、grasp pose、contact point、motion trace 等表示，整理成一套可訓練、可評測、也可作為 world model 條件的 intermediate representation suite。

我覺得它值得放進資料庫，是因為它把 Physical AI 的瓶頸從「模型夠不夠大」移到「機器人資料能不能被結構化成可泛化的介面」。這和近期 VLA、robot foundation model、embodied world model 的路線都相連：如果 web-scale VLM 有語意與視覺常識，但 robot data 稀缺且 embodiment-specific，中間表示可能是把高層 reasoning 與低層 control 接起來的橋。

它也有一個值得追的主張：intermediate representations 不只是可解釋的輔助標籤，而是雙向介面，一邊約束低階 action space，一邊約束 open-world physical simulator 的 latent rollout。這個說法如果成立，代表資料標註格式本身會變成 Physical AI 系統架構的一部分。

## 一句話理解

RoboInter1.5 想把機器人操作中的中間結構標註，從「輔助 annotation」提升為連接 VLM reasoning、VLA execution 與 embodied world modeling 的共同介面。

## Summary / Abstract 說了什麼

摘要指出，現有 robot datasets 昂貴、常綁定特定 embodiment，而且缺少足夠細緻的結構標註，難以支撐 generalizable reasoning、execution 或 long-horizon environment dynamics simulation。作者延伸先前 RoboInter1.0，提出 RoboInter1.5：一套面向 robotic manipulation 與 embodied world modeling 的 intermediate representation 資源。

RoboInter-Data 包含超過 230k manipulation episodes、571 個 scenes，並提供 per-frame dense annotations，涵蓋十多種 intermediate representations，例如 subtasks、primitive skills、object / gripper grounding、segmentation、affordance、grasp poses、contact points、motion traces 等。基於這些標註，RoboInter-VQA 設計 spatial / temporal embodied VQA tasks，用來評測與提升 RoboInter-VLM 的中間表示推理能力。

摘要也說，RoboInter-VLA 進一步研究這些表示如何透過 implicit、explicit、modular plan-then-execute 等範式影響 action execution；RoboInter-World 則把 intermediate representations 當成 structured conditioning signals，用於 controllable prediction of future world states。作者宣稱這套系統提供 unified spatiotemporal scaffolding，並將 intermediate representations 視為一種雙向介面。

## Introduction 的問題設定

Introduction 先從大模型進展談起：large VLMs 推動 embodied question answering、VLA robotic control、embodied world models 等方向。但作者指出，web-scale multimodal data 雖然給 VLM 廣泛語意推理能力，VLA policies 與 generative world models 卻很難直接繼承這些能力。

接著，作者把 bottleneck 放在 robot data 的成本與耦合性上。大規模 robot dataset 的蒐集與標註很昂貴，而且常與特定 robot embodiment、scene、task 設定綁在一起；如果只靠更多 raw trajectories，不一定能得到可泛化的 embodied reasoning。

Introduction 進一步整理近年路線：control 端已有 modular VLA 與 end-to-end VLA 使用 subtasks、grounding、motion traces 等訊號，試圖把 high-level reasoning 與 low-level control 拆開；world modeling 端也開始需要更可控的條件，以避免生成的未來狀態只是視覺上合理、但不一定對 robot interaction 有用。作者的核心問題是：能不能把這些分散的中間訊號變成一套統一的資料、benchmark 與模型接口？

## 研究的第一性問題

- **基本問題**：如果 robot learning 要跨 embodiment、跨場景、跨任務泛化，raw video / action trajectory 是否足夠？還是需要可重用的中間結構？
- **約束**：robot data 昂貴、標註細、場景與硬體耦合；world model 要同時保留視覺合理性與物理互動可控性。
- **既有方法卡點**：端到端 VLA 可能把 reasoning、grounding、control 壓進同一個 latent space，難以診斷；純視覺 world model 可能缺少 task-relevant constraints。
- **作者試圖移動的邊界**：把 intermediate representations 從「可解釋輔助」變成「VQA、VLA、world model 都共享的 spatiotemporal scaffold」。

## 可能的貢獻與限制（只基於 summary + introduction）

### 論文自稱

- 提供 RoboInter-Data：230k+ manipulation episodes、571 scenes、per-frame dense annotations。
- 建立 RoboInter-VQA，用 spatial / temporal embodied VQA 測量中間表示推理能力。
- 建立 RoboInter-VLA，研究 intermediate representations 對 action execution 的影響。
- 建立 RoboInter-World，把中間表示當成 future world state prediction 的 structured conditioning signals。
- 將 intermediate representations 概念化為同時約束低階 action 與 world model rollout 的雙向介面。

### 我的保守判讀

這篇的價值很可能在「資料與介面設計」，不只是單一模型表現。若後續實驗支持它，RoboInter1.5 可以成為 VLA / embodied world model 的一種共同資料語言。不過我目前只讀到摘要與 Introduction，還不能判斷標註品質、benchmark 是否避免 leakage、VLA / world model 的實際提升是否穩健，也不能判斷這些 intermediate representations 在不同 robot embodiment 間是否真的降低遷移成本。

## 可放進資料庫的筆記

- Intermediate representation 可以被視為 Physical AI 的「介面層」，不是單純的可解釋標籤。
- VLM 到 VLA 的落差，常不是語意理解不足，而是缺少把語意落地到 action-relevant state 的結構。
- Embodied world model 若只學 video dynamics，可能需要 task / contact / affordance 等條件來避免無用的視覺想像。
- Robot data 的昂貴不只在收集量，也在 annotation schema 是否可跨任務複用。
- Modular plan-then-execute 與 end-to-end VLA 的差異，可以透過中間表示來重新比較，而不只是比較 architecture。
- 「雙向介面」是重要概念：同一套 representation 可能同時服務 reasoning → action，也服務 action / object motion → future state prediction。
- 如果未來 robot foundation model 需要資料標準，這類 dense per-frame embodied annotations 可能比單純增加 episodes 更關鍵。

## 後續想追的問題

- RoboInter1.5 的 annotation 是人工、半自動還是模型生成？標註錯誤如何影響下游？
- RoboInter-VQA 是否真的測到 embodied reasoning，還是測到資料集中可被捷徑解出的模式？
- RoboInter-VLA 的 implicit、explicit、modular plan-then-execute 三種用法，哪一種對真實 robot transfer 最有效？
- RoboInter-World 的 future state prediction 是否與實際 manipulation success 有穩定相關？
- 這套 intermediate representations 能否跨 humanoid、mobile manipulator、single-arm setup 泛化？
