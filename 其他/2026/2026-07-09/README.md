# 2026-07-09 其他筆記

## AI 晶片 / 資料流架構專欄

- [Cerebras WSE-3 與 GPT-5.6 Sol：不是更大的 GPU，而是把記憶體牆拆成 90 萬個小房間](./01-cerebras-wse3-csl-gpt56-sol資料流架構專欄.md)

## 本日主題

- 這篇把使用者提供的 Cerebras / CSL / CUDA 差異論述，拆成「資料在哪裡、平行性由誰描述、通訊圖長什麼樣」三個問題。
- 查核後保留重要修正：OpenAI 官方頁面寫的是 GPT-5.6 Sol on Cerebras 最高 750 tokens/s；Cerebras 產品頁的 3,000+ tokens/s 敘事不可直接等同於 GPT-5.6 Sol；stencil 論文目前查到的是 WSE-3 對 A100 baseline 最高 342×，不是本筆記已驗證的 H100 95–210×。
