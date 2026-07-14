# LLM Prompt Interface｜Prompt 作為模型控制介面

這個專題整理一條反直覺但越來越重要的研究線：prompt 不只是人類寫給模型看的自然語言指令，也可能是改變模型內部 activation、reasoning basin 與 silent workspace 的控制介面。

## 核心問題

為什麼某些不合語法、不可讀、像 gibberish 或 nonsense 的 token sequence，會在分類、推理、jailbreak、prompt optimization 中比人類邏輯清楚的 prompt 更有效？

## 專題筆記

- [Prompt 不只是語意指令：從 RLPrompt、Nonsense Helps 到 J-space](./01-prompt-as-model-control-interface.md)

## 目前暫定模型

```text
human-readable prompt engineering
    ↓
automatic discrete prompt optimization
    ↓
gibberish / nonsense token programs
    ↓
activation redistribution + workspace steering
    ↓
J-space / J-lens 作為觀測內部概念工作區的工具
```

## 後續可補

- 用 J-lens 重跑 LoPE / Lorem perturbation 的實驗設計。
- 比較 RLPrompt、PromptQuine、GFlowPO 的 prompt search 空間。
- 將 adversarial suffix / jailbreak 與一般 reasoning improvement 分開整理。
- 追蹤 2026 之後是否有直接把 J-space 用於 prompt optimization 診斷的研究。
