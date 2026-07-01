# Sequential Planning via Anchored Robotic Keypoints

## 原文資訊
- 論文：Sequential Planning via Anchored Robotic Keypoints
- 作者：Bryce Grant, Aryeh Rothenberg, Logan Senning, Zonghe Chua, Zach Patterson, Peng Wang
- arXiv ID：2606.30613v1
- 分類：Robotics (cs.RO)
- 發表 / 更新：Submitted on 29 Jun 2026 / v1
- 連結：[abs](https://arxiv.org/abs/2606.30613v1) / [pdf](https://arxiv.org/pdf/2606.30613v1)
- 本次閱讀範圍：Summary/Abstract + Introduction
- 擷取日期：2026-07-01

## 為什麼選這篇

這篇不是典型的「再訓練一個更大的 VLA」論文，而是從另一個角度回應 VLA 在 embodied manipulation 上的弱點：當測試場景位置或指令改變時，端到端模型容易把學到的軌跡綁在原本 layout 上。它把 LLM / VLM / symbolic planning / perception grounding 放在同一個系統裡，對 LLM + Robotics 的交會很有參考價值。

我選它的主要原因，是它提出一個清楚的 first-principles 問題：面對位置與語言擾動時，機器人系統應該把 test-time compute 花在重新規劃，還是重新感知？作者的答案是，plan structure 往往沒有變，真正需要更新的是物體位置與 grounded keypoints。因此 SPARK 把 LLM planning call 限制為一次，把後續計算重心放到 perception prompts、SAM3 grounding 與 primitive retry。

這篇也提供一個和 VLA 熱潮不同的對照：在某些 manipulation 任務中，可檢查的 typed behavior tree 與 modular primitives 也許比黑箱 policy 更容易定位失敗、生成訓練資料、跨機器人重用。不過本次只讀摘要與 Introduction，尚未檢查實驗設定是否公平或 benchmark 是否完整。

## 一句話理解

SPARK 主張：機器人長序列操作的 plan 可以保持符號化與可檢查，把 test-time compute 優先用在重新 grounding 物體 keypoints，而不是每次失敗都重新叫 LLM 生成程式。

## Summary / Abstract 說了什麼

摘要介紹 SPARK 是 training-free 的 neurosymbolic manipulation system。作者宣稱它在六個 LIBERO-PRO position 與 task cells 上達到 43.7%，超過 CaP-Agent0 與 VLA baselines；CaP-Agent0 透過每回合重新 query LLM 做 multi-turn code generation，平均為 18.2%。

SPARK 的核心設計是：先用單次 Gemini call，把任務寫成 typed behavior tree，這個 behavior tree 由可組合 primitives 構成。這些 primitives 已經包含 low-level control，例如 motion、grasping、depth geometry，因此不用像 code-generation agent 一樣每次都重新生成底層控制程式。

剩下的 compute budget 用在 perception。摘要說，系統會用第二次 Gemini call 為每個物體提出三個替代文字 prompts，讓 SAM3 評估並選擇信心最高的 prompt-to-label pair；若 primitive 失敗，recovery loop 會在新偵測到的物體上重試，不再呼叫 LLM。

作者宣稱 alternative prompts 對 spatial suite 增加 +27.7 points、object suite 增加 +10.0，recovery loop overall 再加 +5.0。也宣稱同一組 primitives 可在 UR10e、Franka FR3、bimanual Franka 三種 robot families 上執行，九個任務、每個二十次 trial，平均 68%。這些數字目前只記為摘要與 Introduction 的 claim。

## Introduction 的問題設定

Introduction 先指出，端到端 VLA 在標準 simulated manipulation benchmark 上可以有很高分，但當測試場景偏離訓練 layout 時，分數可能大幅下降。作者引用 LIBERO-PRO 作為測試這類 overfitting 的 benchmark，特別關注 object position 與 task instruction perturbations。

作者對 VLA 失敗的解釋是：一些機制研究顯示，VLA latent features 可能把 end-effector position 或軌跡與固定 layout 綁得太緊；物體一移位，原本 memorized trajectory 就容易失效。這裡的問題不是「高層任務變了」，而是「目標物 pixels 的位置變了」。

接著，Introduction 討論 Code-as-Policy / CaP-Agent0 類方法。這些方法用 code-generation agent 在 test time 寫控制程式，可部分恢復 robustness，但代價是多次 frontier model calls；而且每次 script 失敗，常常只能重新 query model。

SPARK 的觀察是：位置或語言擾動通常不改變 plan structure。把碗放到盤子上，不論碗在左邊或右邊，高層 plan 仍類似；真正需要重做的是 object grounding。因此系統先用 typed grammar 產生符號 plan，再把主要 test-time compute 放到 live perception、prompt alternatives、keypoint anchoring 與 primitive retry。

## 研究的第一性問題

- 基本問題：長序列 manipulation 的泛化瓶頸，是 plan 需要重寫，還是 perception grounding 需要更新？
- 約束：真實機器人部署不能每個小失敗都重新呼叫昂貴 LLM；也需要可定位失敗來源。
- 既有方法卡點：端到端 VLA 可能 memorized layout；Code-as-Policy 則把大量 compute 花在反覆重新生成 plan/code。
- 作者試圖移動的邊界：把 plan 固定為 typed behavior tree，讓 perception 與 primitive retry 成為主要調適層；同時保留可檢查 post-condition 與可累積的 labeled trajectory。

## 可能的貢獻與限制（只基於 summary + introduction）

### 論文自稱

- 提出 SPARK：training-free neurosymbolic manipulation system。
- 用一次 Gemini planning call 產生 typed behavior tree，避免每回合重新生成程式。
- 將額外 compute 用在 perception prompts、SAM3 grounding 與 recovery loop。
- 在 LIBERO-PRO position/task 擾動上超過 CaP-Agent0 與 VLA baselines。
- 同一 primitive grammar 可跨 UR10e、Franka FR3、bimanual Franka 執行，並產生可標註的 trajectory logs。

### 我的保守判讀

- 這篇的概念強點，是把「robustness」拆成 plan robustness 與 grounding robustness，而不是籠統地說 VLA 不夠泛化。
- 但 Introduction 的敘事對 VLA baseline 可能較嚴格，對 modular system 的工程先驗較友善；需要讀實驗章節確認任務是否真的適合 typed primitives。
- SPARK 的 training-free 優勢可能依賴預先設計的 primitive grammar。若任務超出 primitive set，系統可能不像摘要看起來那麼 general。
- 使用 Gemini 與 SAM3 的 perception loop 仍有外部模型依賴；若換模型、換場景、換物體類型，prompt selection 的穩定性需要再確認。

## 可放進資料庫的筆記

- Test-time compute allocation 是 embodied agent 設計的核心問題：不是所有失敗都該交給 LLM 重新規劃。
- 若 task plan 在擾動下不變，compute 應優先花在 grounding，而不是重新生成 plan。
- Typed behavior tree 可以把 LLM 的輸出限制在可檢查、可組合、可重試的結構裡。
- Modular primitives 的價值不只在執行，也在失敗定位：post-condition 可以指向 perception、controller 或 kinematic limit。
- Training-free system 若能產生 labeled trajectories，可能反過來成為訓練 VLA / policy 的資料來源。
- VLA 與 neurosymbolic robotics 的競爭，不一定是二選一；可檢查 planner 可能負責資料生成與 safety envelope，VLA 負責更廣泛的 pattern learning。
- 「物體位置改變」與「任務結構改變」應分開看，否則容易把 perception failure 誤判成 planning failure。

## 後續想追的問題

- LIBERO-PRO 的比較是否控制了所有模型的 perception input、prompt budget 與 retry budget？
- Typed primitive grammar 覆蓋哪些任務？哪些任務需要新增 primitive 才能完成？
- SPARK 在 real robot 上的 68% 平均成功率，失敗主要來自 perception、control 還是 plan grammar？
- SAM3 prompt alternative selection 在透明、反光、遮擋、相似物體場景下是否穩定？
- 產生的 labeled trajectories 是否真的適合訓練 VLA，還是只適合訓練特定 primitive policy？
