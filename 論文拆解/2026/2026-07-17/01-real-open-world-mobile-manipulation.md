# Exploratory, Communicative, and Deployable: Vision-Driven Embodied Agents for Open-World Mobile Manipulation

## 原文資訊
- 論文：Exploratory, Communicative, and Deployable: Vision-Driven Embodied Agents for Open-World Mobile Manipulation
- 作者：Boyu Mi, Mengchen Ma, Yifei Yao, Xing Gao, Junting Chen, Yangzi Li, Zihou Zhu, Guohao Li, Zhenfei Yin, Tai Wang, Yao Mu, Jiangmiao Pang, Hanqing Wang
- arXiv ID：2607.13653v1
- 分類：Computer Vision and Pattern Recognition (cs.CV); Robotics (cs.RO)
- 發表 / 更新：Submitted on 15 Jul 2026
- 連結：[abs](https://arxiv.org/abs/2607.13653v1) / [pdf](https://arxiv.org/pdf/2607.13653v1)
- 本次閱讀範圍：Summary/Abstract + Introduction（arXiv HTML 未提供，Introduction 由 PDF 文字抽取；未讀 methods / experiments / results）
- 擷取日期：2026-07-17

## 為什麼選這篇

這篇很貼近「LLM + Robotics / Embodied AI」的核心問題：LLM / VLM 已經能做語言推理與視覺理解，但真實機器人部署時，任務不是只在文字環境裡調用工具，而是要在不完整感知、模糊指令、相似物體與可執行導航 / 抓取工具鏈之間閉環運作。

作者把問題放在 open-world mobile manipulation：機器人要自己探索環境、看見候選物、必要時向人確認意圖，最後執行操作。這比單純「語言指令轉動作」多了一層真實部署約束：使用者可能講得不完整，環境資訊也不能靠 simulator oracle 直接讀出。

我選它的原因不是因為摘要中的成功率數字，而是因為它把 embodied agent benchmark 常見的捷徑講得很清楚：若訓練 / 評測時使用全域物件清單、目標 3D pose、oracle category finder，模型看似有推理能力，實際上可能只是依賴現實世界拿不到的介面。

## 一句話理解

這篇想把 embodied agent 從「有 oracle 的模擬工具使用」推向「只能靠可部署視覺感知、主動探索與人機澄清來完成開放世界移動操作」。

## Summary / Abstract 說了什麼

摘要指出，真實部署的 embodied agents 需要三件事：主動探索、視覺 grounding、以及在指令不清楚時互動式釐清意圖。作者認為既有框架常依賴 simulator privileged states，或假設使用者指令完整，因而跳過了部署時最麻煩的部分。

論文提出 REAL，一個面向 open-world mobile manipulation 的 agentic framework。它建立與 sim-to-real 較一致的環境 API，不讓 agent 使用 oracle perception；同時加入 simulated user，讓 agent 可以在任務執行過程中透過語言互動消除歧義。摘要還說，作者用任務組合來收集資料、做 supervised fine-tuning 與 online reinforcement learning，並在實體雙臂移動機器人上測試。

這些主張目前只能先視為作者在摘要與 Introduction 中的宣稱；我沒有讀實驗章節，因此不把成功率或 benchmark 表現當成已驗證結論。

## Introduction 的問題設定

Introduction 先把 LLM 定位成 embodied agents 的重要驅動：早期系統多在文字型環境中，透過 textual perception 與 tool calling 完成 household tasks。但純文字互動缺少空間與物理 grounding，因此後續大量引入 VLM 與高擬真視覺環境。

作者接著指出兩個部署落差。第一，許多環境讓 agent 使用 simulator privileged perceptual information，例如全域物件清單、目標位置或 category-finding API。這些介面在真實世界不可直接取得，導致從模擬到實體部署時失效。第二，許多 benchmark 假設使用者給出明確完整指令，讓執行變成 open-loop；但現實中使用者常不知道環境細節，初始指令可能含糊，agent 必須在探索後回頭問人。

因此 REAL 的問題設定是：移除 oracle perceptual API，讓 agent 只能透過 RGB-based exploration、navigation、object detection、visual prompting for manipulation 這類比較可部署的工具鏈取得資訊；同時讓 simulated user 根據 agent 目前探索結果給出動態語言回饋。

## 研究的第一性問題

- **基本問題**：一個 embodied agent 若要在真實家居 / 開放場景中完成移動操作，哪些資訊必須由感知與互動取得，而不能在 benchmark 裡偷看？
- **約束**：機器人不能讀取全域物件清單、oracle 3D pose、目標位置；只能用可部署的感測與工具鏈逐步縮小不確定性。
- **既有方法卡點**：很多系統把探索與溝通簡化掉，導致模型能力被高估；文字層級 planning 也可能沒有緊密接上視覺觀察。
- **作者試圖移動的邊界**：把「agent 會不會推理」改成「agent 能不能在不完整、模糊、只能局部觀測的環境裡，透過探索與詢問完成任務」。

## 可能的貢獻與限制（只基於 summary + introduction）

### 論文自稱

- REAL 移除 oracle perceptual API，建立較接近 sim-to-real 的 embodied agent 環境。
- 它把探索、視覺 grounding、使用者互動、導航與操作整合到一個 open-world mobile manipulation framework。
- 作者宣稱透過任務組合、SFT 與 online RL 系統性優化 agent，並在實體雙臂移動機器人上展示零樣本轉移能力。

### 我的保守判讀

- 這篇的價值可能在 benchmark / environment design，而不只是某個單點模型技巧。它提醒我們：Physical AI 的評測若允許不可部署資訊，會把「世界模型」與「資料庫查詢」混在一起。
- simulated user 是合理的中介，但後續要看它是否過度規則化；如果使用者回覆太乾淨，仍可能低估真實人機溝通成本。
- 我還沒讀方法與實驗，所以不能判斷 REAL 的工具鏈是否真的穩定，也不能判斷 78.3% 實體成功率的任務難度與失敗分布。

## 可放進資料庫的筆記

- Embodied agent 的關鍵不是只把 LLM 接上工具，而是讓工具介面符合真實世界可取得的感知與控制通道。
- Benchmark 中的 oracle API 會形成「隱形資料洩漏」：模型看似能探索，其實只是讀取模擬器內部狀態。
- Open-world mobile manipulation 至少同時包含三種不確定性：環境未知、目標語意模糊、動作執行有物理誤差。
- 互動式澄清不是 UX 附加功能，而是 embodied control 的一部分；它可以把不可觀測的使用者偏好變成可執行約束。
- 若一個 robotics agent 的 planning 主要在文字層完成，仍要追問它如何被視覺觀察校正。
- Sim-to-real 的重點不只在影像 domain gap，也在「介面是否可部署」的 API gap。
- Physical AI 評測應區分：感知取得的資訊、使用者提供的資訊、模擬器偷看的資訊。

## 後續想追的問題

- REAL 的 simulated user 如何生成回覆？它是否能覆蓋真實人類模糊、反悔或資訊不一致的情況？
- online RL 的 reward 如何設計？是否會過度依賴環境中的可量測代理指標？
- 實體機器人失敗案例主要來自探索、辨識、語言澄清、導航還是操作？
- 這套 environment API 能否移植到不同平台，例如單臂 mobile manipulator 或人形機器人？
- 若沒有大型資料收集能力，小型 lab 能否重現 REAL 的核心設計？
