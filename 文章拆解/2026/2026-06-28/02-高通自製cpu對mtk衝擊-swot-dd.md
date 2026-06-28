# 高通自製 CPU 對 MTK 的衝擊：以第一性原理做 SWOT 與 DD 分析

## 原文資訊

- 類型：使用者指定獨立專題筆記。
- 主題：高通 Oryon 自製 CPU / Snapdragon 8 Elite 對 MTK（聯發科）在旗艦手機 SoC、營收份額與高階 ASP 的衝擊。
- 寫作日期：2026-06-28。
- 性質：產業分析與思維筆記，不是投資建議。

## 相關新聞與資料

- [Qualcomm Oryon CPU 官方頁](https://www.qualcomm.com/processors/oryon)：高通把 Oryon 描述為自研、可跨 PC、手機、車用等平台調整的 CPU 架構，重點是效能、功耗與可依平台客製。
- [9to5Google：Snapdragon 8 Elite has Oryon CPU for notable performance and efficiency gains](https://9to5google.com/2024/10/21/qualcomm-snapdragon-8-elite/)：指出 Snapdragon 8 Elite 把 Oryon 帶進 Android 手機，採 2 個 Prime + 6 個 Performance cores，沒有 efficiency cores；高通宣稱 CPU 單核與多核效能比 Snapdragon 8 Gen 3 提升 45%，CPU 功耗效率提升 44%。
- [Android Authority：Snapdragon 8 Elite deep dive](https://www.androidauthority.com/snapdragon-8-elite-deep-dive-3491526/)：把 Snapdragon 8 Elite 稱為高通回到自製 CPU 的重大轉向；文章也提醒，自製微架構帶來差異化，但伴隨更高開發成本與熱/功耗驗證壓力。
- [MediaTek Dimensity 9400 官方頁](https://www.mediatek.com/products/smartphones/mediatek-dimensity-9400)：聯發科主打全大核 CPU、Arm Cortex-X925、AI、影像與遊戲能力，代表它仍採 Arm 公版/半客製核心路線來拚旗艦。
- [Android Authority：Dimensity 9400 遊戲實測](https://www.androidauthority.com/gaming-on-mediatek-dimensity-9400-3510050/)：實測認為 Dimensity 9400 與 Snapdragon 8 Elite 在實際遊戲中差距很小，甚至在熱與長時間穩定性上不必然輸。
- [硬是要學：Snapdragon 8 Elite 與天璣 9400 跑分比較](https://www.soft4fun.net/tech/news/snapdragon-8-elite-vs-dimensity-9400.htm)：中文科技媒體也把兩者放在同一個旗艦對位框架裡，顯示市場已把「高通 Oryon 自製 CPU vs 聯發科天璣全大核」當成下一輪旗艦手機 SoC 故事線。
- [Counterpoint：Global Smartphone AP Market Share Q1 2024](https://www.counterpointresearch.com/en/reports/infographic-global-smartphone-ap-market-share-q1-2024)：MediaTek 以 40% 出貨份額領先，高通為 23%；但營收份額高通 36%、MediaTek 17%。
- [Counterpoint：Global Smartphone AP Market Share Q4 2024](https://www.counterpointresearch.com/report/infographic-global-smartphone-ap-market-share-q4-2024)：MediaTek Q4 2024 出貨份額仍為 34%，高通為 21%；但營收份額高通 34%、MediaTek 13%；Snapdragon 8 Elite 與 Galaxy S25 設計案強化高通高階營收敘事。
- [MediaTek 2024 Annual Report](https://cdn-www.mediatek.com/posts/2024-English-Annual-Report.pdf)：MediaTek 2024 年營收新台幣 5,306 億元、年增 22.4%；Dimensity 9400 帶動 flagship revenue 翻倍，2024 年貢獻超過 20 億美元。

## 一句話拆解

高通自製 CPU 對 MTK 的真正威脅，不是馬上搶走 MTK 的出貨第一，而是把高階 Android SoC 的價值池重新綁到「自研微架構 + 高階品牌敘事 + 跨平台生態」上，讓 MTK 即使守住量，也可能被壓住營收份額與高 ASP 上限。

## 第一性原理拆解

### 1. 手機 SoC 市場賣的不是「晶片」，而是 OEM 的產品風險控制

手機品牌買 SoC 時，表面上買 CPU、GPU、NPU、ISP、modem；底層其實買的是：

1. **上市確定性**：能不能準時量產、驅動穩定、Android 版本與相機模組順利整合。
2. **旗艦敘事**：能不能讓售價更高的 Pro / Ultra / Fold 系列講出差異。
3. **長期支援成本**：韌體、modem、影像調校、遊戲最佳化、AI SDK、供應鏈共同除錯。
4. **失敗風險轉嫁**：如果熱、續航、訊號、相機或遊戲表現出問題，品牌能不能找到可信任的上游一起解。

所以 SoC 競爭不是單純跑分。跑分只是銷售故事的入口；真正決定高階設計案的是「整機風險 / 旗艦溢價 / 合作成本」的總和。

### 2. 自製 CPU 的本質是「把不可替代性往底層移」

MTK 使用 Arm 最新核心時，可以靠整合能力、成本、節奏與功耗調校追上高通。這條路的好處是研發槓桿高，壞處是核心 IP 的差異化有限。

高通 Oryon 的意義在於：高通把差異化從 SoC 整合層往 CPU 微架構層下移。這讓高通可以對 OEM 說：

> 這不只是另一顆使用 Arm 公版核心的旗艦 SoC；這是高通自己定義 CPU 微架構，並把它擴展到手機、PC、車用與座艙的技術家族。

如果市場相信這個敘事，高通就能把高階 Android SoC 的溢價理由重新包裝成「自研底層能力」。MTK 的問題不是不能打，而是它必須用別的證據證明：不自製 CPU 仍能提供同等或更好的整機價值。

### 3. 出貨份額與營收份額會分叉

Counterpoint 的資料顯示，MTK 在出貨份額上明顯強：2024 Q1 MediaTek 40%、高通 23%；Q4 MediaTek 34%、高通 21%。但營收份額反過來：Q1 高通 36%、MediaTek 17%；Q4 高通 34%、MediaTek 13%。

這代表高通出貨少，但平均價值密度更高。用粗略的「營收份額 / 出貨份額」估相對 ASP：

| 期間 | MTK 出貨份額 | MTK 營收份額 | 高通出貨份額 | 高通營收份額 | 高通相對 ASP / MTK 相對 ASP |
|---|---:|---:|---:|---:|---:|
| 2024 Q1 | 40% | 17% | 23% | 36% | 約 3.7 倍 |
| 2024 Q4 | 34% | 13% | 21% | 34% | 約 4.2 倍 |

這不是實際售價，只是相對價值密度。但方向很清楚：MTK 的量大，卻不等於拿到最多價值。

### 4. Oryon 的殺傷力在「高 ASP 池」，不是全部 Android 市場

MTK 的基本盤是中低階與中階 5G 的大出貨。高通 Oryon 不太可能在短期內把這塊全部吃掉；高通也未必想用旗艦 Oryon 去打最便宜的量。

真正的戰場是：

- Samsung Galaxy S / Fold / Flip 這類高曝光 Android 旗艦。
- 中國品牌 Ultra / Pro / 遊戲手機 / 摺疊機。
- 需要高階影像、遊戲、AI、modem 與長期更新承諾的機型。
- 可以支撐更高 SoC ASP 與品牌聯名行銷的設計案。

如果高通守住這些高階設計案，MTK 即使繼續出貨第一，也可能被固定在「量大但營收份額低」的位置。

## SWOT 分析：MTK 面對高通 Oryon

### Strengths｜優勢

1. **出貨規模與中低階 5G 基本盤強**  
   MTK 在全球智慧手機 AP/SoC 出貨份額上仍領先，代表它對 OEM 的成本、供貨與產品線覆蓋很有吸引力。

2. **Arm 公版核心路線的研發效率高**  
   採用 Arm 最新核心讓 MTK 不必承擔完整自研 CPU 的長周期風險，可把資源集中在 SoC 整合、功耗、影像、AI、連線與客戶支援。

3. **Dimensity 9400 已證明可打進旗艦敘事**  
   MTK 年報指出 flagship revenue 2024 年翻倍、貢獻超過 20 億美元，表示天璣旗艦不是只有聲量，也開始有實質營收貢獻。

4. **整機體驗不必然輸高通**  
   Android Authority 遊戲實測顯示 Dimensity 9400 與 Snapdragon 8 Elite 在實際遊戲表現接近，甚至在熱與長時間穩定性上有可辯護空間。

### Weaknesses｜弱點

1. **營收份額低於出貨份額，ASP 結構偏低**  
   2024 Q4 MediaTek 出貨份額 34%、營收份額 13%；高通出貨份額 21%、營收份額 34%。這是最核心弱點：MTK 拿到的量多，但高價值池仍多由高通與 Apple 分走。

2. **缺少自製 CPU 的底層差異化敘事**  
   MTK 可以說「我整合得很好」，但高通可以說「CPU 微架構是我自己定義」。在旗艦品牌行銷與投資人敘事上，後者更容易形成技術護城河想像。

3. **高階品牌慣性仍偏向 Snapdragon**  
   很多 Android 旗艦使用 Snapdragon 已形成消費者認知、遊戲開發者最佳化與通路話術。MTK 要打進高階，不只是效能問題，也是品牌信任切換成本。

4. **旗艦突破仍可能集中在少數中國品牌機型**  
   如果天璣旗艦主要被少數 OEM 採用，而不是跨 Samsung、全球通路、摺疊機與 Ultra 系列廣泛滲透，旗艦營收成長會比較脆弱。

### Opportunities｜機會

1. **若高通 Oryon 手機偏熱或成本過高，MTK 可用穩定與性價比切入**  
   自製 CPU 不是免費午餐。若高通峰值高但熱、功耗、BOM 或供貨壓力大，MTK 可用「長時間穩定體驗 + 成本彈性」爭取高階以外的大量設計案。

2. **中國 Android 品牌需要第二供應來源**  
   OEM 不希望高階完全被高通鎖住。若 MTK 能提供接近旗艦體驗，品牌端有動機扶持天璣作為談判籌碼與產品差異化來源。

3. **AI、影像、連線與電源管理可重新定義旗艦價值**  
   如果未來手機旗艦差異從 CPU 峰值轉向端側 AI、影像 pipeline、低功耗 always-on、連線與遊戲穩定性，MTK 可把戰場拉回 SoC 整體能力。

4. **ASIC / 車用 / Edge 平台可分散手機旗艦壓力**  
   MTK 年報也提到 ASIC、車用、連線等方向。若這些線成長，手機旗艦的敘事壓力仍重要，但不會是唯一成長來源。

### Threats｜威脅

1. **高通以 Oryon 建立跨手機、PC、車用的一體化平台敘事**  
   這會讓高通更像「Android 陣營的 Apple 式底層能力供應商」，而 MTK 容易被定位為高效率整合者。

2. **Samsung 與高階中國旗艦若持續偏向 Snapdragon，MTK 高階 ASP 上攻受限**  
   旗艦設計案具有示範效果。若 Snapdragon 8 Elite / 後續 Oryon 持續拿到最有曝光度的 Android 旗艦，MTK 的營收份額很難明顯上升。

3. **高通可能把自製 CPU 下放到更低價格帶**  
   Oryon 若逐步擴展到次旗艦或中高階，會壓縮 MTK 原本較舒服的中高階空間。

4. **市場把「自研 CPU」等同於高階護城河**  
   即使實際體驗差距不大，只要消費者、媒體與投資人相信自研 CPU 更高階，MTK 就需要付出更多行銷與實測證據來抵消敘事劣勢。

## DD｜Due Diligence 檢查框架

以下不是投資建議，而是如果要做產業 / 公司 DD，應該持續追蹤的問題清單。

### A. 市場份額 DD：不要只看出貨，要拆成四層

1. **總 AP/SoC 出貨份額**  
   MTK 是否繼續維持全球第一？如果出貨第一消失，代表基本盤鬆動。

2. **5G AP/SoC 出貨份額**  
   低階 LTE 向 5G 遷移時，MTK 是否能吃到升級紅利？這決定中低階基本盤的品質。

3. **高階 Android 設計案份額**  
   應追蹤 Pro / Ultra / Fold / Flip / gaming phone / Samsung Galaxy S 系列中的天璣採用率，而不是只看總量。

4. **營收份額與 ASP 指標**  
   如果 MTK 出貨份額高但營收份額不升，代表仍停在低 ASP 結構。最重要的變化是營收份額能不能從 13%–17% 往上走。

### B. 產品 DD：Oryon 是否真的形成可持續差距

要看：

- 單核 / 多核峰值是否穩定領先。
- 長時間負載下是否過熱或降頻。
- 真實遊戲、相機、AI 推理與多工是否有可感差距。
- OEM 實機是否普遍表現好，而不是只在 Qualcomm reference design 好。
- 後續 Oryon 世代是否能每年穩定迭代。

若 Oryon 只是單代峰值漂亮，但實機體驗不穩，MTK 的威脅會下降。若 Oryon 連續幾代都能在效能、功耗、AI 與跨平台開發工具上穩定領先，MTK 的高階敘事會更吃力。

### C. 客戶 DD：誰在用天璣旗艦，誰只拿來議價

關鍵問題：

- 天璣旗艦是否進入全球銷量大的機型，還是只在中國市場特定型號？
- OEM 是把天璣放在最高階 Ultra，還是放在次旗艦 / 地區限定版？
- Samsung 是否有更多 MediaTek 高階採用，或仍主要偏向 Qualcomm / Exynos？
- 中國品牌是否願意用天璣做最貴機型，而不只是用來降低 BOM 或對高通議價？

如果 OEM 只把 MTK 當「高通備胎」或議價工具，MTK 的旗艦收入很難真正重估。

### D. 財務 DD：旗艦收入是否改善整體毛利與營收份額

MediaTek 2024 年營收新台幣 5,306 億元，flagship revenue 超過 20 億美元。這給我們一個觀察基準。

可以追蹤：

- flagship revenue 是否連續成長，而不是只因 Dimensity 9400 單代成功。
- 毛利率是否因高階 SoC 占比增加而改善。
- 研發費用率是否因追高階而上升太快，侵蝕營業利益率。
- 高階 SoC 成長是否真的提高營收份額，而不是只替換原本中高階產品。

粗略情境：若以 MTK 2024 年總營收約新台幣 5,306 億元、旗艦收入約 20 億美元作基準，若高通 Oryon / Snapdragon 8 Elite 使 MTK 旗艦收入少拿 5%、10%、20%、30%，大致對應：

| 假設 MTK 旗艦收入流失 | 約流失金額 | 約占 MTK 2024 總營收 |
|---:|---:|---:|
| 5% | 1 億美元，約新台幣 32 億元 | 約 0.6% |
| 10% | 2 億美元，約新台幣 64 億元 | 約 1.2% |
| 20% | 4 億美元，約新台幣 128 億元 | 約 2.4% |
| 30% | 6 億美元，約新台幣 192 億元 | 約 3.6% |

這個估算有意保守，只看旗艦收入，不假設毛利率、產品組合與股價評價倍數變化。實際市場反應可能比營收百分比大，因為旗艦設計案代表的是未來 ASP 與品牌升級可能性。

### E. 生態 DD：高通是否把 CPU 差異轉成軟體黏著

自製 CPU 本身不是終局。真正麻煩的是高通把 Oryon 變成軟體與開發者生態：

- PC / 手機 / 車用共用工具鏈與最佳化知識。
- AI SDK、遊戲引擎、相機 pipeline 與 NPU 工具更早支援 Snapdragon。
- OEM 工程團隊更熟悉高通除錯流程。
- 開發者預設先針對 Snapdragon 旗艦最佳化。

如果這些發生，MTK 面臨的不是單一 CPU 差距，而是生態慣性差距。

## 第一性原理下的結論

### 1. MTK 不會因高通自製 CPU 立刻失去基本盤

MTK 的量來自成本、供貨、產品線覆蓋與中低階 5G 遷移。高通 Oryon 的旗艦敘事不會直接消滅這些優勢。

### 2. 真正風險是「上攻高階 ASP 被封頂」

MTK 需要從出貨第一變成營收份額更高，才有機會改變市場對它的評價。Oryon 若強化高通在高階 Android 的地位，就會壓住 MTK 最重要的升級路徑。

### 3. 最值得追蹤的是營收份額，不是跑分新聞

跑分容易被單代產品、散熱設計、測試條件影響。更關鍵的是：

- MTK 營收份額是否從 13%–17% 往上走？
- flagship revenue 是否連續成長？
- 高階 Android 設計案是否從單點突破變成廣泛採用？

### 4. MTK 的最佳反擊不是複製 Oryon，而是重新定義旗艦價值

MTK 不一定要馬上自研 CPU。它可以用另一組第一性原理反擊：手機使用者買的是完整體驗，而不是 CPU 微架構本身。若 MTK 能在長時間穩定、AI、影像、連線、成本與 OEM 合作上做出可感差異，就能削弱 Oryon 的敘事壓力。

## 我可以放進資料庫的筆記

- 高通 Oryon 對 MTK 的核心威脅不是出貨份額，而是高階 Android 的營收份額與 ASP 上限。
- Counterpoint 2024 Q1/Q4 顯示 MTK 出貨份額領先，但高通營收份額領先；這是「量」與「價值池」分叉的典型案例。
- MediaTek 2024 年 flagship revenue 超過 20 億美元，是觀察 MTK 能否真正上攻高階的關鍵基準。
- SWOT 看，MTK 的優勢在出貨、成本、整合與中低階 5G；弱點在高階品牌敘事、自研 CPU 差異化與 ASP 結構。
- DD 時要追蹤四個核心指標：高階設計案份額、營收份額、flagship revenue 連續性、毛利率是否隨高階化改善。
- 第一性原理：SoC 市場的本質不是賣晶片規格，而是幫 OEM 降低整機風險、建立旗艦溢價、取得長期軟硬體支援。

## 仍不確定的地方

1. Counterpoint 的公開頁面提供份額資訊，但更細的 ASP、價格帶、品牌別設計案資料可能在付費報告內。
2. Snapdragon 8 Elite / Dimensity 9400 的實機表現高度依賴 OEM 散熱、電池、軟體調校，不能只用單一測試推論所有手機。
3. MTK 後續 Dimensity 9500 / 9600 產品策略、Samsung 採用情況與中國品牌 Ultra 機型導入程度，會改變判斷。
4. 本文情境估算使用約略匯率與簡化假設，只作風險量級框架，不作財務預測。
