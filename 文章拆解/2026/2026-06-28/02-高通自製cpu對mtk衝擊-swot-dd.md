# 高通自製 CPU 對 MTK 的衝擊：以 2026 最新資料做 SWOT 與 DD 第一性分析

## 原文資訊

- 類型：使用者指定獨立專題筆記。
- 主題：高通 Oryon 自製 CPU / Snapdragon 8 Elite Gen 5 對 MTK（聯發科）在旗艦手機 SoC、出貨份額、營收份額與高階 ASP 的衝擊；補入 2026 最新公開市場資料。
- 寫作日期：2026-06-28。
- 性質：產業分析與思維筆記，不是投資建議。

## 相關新聞與資料

- [Qualcomm Oryon CPU 官方頁](https://www.qualcomm.com/processors/oryon)：高通把 Oryon 描述為自研、可跨 PC、手機、車用等平台調整的 CPU 架構，重點是效能、功耗與可依平台客製。
- [9to5Google：Snapdragon 8 Elite has Oryon CPU for notable performance and efficiency gains](https://9to5google.com/2024/10/21/qualcomm-snapdragon-8-elite/)：指出 Snapdragon 8 Elite 把 Oryon 帶進 Android 手機，採 2 個 Prime + 6 個 Performance cores，沒有 efficiency cores；高通宣稱 CPU 單核與多核效能比 Snapdragon 8 Gen 3 提升 45%，CPU 功耗效率提升 44%。
- [Android Authority：Snapdragon 8 Elite deep dive](https://www.androidauthority.com/snapdragon-8-elite-deep-dive-3491526/)：把 Snapdragon 8 Elite 稱為高通回到自製 CPU 的重大轉向；文章也提醒，自製微架構帶來差異化，但伴隨更高開發成本與熱/功耗驗證壓力。
- [MediaTek Dimensity 9400 官方頁](https://www.mediatek.com/products/smartphones/mediatek-dimensity-9400)：聯發科主打全大核 CPU、Arm Cortex-X925、AI、影像與遊戲能力，代表它仍採 Arm 公版/半客製核心路線來拚旗艦。
- [Android Authority：Dimensity 9400 遊戲實測](https://www.androidauthority.com/gaming-on-mediatek-dimensity-9400-3510050/)：實測認為 Dimensity 9400 與 Snapdragon 8 Elite 在實際遊戲中差距很小，甚至在熱與長時間穩定性上不必然輸。
- [硬是要學：Snapdragon 8 Elite 與天璣 9400 跑分比較](https://www.soft4fun.net/tech/news/snapdragon-8-elite-vs-dimensity-9400.htm)：中文科技媒體也把兩者放在同一個旗艦對位框架裡，顯示市場已把「高通 Oryon 自製 CPU vs 聯發科天璣全大核」當成下一輪旗艦手機 SoC 故事線。
- [Counterpoint：Global Smartphone AP-SoC Market Share: Quarterly，更新至 Q1 2026](https://www.counterpointresearch.com/en/insights/global-smartphone-apsoc-market-share-quarterly)：MediaTek 在 Q1 2026 仍居出貨第一，但份額由 Q1 2025 的 38% 降至 32%；Qualcomm 由 27% 降至 23%；Apple 由 15% 升至 19%；UNISOC 由 10% 升至 14%。Counterpoint 將 MediaTek / Qualcomm 的中低階壓力指向記憶體短缺與成本上升。
- [MalaysianWireless 轉述 Counterpoint Q4 2025 SoC tracker](https://www.malaysianwireless.com/2026/03/apple-overtakes-qualcomm-q4-2025-soc-market/)：Q4 2025 MediaTek 仍為出貨第一，約 30%；Apple 23% 超過 Qualcomm 22%；MediaTek premium-tier shipments 因 Dimensity 9500、vivo X300、OPPO Find X9 增加，但 mainstream / entry-level 下滑。
- [MediaTek 2025 Q4 / FY2025 Earnings Release](https://www.mediatek.com/hubfs/MediaTek%20Assets/Pdfs/Quarterly%20Earnings%20Release/2025/Quarterly%20Earnings%20Release-2025Q4/Press%20Release.pdf)：2025 全年營收新台幣 5,959.66 億元、年增 12.3%；Q4 營收新台幣 1,501.88 億元，季增 5.7%、年增 8.8%，主要受 flagship SoC Dimensity 9500 強勁放量帶動；但 2025 全年毛利率 47.5%，年減 2.1 個百分點。
- [Wccftech 轉述 MediaTek Q2 2025 earnings / presentation](https://wccftech.com/mediatek-q2-2025-earnings-3-billion-flagship-chipset-revenue-and-more-details/)：MediaTek 旗艦晶片收入在 Q2 2025 達約 30 億美元，並預期年增約 40%；同文也提到 mobile revenue 約占 Q2 2025 營收 52%、Q2 2025 gross margin 約 49.1%。這是第三方轉述，可靠性低於官方財報，但可作為旗艦收入量級參考。
- [MediaTek 2026 年 5 月營收公告](https://www.mediatek.com/hubfs/MediaTek%20Assets/Pdfs/Monthly%20Reports/2026/Monthly%20Sales%20Revenue%20May,%202026.pdf)：2026 年 5 月合併營收新台幣 474.34 億元，年增 4.99%；2026 年 1–5 月累計營收新台幣 2,433.21 億元，年減 1.59%。
- [Qualcomm FY2026 Q2 Earnings Release](https://s204.q4cdn.com/645488518/files/doc_financials/2026/q2/FY2026-2nd-Quarter-Earnings-Release.pdf)：FY2026 Q2 QCT handset revenue 為 60.24 億美元，年減 13%；Qualcomm 同樣提到記憶體供給限制與相關價格對 handset OEM 需求的影響，並預期中國客戶 handset revenue 在 FY2026 Q3 觸底後恢復季增。
- [Qualcomm：Snapdragon 8 Elite Gen 5 發表](https://www.qualcomm.com/news/releases/2025/09/snapdragon-8-elite-gen-5--the-world-s-fastest-mobile-system-on-a)：3rd Gen Qualcomm Oryon CPU、3nm、on-device / agentic AI，官方宣稱 CPU 效能提升 20%、GPU 23%、NPU 37%，並列出 Samsung、OPPO、vivo、小米、Honor、OnePlus 等旗艦 OEM。
- [MediaTek Dimensity 9500 官方頁](https://www.mediatek.com/products/smartphones/mediatek-dimensity-9500)：Dimensity 9500 主打 N3P、Armv9.3、C1-Ultra / C1-Premium / C1-Pro CPU、NPU 990、agentic AI、raytracing、4K120 / 8K 影像能力，代表 MTK 2026 旗艦對抗已從 Dimensity 9400 推進到 9500 世代。
- [Counterpoint：Global Smartphone AP Market Share Q4 2024](https://www.counterpointresearch.com/report/infographic-global-smartphone-ap-market-share-q4-2024)：較舊但仍有用的公開營收份額參考：MediaTek Q4 2024 出貨份額 34%、營收份額 13%；高通出貨份額 21%、營收份額 34%，顯示高通高階 ASP 優勢。

## 一句話拆解

用 2026 最新公開資料重看，高通自製 CPU 對 MTK 的威脅不能簡化成「Snapdragon 立刻搶走出貨第一」。更準確地說：MTK 仍守住 AP/SoC 出貨第一，但 Q1 2026 份額年減 6 個百分點；同時 Apple、Samsung Exynos、UNISOC 都在不同價格帶分食結構性成長。高通 Oryon 的真正威脅，是把高階 Android SoC 的價值池重新綁到「自研微架構 + 高階品牌敘事 + 跨平台生態」上，讓 MTK 即使守住量，也可能被壓住營收份額與高 ASP 上限。

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

### 3. 2026 最新市場資料：MTK 仍第一，但「第一」的品質變差

Counterpoint 最新公開季資料更新到 Q1 2026。若只看出貨份額，MTK 仍是全球智慧手機 AP/SoC 第一；但和一年前比，壓力已很明顯：

| 廠商 | Q1 2025 出貨份額 | Q1 2026 出貨份額 | 年變化 |
|---|---:|---:|---:|
| MediaTek | 38% | 32% | -6 個百分點，約 -15.8% |
| Qualcomm | 27% | 23% | -4 個百分點，約 -14.8% |
| Apple | 15% | 19% | +4 個百分點 |
| UNISOC | 10% | 14% | +4 個百分點 |
| Samsung | 5% | 7% | +2 個百分點 |
| HiSilicon | 4% | 4% | 持平 |

這組數字讓原本的「高通 vs MTK」問題變得更複雜。2026 的市場衝擊不是只有高通 Oryon 一條線，而是三條線同時發生：

1. **中低階手機被記憶體成本擠壓**：Counterpoint 指出 mainstream / entry-level 受記憶體短缺影響；這直接打到 MTK 的量大基本盤，也打到 Qualcomm Snapdragon 400 / 600。
2. **高階與垂直整合者變強**：Apple Q1 2026 份額升到 19%，Samsung Exynos 升到 7%，代表高階與品牌自研晶片正在吃掉更多結構性注意力。
3. **低價位有 UNISOC 往上補位**：UNISOC 升到 14%，顯示最底部市場不是全部流向 MTK，而是有更低成本競爭者承接。

所以，2026 版的第一性判斷要修正成：**MTK 面對的不是單一高通威脅，而是上下夾擊。上方是 Qualcomm / Apple / Samsung 用高階或垂直整合拿價值池；下方是記憶體成本與 UNISOC 壓縮中低階量。**

### 4. 出貨份額與營收份額會分叉

較舊但仍可作為「價值密度」基準的 Counterpoint 2024 公開資料顯示，MTK 在出貨份額上明顯強：2024 Q1 MediaTek 約 41%、高通約 27%；Q4 2024 MediaTek 約 31%–34%、高通約 21%–25%，不同公開頁面口徑略有差異。但營收份額反過來：Q1 高通 36%、MediaTek 17%；Q4 高通 34%、MediaTek 13%。

這代表高通出貨少，但平均價值密度更高。用粗略的「營收份額 / 出貨份額」估相對 ASP：

| 期間 | MTK 出貨份額 | MTK 營收份額 | 高通出貨份額 | 高通營收份額 | 高通相對 ASP / MTK 相對 ASP |
|---|---:|---:|---:|---:|---:|
| 2024 Q1 | 40% | 17% | 23% | 36% | 約 3.7 倍 |
| 2024 Q4 | 34% | 13% | 21% | 34% | 約 4.2 倍 |

這不是實際售價，只是相對價值密度。但方向很清楚：MTK 的量大，卻不等於拿到最多價值。

2026 最新資料還沒有同等公開、完整的「營收份額」表，因此不能假裝已知道 Q1 2026 的 revenue share。但我們可以用三個財務訊號判斷壓力方向：

- MediaTek 2025 全年營收新台幣 5,959.66 億元，年增 12.3%；Q4 2025 Dimensity 9500 強勁放量，證明 MTK 的旗艦上攻仍有效。
- 但 MediaTek 2025 全年毛利率 47.5%，年減 2.1 個百分點；2026 年 1–5 月累計營收年減 1.59%，表示高階放量沒有完全抵消整體產品組合 / 中低階壓力。
- Qualcomm FY2026 Q2 handset revenue 年減 13%，說明高通也受 handset 市場與記憶體成本影響；所以 2026 不是「高通全面順風、MTK 全面逆風」，而是 Android SoC 整體被成本與換機需求壓住，高階價值池變得更重要。

### 5. Oryon 的殺傷力在「高 ASP 池」，不是全部 Android 市場

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

3. **Dimensity 9400 / 9500 已證明可打進旗艦敘事**  
   2024 年 Dimensity 9400 讓 flagship revenue 超過 20 億美元；到 2025 Q4，MediaTek 又明確說 Q4 營收季增主要受 Dimensity 9500 強勁放量帶動。這表示天璣旗艦不是只有聲量，也已連續兩代形成營收貢獻。

4. **整機體驗不必然輸高通**  
   Android Authority 遊戲實測顯示 Dimensity 9400 與 Snapdragon 8 Elite 在實際遊戲表現接近，甚至在熱與長時間穩定性上有可辯護空間。

### Weaknesses｜弱點

1. **出貨第一的含金量下降，ASP 結構仍偏低**  
   Q1 2026 MediaTek 出貨份額仍是第一，但已由 Q1 2025 的 38% 降至 32%。較舊的 2024 公開營收份額也顯示，MediaTek 出貨高但營收份額低於高通。這是最核心弱點：MTK 拿到的量多，但高價值池仍多由高通、Apple 與部分垂直整合品牌分走。

2. **缺少自製 CPU 的底層差異化敘事**  
   MTK 可以說「我整合得很好」，但高通可以說「CPU 微架構是我自己定義」。在旗艦品牌行銷與投資人敘事上，後者更容易形成技術護城河想像。

3. **高階品牌慣性仍偏向 Snapdragon**  
   很多 Android 旗艦使用 Snapdragon 已形成消費者認知、遊戲開發者最佳化與通路話術。MTK 要打進高階，不只是效能問題，也是品牌信任切換成本。

4. **旗艦突破仍可能集中在少數中國品牌機型**  
   Q4 2025 Counterpoint 轉述資料提到 Dimensity 9500 受 vivo X300、OPPO Find X9 帶動。這是好消息，但也提醒 DD 重點：如果天璣旗艦主要集中在少數中國 OEM，而不是跨 Samsung、全球通路、摺疊機與 Ultra 系列廣泛滲透，旗艦營收成長會比較脆弱。

### Opportunities｜機會

1. **若高通 Oryon 手機偏熱或成本過高，MTK 可用穩定與性價比切入**  
   自製 CPU 不是免費午餐。若高通峰值高但熱、功耗、BOM 或供貨壓力大，MTK 可用「長時間穩定體驗 + 成本彈性」爭取高階以外的大量設計案。

2. **中國 Android 品牌需要第二供應來源**  
   OEM 不希望高階完全被高通鎖住。若 MTK 能提供接近旗艦體驗，品牌端有動機扶持天璣作為談判籌碼與產品差異化來源。

3. **AI、影像、連線與電源管理可重新定義旗艦價值**  
   如果未來手機旗艦差異從 CPU 峰值轉向端側 AI、影像 pipeline、低功耗 always-on、連線與遊戲穩定性，MTK 可把戰場拉回 SoC 整體能力。

4. **ASIC / 車用 / Edge / AI PC 平台可分散手機旗艦壓力**  
   MediaTek 官方產品線已把 smartphone、AI PC、automotive、ASIC / data center 等放在同一個 broader edge-to-cloud 敘事裡。若這些線成長，手機旗艦的敘事壓力仍重要，但不會是唯一成長來源。

### Threats｜威脅

1. **高通以 Oryon 建立跨手機、PC、車用的一體化平台敘事**  
   這會讓高通更像「Android 陣營的 Apple 式底層能力供應商」，而 MTK 容易被定位為高效率整合者。

2. **Samsung 與高階中國旗艦若持續偏向 Snapdragon，MTK 高階 ASP 上攻受限**  
   旗艦設計案具有示範效果。Snapdragon 8 Elite Gen 5 官方列出 Samsung、OPPO、vivo、小米、Honor、OnePlus 等 OEM；若 Oryon 持續拿到最有曝光度的 Android 旗艦，MTK 的營收份額很難明顯上升。

3. **高通可能把自製 CPU 下放到更低價格帶**  
   Oryon 若逐步擴展到次旗艦或中高階，會壓縮 MTK 原本較舒服的中高階空間。

4. **市場把「自研 CPU」等同於高階護城河**  
   即使實際體驗差距不大，只要消費者、媒體與投資人相信自研 CPU 更高階，MTK 就需要付出更多行銷與實測證據來抵消敘事劣勢。2026 尤其麻煩的是，高通已把 Oryon 和 agentic AI、PC / data center / 車用敘事相連，不只是手機 CPU 敘事。

5. **中低階記憶體成本與 UNISOC 分食，壓縮 MTK 基本盤**  
   Q1 2026 UNISOC 出貨份額升至 14%，加上記憶體成本推高中低階手機價格，MTK 不是只在高階被高通壓，在低端也面臨價格帶經濟性變差與更低成本對手補位。

## DD｜Due Diligence 檢查框架

以下不是投資建議，而是如果要做產業 / 公司 DD，應該持續追蹤的問題清單。

### A. 市場份額 DD：不要只看出貨，要拆成四層

1. **總 AP/SoC 出貨份額**  
   MTK 是否繼續維持全球第一？Q1 2026 仍第一，但已從 Q1 2025 的 38% 降到 32%。如果後續跌破 30% 或被 Apple / Qualcomm 拉近，就代表基本盤鬆動。

2. **5G AP/SoC 出貨份額**  
   低階 LTE 向 5G 遷移時，MTK 是否能吃到升級紅利？這決定中低階基本盤的品質。

3. **高階 Android 設計案份額**  
   應追蹤 Pro / Ultra / Fold / Flip / gaming phone / Samsung Galaxy S 系列中的天璣採用率，而不是只看總量。

4. **營收份額與 ASP 指標**  
   如果 MTK 出貨份額高但營收份額不升，代表仍停在低 ASP 結構。2026 公開頁面尚未給完整 revenue share，因此 DD 上要等 Counterpoint / Canalys / Omdia 等後續資料；在資料出來前，用 flagship SoC revenue、毛利率、產品組合與高階設計案份額作替代指標。

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

MediaTek 2024 年營收新台幣約 5,306 億元，flagship revenue 超過 20 億美元。到 2025 年，全年營收升至新台幣 5,959.66 億元、年增 12.3%；Q4 2025 營收季增主要受 Dimensity 9500 強勁放量帶動。這表示旗艦上攻仍有效，但 2025 毛利率年減 2.1 個百分點、2026 年 1–5 月累計營收年減 1.59%，說明市場衝擊不能只看單一旗艦晶片聲量。

可以追蹤：

- flagship revenue 是否連續成長，而不是只因 Dimensity 9400 / 9500 單代成功。
- 毛利率是否因高階 SoC 占比增加而改善。
- 研發費用率是否因追高階而上升太快，侵蝕營業利益率。
- 高階 SoC 成長是否真的提高營收份額，而不是只替換原本中高階產品。
- 2026 年後續月營收是否擺脫 1–5 月累計年減，尤其 Q3 / Q4 旗艦新機週期能否拉回成長。

粗略情境：若以 MTK 2025 年總營收約新台幣 5,959.66 億元、旗艦收入約 30 億美元以上作基準（外媒曾轉述 2025 Q2 時 flagship chipset revenue 已達約 30 億美元；保守起見，這裡只作量級框架），若高通 Oryon / Snapdragon 8 Elite Gen 5 使 MTK 旗艦收入少拿 5%、10%、20%、30%，大致對應：

| 假設 MTK 旗艦收入流失 | 約流失金額 | 約占 MTK 2025 總營收 |
|---:|---:|---:|
| 5% | 1.5 億美元，約新台幣 48 億元 | 約 0.8% |
| 10% | 3 億美元，約新台幣 96 億元 | 約 1.6% |
| 20% | 6 億美元，約新台幣 192 億元 | 約 3.2% |
| 30% | 9 億美元，約新台幣 288 億元 | 約 4.8% |

這個估算有意保守，只看旗艦收入，不假設毛利率、產品組合與股價評價倍數變化。實際市場反應可能比營收百分比大，因為旗艦設計案代表的是未來 ASP 與品牌升級可能性。

### E. 生態 DD：高通是否把 CPU 差異轉成軟體黏著

自製 CPU 本身不是終局。真正麻煩的是高通把 Oryon 變成軟體與開發者生態：

- PC / 手機 / 車用共用工具鏈與最佳化知識。
- AI SDK、遊戲引擎、相機 pipeline 與 NPU 工具更早支援 Snapdragon。
- OEM 工程團隊更熟悉高通除錯流程。
- 開發者預設先針對 Snapdragon 旗艦最佳化。

如果這些發生，MTK 面臨的不是單一 CPU 差距，而是生態慣性差距。

## 第一性原理下的結論

### 1. MTK 不會因高通自製 CPU 立刻失去基本盤，但基本盤品質正在被測試

MTK 的量來自成本、供貨、產品線覆蓋與中低階 5G 遷移。高通 Oryon 的旗艦敘事不會直接消滅這些優勢。不過 Q1 2026 MTK 出貨份額已從一年前的 38% 降到 32%，表示基本盤不是無風險；記憶體成本、低價手機需求與 UNISOC 都在測試 MTK 的量大優勢。

### 2. 真正風險是「上攻高階 ASP 被封頂」

MTK 需要從出貨第一變成營收份額更高，才有機會改變市場對它的評價。Dimensity 9500 放量顯示它有能力往上打；但如果毛利率沒有同步改善、營收份額沒有同步上升，代表高階化仍可能被價格競爭、產品組合與 OEM 議價抵消。Oryon 若強化高通在高階 Android 的地位，就會壓住 MTK 最重要的升級路徑。

### 3. 最值得追蹤的是營收份額，不是跑分新聞

跑分容易被單代產品、散熱設計、測試條件影響。2026 起更關鍵的是：

- MTK 營收份額是否從 2024 公開資料中的 13%–17% 往上走？
- flagship revenue 是否連續成長？
- 毛利率能否停止下滑？
- 2026 年 1–5 月累計營收年減能否在旗艦新機週期後反轉？
- 高階 Android 設計案是否從單點突破變成廣泛採用？

### 4. MTK 的最佳反擊不是複製 Oryon，而是重新定義旗艦價值

MTK 不一定要馬上自研 CPU。它可以用另一組第一性原理反擊：手機使用者買的是完整體驗，而不是 CPU 微架構本身。若 MTK 能在長時間穩定、AI、影像、連線、成本與 OEM 合作上做出可感差異，就能削弱 Oryon 的敘事壓力。

## 我可以放進資料庫的筆記

- 高通 Oryon 對 MTK 的核心威脅不是出貨份額，而是高階 Android 的營收份額與 ASP 上限。
- 2026 最新 Counterpoint 出貨資料顯示：MTK Q1 2026 仍是第一，但從 Q1 2025 的 38% 降至 32%；這代表「出貨第一」仍在，但含金量正在下降。
- 2026 的市場衝擊不是單一高通威脅，而是上下夾擊：上方有 Qualcomm Oryon、Apple A-series、Samsung Exynos 的高階 / 垂直整合；下方有記憶體成本與 UNISOC 分食中低階。
- Counterpoint 2024 Q1/Q4 顯示 MTK 出貨份額領先，但高通營收份額領先；這是「量」與「價值池」分叉的典型案例。2026 目前公開資料尚不足以更新 revenue share，只能用財報、毛利率、flagship revenue 與高階設計案替代觀察。
- MediaTek 2025 全年營收新台幣 5,959.66 億元、年增 12.3%，Dimensity 9500 帶動 Q4；但全年毛利率 47.5%、年減 2.1 個百分點，2026 年 1–5 月累計營收年減 1.59%，說明旗艦放量不必然等於整體財務品質改善。
- SWOT 看，MTK 的優勢在出貨、成本、整合與中低階 5G；弱點在高階品牌敘事、自研 CPU 差異化、ASP 結構，以及中低階市場被記憶體成本與 UNISOC 侵蝕。
- DD 時要追蹤五個核心指標：高階設計案份額、營收份額、flagship revenue 連續性、毛利率是否隨高階化改善、2026 後續月營收是否擺脫累計年減。
- 第一性原理：SoC 市場的本質不是賣晶片規格，而是幫 OEM 降低整機風險、建立旗艦溢價、取得長期軟硬體支援。

## 仍不確定的地方

1. Counterpoint 的公開頁面提供 2026 出貨份額，但更細的 ASP、營收份額、價格帶、品牌別設計案資料可能在付費報告內。
2. 2026 尚未找到同等公開、完整的 AP/SoC revenue share 表，因此本文不能把 2024 的 revenue share 直接當成 2026 現況，只能當成價值密度結構基準。
3. Snapdragon 8 Elite / Snapdragon 8 Elite Gen 5、Dimensity 9400 / 9500 的實機表現高度依賴 OEM 散熱、電池、軟體調校，不能只用單一測試推論所有手機。
4. MTK 後續 Dimensity 9600、Samsung 採用情況、中國品牌 Ultra / Fold 機型導入程度，以及記憶體價格是否回落，會改變判斷。
5. 本文情境估算使用約略匯率與簡化假設，只作風險量級框架，不作財務預測。
