# Why Multi-Step Tool-Use Reinforcement Learning Collapses and How Supervisory Signals Fix It

## 原文資訊

- 論文：Why Multi-Step Tool-Use Reinforcement Learning Collapses and How Supervisory Signals Fix It
- 作者：Yupu Hao, Zhuoran Jin, Huanxuan Liao, Kang Liu, Jun Zhao
- arXiv ID：2606.26027v1
- 分類：cs.CL, cs.LG
- 發表 / 更新：2026-06-24 / 2026-06-24
- 連結：[abs](https://arxiv.org/abs/2606.26027v1) / [pdf](https://arxiv.org/pdf/2606.26027v1)
- 本次閱讀範圍：Summary/Abstract + Introduction
- 擷取日期：2026-06-26

## 為什麼選這篇

這篇適合放進 LLM agent 與 tool use 的筆記庫，因為它不是只問「工具使用任務能不能用 RL 提升」，而是更細地問：為什麼 multi-step tool-use RL 有時會突然崩掉？這個問題比單純追求 benchmark 分數更接近 agent 訓練的工程核心。

我選它的原因是，它把 failure mode 指向「結構崩壞」而不是籠統地說模型變笨。對需要穩定呼叫工具、遵守格式、維持多步執行軌跡的 LLM agent 來說，能力本身與輸出結構其實是兩件事：模型可能仍有任務能力，但因為控制 token 或格式模式被 RL 放大，導致執行鏈條失效。

這篇也值得記錄，因為它把 supervision 與 RL 的關係放在比較務實的位置：監督訊號不只是冷啟動資料，也可能是限制探索不要破壞工具呼叫結構的護欄。不過，本次只讀摘要與 Introduction，還不能判斷實驗是否足以支持所有宣稱。

## 一句話理解

這篇論文想解釋並緩解 multi-step tool-use RL 的一種崩壞：RL 可能放大特定控制 token，讓工具呼叫格式瓦解，而適當交錯的監督訊號可以讓探索比較穩定。

## Summary / Abstract 說了什麼

摘要指出，工具使用讓 LLM 可以處理更複雜的任務，而 agentic RL 看起來有機會提升這類能力；但在實務上，只靠 RL 訓練 tool-use 任務常出現不穩定或收益有限的狀況。

作者觀察到某些模型會出現 catastrophic collapse：性能突然掉到接近失效，工具呼叫的結構也被破壞。摘要把這個現象歸因於特定 control tokens 的機率異常升高，進而干擾結構化執行；但作者同時主張，底層 tool-use 能力並沒有完全消失，而是被錯誤格式遮蔽。

為了處理這個問題，論文系統性比較多種 supervisory signals，包括 off-policy supervision、hint-based guidance、erroneous example supervision 等，並放在 synchronous 與 interleaved 兩種訓練安排下測試。摘要宣稱，interleaving SFT with RL 可以顯著改善穩定性，但在 format 與 content OOD 評估下仍會有退化。

## Introduction 的問題設定

Introduction 先把工具使用放在 LLM agent 的脈絡中：外部工具讓模型不只是產生文字，也能透過 API 或環境互動完成複雜任務。這類任務的難點在於 multi-step、structured interaction，以及工具回饋的多樣性。

接著，作者指出 RL-based optimization 在 tool-use setting 裡常有 training instability 與 limited performance gains。他們在實驗中觀察到錯誤回應頻率會在某些階段突然上升，甚至出現 performance collapse 與 valid tool-invocation structure breakdown。

Introduction 的核心主張是：這種失敗不一定代表模型失去推理能力，而可能是特定 control tokens 的機率被異常放大，使結構化生成逐步退化成錯誤執行模式。換句話說，問題是 execution format 的結構被 RL 訓練污染，而不只是 task competence 不足。

作者因此提出問題：監督訊號應該如何與 RL 結合？哪些 supervision 形式最能提升穩定性？Introduction 列出的比較範圍包含 SFT then RL、off-policy supervision、erroneous trajectory supervision、hint-based guidance，以及 Process Reflection Supervision；同時也比較 synchronous 與 interleaved training paradigms。

最後，作者宣稱三個貢獻：辨識 multi-turn agentic RL 的 structural collapse；指出 control-token dynamics 是不穩定的 token-level 機制；系統性研究 supervision 如何穩定 multi-turn agentic RL，並提出把中間 trajectory information 轉成文字監督訊號的 Process Reflection Supervision。

## 研究的第一性問題

### 基本問題

LLM agent 的工具使用不只是「知道該用哪個工具」，還包括能不能在多步互動中維持正確的控制結構。對工具呼叫任務來說，格式、特殊 token、步驟邊界與回饋解析都是任務能力的一部分。

### 約束

- RL 的 scalar reward 可能只回饋任務成功與否，未必直接保護中間格式結構。
- 多步工具使用的錯誤會累積；前面格式壞掉，後面能力再強也無法正常執行。
- 特殊 control tokens 的機率分布若被錯誤放大，可能造成系統性崩壞。
- SFT 有助於穩定格式，但也可能帶來泛化與探索能力的限制。
- OOD 格式與內容評估可能揭露「穩定」其實只是對訓練分布格式的適應。

### 既有方法卡點

單純 RL 容易把「得到 reward」與「維持可執行結構」混在一起。若 reward 對格式破壞不夠敏感，訓練可能沿著短期有效但結構脆弱的方向走。

單純 SFT 也不是完整答案。它能提供好的初始化與格式範例，但可能限制模型在新工具、新格式、新任務上的探索。因此這篇論文關注的不是 SFT 或 RL 哪一個比較好，而是 supervision 何時、如何、以什麼形式介入 RL。

### 作者試圖移動的邊界

作者試圖把 agentic RL 的失敗分析從「模型能力退化」移到「結構控制退化」。如果這個 framing 成立，那麼穩定訓練 agent 不只需要更好的 reward，也需要能約束 token-level execution pattern 的監督訊號。

## 可能的貢獻與限制（只基於 summary + introduction）

### 論文自稱

- 在 multi-turn agentic RL 中辨識出 structural collapse 這個失敗模式。
- 主張 collapse 不主要是 reasoning ability 消失，而是工具呼叫結構被 malformed control-token sequences 破壞。
- 指出 RL 可能 disproportionally amplify special control tokens，造成 policy mass 重新分配。
- 系統性比較多種 supervisory signals 與 synchronous / interleaved training paradigms。
- 宣稱 interleaved SFT with RL 對穩定性有明顯幫助，但在 format/content OOD 下仍有退化。
- 提出 Process Reflection Supervision，把中間軌跡中的資訊轉成文字監督訊號。

### 我的保守判讀

這篇最有價值的地方可能是 failure diagnosis，而不只是某個 supervision recipe。它提醒我們，agent 訓練的崩壞常常不是「模型不會想」，而是「模型還會想，但輸出接口壞了」。對工具使用來說，接口壞掉就等同能力無法被執行。

但因為本次只讀 summary 與 Introduction，我還不能判斷：

- control-token probability spikes 的分析是否足夠排除其他原因；
- catastrophic collapse 是否只出現在特定模型、資料集或格式設計；
- interleaved SFT + RL 的改善是否來自 supervision 本身，還是來自訓練節奏、學習率或資料混合；
- Process Reflection Supervision 相對其他 supervision 的獨立貢獻有多大；
- OOD 退化的程度是否會限制方法在真實 agent 系統中的可用性。

## 可放進資料庫的筆記

- Tool-use agent 的能力可以拆成兩層：任務推理能力，以及維持可執行工具呼叫結構的能力。
- RL collapse 不一定是 capability collapse，也可能是 interface / format collapse。
- 特殊 control token 是 agent 系統裡的結構骨架；一旦 token-level dynamics 被錯誤放大，整個執行流程可能失效。
- Scalar reward 可能不足以保護多步工具使用中的中間結構，需要額外 supervision 或 process-level constraint。
- SFT 在 agentic RL 中不只是 pretraining，也可以是穩定探索邊界的調節器。
- Interleaving supervision and RL 可以被理解成「讓模型探索，但定期校準結構語法」。
- OOD format 評估很重要，因為 agent 的穩定性不應只等於熟悉模板下的穩定。
- 對 LLM agent 來說，格式不是表面細節；格式就是可執行性的條件。

## 後續想追的問題

1. 這篇如何量化 control-token probability spikes？是否有可重現的診斷指標？
2. collapse 是否和特定 tool-call schema 或 special token 設計有關？
3. interleaved SFT with RL 的最佳節奏是固定比例，還是應該由 collapse risk 自適應調整？
4. Process Reflection Supervision 是否真的優於簡單的 successful trajectory replay？
5. 如果換成更開放的真實工具環境，這種 structural collapse 會更嚴重還是比較少見？

## 我的理解總結

這篇論文把 multi-step tool-use RL 的失敗看成一種「結構執行層」問題：模型可能仍保有工具使用能力，但 RL 過程把控制 token 與格式分布推歪，讓能力無法透過正確接口表現出來。它對 LLM agent 的提醒是：訓練更會探索的模型之前，要先確保模型不會把自己與工具之間的語法拆掉。
