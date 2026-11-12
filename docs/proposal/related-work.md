# Related Work

## 1. Smart Hydration Monitoring

Griffith、Shi 與 Biswas 以容器附掛的三軸加速度計取得飲用動作，利用容器傾角特徵估計單次飲用量，也比較從液位變化估計累積飲用量的可行性。飲用量、初始液位與個人動作差異均會影響訊號，該研究的動作特徵建立在感測器隨容器移動的條件下。[[1]](#ref-1)

Cohen、Fernie 與 Roshan Fekr 以參考秤比較四款商用智慧水瓶，評估單次攝取量誤差、累計偏差與漏記。不同裝置在各指標的表現有差異。Baker 等人則在美式足球季前訓練中驗證光學瓶蓋，以重量法與液體密度換算作為體積參考，並分析群體平均差異及個體一致性。這些方法支持分開呈現重量誤差、事件漏記與累積紀錄品質，文獻中的量測結果仍屬各自裝置及場景。[[2]](#ref-2) [[3]](#ref-3)

## 2. Drinking Activity Recognition and Uncertain Events

Gómez-Carmona 與 Casado-Mansilla 發布的 OHM 資料集，記錄辦公室參與者操作附有六軸 IMU 的容器時的加速度、角速度及旋轉角度，並標註從瓶子飲用、從杯子飲用及其他互動。它適合用於規劃測試情境。SmartIntake 的 E2 另行蒐集杯墊重量時間序列及行為真值。[[4]](#ref-4)

單一杯墊在杯子離開時無法觀察液體流向。同樣的減重可能對應喝水、倒水或灑出。FSM 利用離杯、放回及穩定重量排除部分誤觸或不完整事件，但可觀察訊號相同時仍需人工確認或事後修正。本研究分別評估自動判定與最終紀錄，以量化確認所帶來的可靠性變化及操作負擔。見[E2 實驗設計](../experiments/experiment-plan.md#e2--hydration-event-reliability)。

## 3. Digital Dietary Logging and Nutrition Estimation

Li 等人以不同飲食型態的參考紀錄比較人工輸入及 AI 食物影像辨識 App，觀察到營養輸出差異及缺值，尤其涉及混合料理與文化特定菜餚。Fridolfsson 等人以參考份量及營養值評估三種 LLM 的影像估算，觀察到較大份量的系統性低估及營養估計變異。這些研究提示應分別查核食品匹配、份量估計及營養來源。[[5]](#ref-5) [[6]](#ref-6)

López Torres 等人稽核由自由文字及影像產生營養值的流程，發現通過內部算術一致性檢查仍可能伴隨重複輸入的再現性不足。該研究沒有獨立的營養真值作為外部參考。SmartIntake 因此保留食品條目、份量基準及確定性換算，讓估算可重算，並將估算的適用範圍交由資料來源與餐點匹配程度界定。[[7]](#ref-7)

## 4. Food Composition Databases

臺灣食品藥物管理署的食品營養成分資料集提供樣品名稱、內容物描述、分析項、單位、每 100 g 含量及部分份量欄位。USDA FoodData Central 提供多類型食品成分資料，包含分析所得的基礎食材資料與來自食品標示的品牌資料。來源類型與份量基準會影響適用性。SmartIntake 優先使用適用的臺灣條目，再以 USDA 補充。食物名稱、烹調狀態、可食部、成分單位與每份基準須一併核對。[[8]](#ref-8) [[9]](#ref-9)

單項食物以去皮後的實測淨重配合每 100 g 成分資料。混合餐點需確認參考資料適用於整餐。無法排除便當餐盒重量時，使用有來源的每份參考營養值。這兩種輸入的精度與來源在紀錄中各自呈現。參見[技術設計](../../design.md#c4-dietary-logging-and-nutrition-estimation)。

## 5. Multi-User IoT Data Attribution

指定飲水文獻的驗證焦點是容器操作、量測誤差及攝取事件，SmartIntake 的研究情境進一步涉及多人共用兩台秤。操作者可能在量測、確認或同步期間切換。如果以事件送達時的使用者決定歸屬，原事件有可能被記到新使用者名下。[[1]](#ref-1) [[2]](#ref-2) [[3]](#ref-3) [[4]](#ref-4)

本研究以兩秤共用的 Active User Session 決定新事件歸屬，並將開始時的 Session ID、User ID 與 Session Version 固定於事件。E3 以觀察紀錄核對操作者，涵蓋切換時的未完成事件及延遲送達，以辨識量測正確但歸屬錯誤的情形。見[E3 實驗設計](../experiments/experiment-plan.md#e3--multi-user-attribution)。

## 6. Domain AI Agents and Tool-Calling Reliability

Yao 等人提出 τ-bench，讓工具型 Agent 在含領域規則的互動情境中使用 API，再依預期狀態與重複執行結果評估成功及一致性。SmartIntake 將可驗證任務的設計用於唯讀查詢，以預先標註的工具、授權結果、數值與來源為參考，另查核缺資料與工具錯誤時的回覆。FastAPI 執行授權與計算，n8n 的 LLM 負責意圖、工具選擇及結果解釋。[[10]](#ref-10)

## 7. Related Work Comparison Table

| 研究或資源 | 使用的訊號或評估依據 | 對 SmartIntake 的方法啟示 |
| --- | --- | --- |
| Griffith 等人 [[1]](#ref-1) | 容器附掛的三軸加速度計 | 區分單次量估算與累積量，留意杯墊可取得的行為線索較少。 |
| Cohen 等人 [[2]](#ref-2)、Baker 等人 [[3]](#ref-3) | 商用智慧水瓶或光學瓶蓋，以參考秤或重量法對照 | 分開評估單次誤差、總量及漏記。 |
| OHM [[4]](#ref-4) | 標記辦公室容器操作的 IMU 時序 | 納入非飲用互動，另採集固定杯墊訊號。 |
| Li 等人 [[5]](#ref-5)、Fridolfsson 等人 [[6]](#ref-6) | 手動或影像紀錄、食物與營養參考值 | 追溯食品匹配、份量取得與營養資料。 |
| López Torres 等人 [[7]](#ref-7) | 營養管線輸出的一致性及再現性稽核 | 區分重算一致性與依獨立真值驗證估算準確度。 |
| 臺灣食品藥物管理署 [[8]](#ref-8)、USDA [[9]](#ref-9) | 食品成分、成分單位及參考份量 | 保留條目、資料版本、份量基準及缺值。 |
| τ-bench [[10]](#ref-10) | 領域規則、API 工具與預期狀態 | 預先定義可核對的工具、答案與授權結果。 |
| SmartIntake（本提案） | 雙秤重量、Discord 操作、工作階段快照及後端回傳 | 擬整合飲水與飲食紀錄、多人歸屬和唯讀查詢，驗證見 E1–E5。 |

## 8. Research Gap and Contributions

| 研究缺口 | 預期貢獻及驗證 |
| --- | --- |
| RG1 — 純重量飲水事件判斷與不確定事件處理 | 定義固定杯墊的候選、人工確認及可修正流程。E1 建立感測誤差基礎，E2 比較自動分類、最終紀錄及確認負擔。 |
| RG2 — 固定工作場域的飲食紀錄及多人設備工作階段整合 | 整合單項秤重、整餐估算及兩台秤共用的工作階段。E3 檢查歸屬，E4 比較紀錄效率。 |
| RG3 — 以可信資料及授權工具驗證 Domain AI Agent 可靠性 | 以唯讀工具取得後端的數值及來源，E5 評估選用工具、數值、來源、授權、安全及缺資料處理。 |

以上缺口界定本提案要驗證的問題，範圍限於指定文獻與固定場域設定。研究貢獻及其適用範圍須經[實驗計畫](../experiments/experiment-plan.md)驗證。

## 9. References

1. <a name="ref-1"></a> Griffith, H., Shi, Y., & Biswas, S. (2019). *A Container-Attachable Inertial Sensor for Real-Time Hydration Tracking*. Sensors, 19(18), 4008. [DOI 10.3390/s19184008](https://doi.org/10.3390/s19184008) · [原文](https://www.mdpi.com/1424-8220/19/18/4008)

2. <a name="ref-2"></a> Cohen, R., Fernie, G., & Roshan Fekr, A. (2022). *Monitoring fluid intake by commercially available smart water bottles*. Scientific Reports, 12, 4402. [DOI 10.1038/s41598-022-08335-5](https://doi.org/10.1038/s41598-022-08335-5) · [原文](https://pmc.ncbi.nlm.nih.gov/articles/PMC8924188/)

3. <a name="ref-3"></a> Baker, L. B., Alfred, S., Lee, K. A., Bonsignore, J. L., Osterberg, K. L., Shepherd, B., De Chavez, P. J. D., & Bentkovski, K. (2023). *Accuracy of a smart bottle in measuring fluid intake by American football players during pre-season training*. Scientific Reports, 13, 11383. [DOI 10.1038/s41598-023-38613-9](https://doi.org/10.1038/s41598-023-38613-9) · [原文](https://pmc.ncbi.nlm.nih.gov/articles/PMC10349061/)

4. <a name="ref-4"></a> Gómez-Carmona, O., & Casado-Mansilla, D. (2021). *Office Hydration Monitoring (OHM) Dataset* (v1). Zenodo. [DOI 10.5281/zenodo.4681206](https://doi.org/10.5281/zenodo.4681206) · [資料集](https://zenodo.org/records/4681206)

5. <a name="ref-5"></a> Li, X., Yin, A., Choi, H. Y., Chan, V., Allman-Farinelli, M., & Chen, J. (2024). *Evaluating the Quality and Comparative Validity of Manual Food Logging and Artificial Intelligence-Enabled Food Image Recognition in Apps for Nutrition Care*. Nutrients, 16(15), 2573. [DOI 10.3390/nu16152573](https://doi.org/10.3390/nu16152573) · [原文](https://www.mdpi.com/2072-6643/16/15/2573)

6. <a name="ref-6"></a> Fridolfsson, J., Sjöberg, E., Thiwång, M., & Pettersson, S. (2025). *Performance Evaluation of 3 Large Language Models for Nutritional Content Estimation from Food Images*. Current Developments in Nutrition, 9(10), 107556. [DOI 10.1016/j.cdnut.2025.107556](https://doi.org/10.1016/j.cdnut.2025.107556) · [原文](https://pmc.ncbi.nlm.nih.gov/articles/PMC12513282/)

7. <a name="ref-7"></a> López Torres, J. F., Castañeda, J. G., & Rivas, E. (2026). *Internal Consistency Does Not Validate an LLM-Assisted Nutrition Pipeline: A Reference-Standard-Free Forensic Reliability Audit of 13,073 Free-Text Meal Records*. Nutrients, 18(19), 3233. [DOI 10.3390/nu18193233](https://doi.org/10.3390/nu18193233) · [原文](https://www.mdpi.com/2072-6643/18/19/3233)

8. <a name="ref-8"></a> 衛生福利部食品藥物管理署。《食品營養成分資料集》。[政府資料開放平臺](https://data.gov.tw/dataset/8543)

9. <a name="ref-9"></a> U.S. Department of Agriculture, Agricultural Research Service. *FoodData Central*. [官方資料庫](https://fdc.nal.usda.gov/)

10. <a name="ref-10"></a> Yao, S., Shinn, N., Razavi, P., & Narasimhan, K. (2024). *τ-bench: A Benchmark for Tool-Agent-User Interaction in Real-World Domains*. arXiv:2406.12045. [DOI 10.48550/arXiv.2406.12045](https://doi.org/10.48550/arXiv.2406.12045) · [預印本](https://arxiv.org/abs/2406.12045) · [ICLR 2025 會議版](https://openreview.net/pdf/57cd0f8d1f7b7790714c1bedf5d781ba10e56590.pdf)
