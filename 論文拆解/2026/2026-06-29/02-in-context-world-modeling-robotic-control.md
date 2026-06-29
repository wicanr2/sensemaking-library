# In-Context World Modeling for Robotic Control

## 原文資訊
- 論文：In-Context World Modeling for Robotic Control
- 作者：Siyin Wang, Junhao Shi, Senyu Fei, Zhaoyang Fu, Li Ji, Jingjing Gong, Xipeng Qiu
- arXiv ID：2606.26025v2
- 分類：Robotics (cs.RO); Computer Vision and Pattern Recognition (cs.CV)
- 發表 / 更新：2026-06-24 / 2026-06-25
- 連結：[abs](https://arxiv.org/abs/2606.26025) / [pdf](https://arxiv.org/pdf/2606.26025)
- 本次閱讀範圍：Summary/Abstract + Introduction
- 擷取日期：2026-06-29

## 為什麼選這篇

這篇處理的是 VLA / robot control 裡一個很根本、但常被模型規模敘事蓋過的問題：部署環境變了，模型怎麼知道「現在這個系統怎麼運作」？例如相機視角、機器人 morphology、控制映射變了，單靠當下 observation + language instruction 的 policy 可能仍把訓練時的配置當成預設常數。

作者把這個 failure mode 重新框成 system identification 問題，而不是單純的 domain generalization 或 fine-tuning 問題。這個 framing 對 Physical AI 很有價值：真實世界的機器人不可能每次換相機、換場地、換硬體就重新標資料與重訓；它需要一種短時間自我校準的能力。

我選它作為第二篇，是因為它跟昨天筆記裡的 VLA 部署、policy routing、language-action pretraining 形成互補：昨天比較像「資料與模型如何讓 VLA 學會行動」，這篇則問「同一個 VLA 到新系統時，能不能先用少量 self-probing 了解 dynamics，再開始執行任務」。

## 一句話理解

ICWM 想把 VLA 的 context window 從「告訴模型要做什麼」改用成「讓模型先理解目前系統如何運作」，用短暫自我探索來做 test-time system identification。

## Summary / Abstract 說了什麼

摘要指出，現代 VLA models 在新 camera viewpoint 或新 robot morphology 下常常泛化失敗，原因是它們通常只根據當前 observation 與 language instruction 決策，沒有把底層 system configuration 當作顯式變數。換言之，模型默默假設部署時的環境跟訓練時一樣；一旦配置改變，就需要資料量大的 fine-tuning。

作者提出 In-Context World Modeling（ICWM），把 system identification 視為 in-context adaptation。它不是用 context window 放任務示範來說明「要做什麼」，而是在任務開始前讓機器人做一小段 task-agnostic self-generated interactions，從這些 observation-action history 中推斷「這個系統怎麼動」。

摘要宣稱，ICWM 在 simulation 與真實機器人平台上都能改善新 camera viewpoints 下的表現，且不需要 parameter update。這次我沒有讀實驗章節，因此只把這些視為論文自稱，尚未判斷其泛化範圍與失敗模式。

## Introduction 的問題設定

Introduction 用一個人類拿到陌生搖桿的比喻開場：人不會一開始就執行任務，而會先隨機推一下、觀察機器人的反應，快速建立 input-output relationship。作者認為，這種自我校準是目前 VLA 缺的能力。

標準 VLA policy 可粗略理解為 \(\pi_\theta(a_t \mid o_t, l)\)：在時間 \(t\) 根據當前 observation \(o_t\) 與 language instruction \(l\) 輸出 action \(a_t\)。作者指出，這個形式把 system configuration \(\psi\)（例如相機視角、機器人形態）當作訓練中吸收進參數的固定常數。當部署時 \(\psi\) 改變，policy 沒有機制恢復正確的 action-observation correspondence。

作者接著把問題定義為 test-time system identification：政策不是不知道任務，而是不知道目前系統配置。既有 in-context learning 常把 context 當作 behavior specification，也就是用示範告訴模型要做什麼；ICWM 則把 context 改成 system identification 的載體，也就是用自我產生、與任務無關的探索片段，讓模型推斷系統如何反應。

## 研究的第一性問題

- 基本問題：機器人 policy 在部署配置改變時，如何先理解目前系統，再執行語言指定的任務？
- 約束：不能每換一個相機視角或 robot morphology 就要求人工示範、標註或重訓。
- 既有方法卡點：標準 VLA 把系統配置隱含在訓練分布中；ICL 多半用 context 表示任務，而不是表示系統動力學。
- 作者試圖移動的邊界：把 context window 變成 test-time calibration / system identification 的空間，而不只是 prompt 或 demonstration buffer。

## 可能的貢獻與限制（只基於 summary + introduction）

### 論文自稱

- 將 VLA 在新配置下的泛化失敗重新定義為 system identification failure。
- 提出 ICWM：用任務開始前的 random exploratory movements 與 resulting visual transitions 作為 context。
- 不需要 task-specific demonstrations、parameter updates 或額外人工介入，就能讓 policy 隱式恢復當前 system configuration。
- 在 simulation 與 real-robot platforms 上改善 novel camera viewpoints 的表現，並宣稱可延伸到 semantic scene variations 與 robot morphological changes。

### 我的保守判讀

- 這篇最有啟發的地方是問題重寫：很多 VLA failure 不一定是語言理解或視覺語意問題，而是 action-observation 對應錯了。
- Random self-probing 是否足夠，可能高度依賴任務與系統差異。如果變化是接觸力學、摩擦、工具 compliance，而不只是視角，短探索片段可能不夠。
- ICWM 把 context 用來表示 dynamics，這會和長任務中的歷史記憶、指令理解、demonstration conditioning 競爭同一個 context budget。
- 我尚未讀 experiments，因此不能判斷它對 morphology changes 的證據強度；Introduction 中最清楚的主軸仍是 camera viewpoint adaptation。

## 可放進資料庫的筆記

- VLA policy 的隱含假設：\(\pi_\theta(a_t \mid o_t, l)\) 常把 system configuration \(\psi\) 藏進參數，部署時 \(\psi\) 變動就會出事。
- In-context learning 不必只回答「任務是什麼」，也可以回答「系統如何運作」。
- Self-probing 是一種低成本校準：在正式任務前用少量自我產生的 action-observation pairs 取得 system identity。
- Physical AI 的泛化不只需要語意泛化，也需要 action-observation correspondence 的泛化。
- 重新 framing 常比模型技巧更重要：把 OOD failure 看成 system identification failure，會導向不同解法。
- Context window 在 embodied AI 中可能同時扮演 instruction memory、trajectory memory、world model memory 與 calibration memory。
- 「無 parameter update 的 test-time adaptation」對真實部署有吸引力，但要小心探索動作本身的安全性。

## 後續想追的問題

- Self-probing 的探索動作如何設計才安全？是否可能撞到物體或破壞任務初始狀態？
- ICWM 對哪些 \(\psi\) 變化最有效：相機視角、控制延遲、morphology、摩擦、payload，還是只對部分變化有效？
- Context 長度增加時，校準片段與任務指令 / 歷史記憶如何取捨？
- 若使用更強的 world model 或 simulator，是否能把 random probing 換成 informative probing？
- ICWM 和 E-TTS 這類 inference-time verifier / feedback framework 能否結合成「先校準，再多候選修正」的部署 pipeline？
