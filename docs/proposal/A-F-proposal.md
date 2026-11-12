# SmartIntake DIC3 研究提案

## A. Attention Getter

固定工作場域中的飲水與用餐事件頻繁，手動紀錄需要中斷當前工作、估計份量並填入表單。單次省略或錯估在回顧跨日攝取量時會累積，也難以由紀錄本身判斷當初使用的是量測值或參考份量。本研究探討，能否由桌面秤承擔份量取得的步驟，讓使用者只在量測無法提供足夠證據時介入，並保留可供查核的資料來源。

## B. But / However

智慧水瓶研究指出，單次攝取誤差、累積量偏差及漏記是不同面向。對固定杯墊而言，杯子拿起後減重可能來自飲用、倒水或灑出。飲食方面，即使重量準確，混合餐點的配方、烹調與包裝仍會影響份量與營養估算。共用秤的事件可能在使用者切換後才送達，正確量測因此仍可能記到錯誤帳號。若分析介面再以 LLM 直接產生營養數字或依模型回覆決定權限，便難以追查誤差或確認資料隔離。參見[相關研究](related-work.md)。

## C. Cure

SmartIntake 以飲水杯墊與食物秤支援半自動紀錄。飲水 FSM 比對拿杯前及放回後的穩定重量，符合規則的一般候選事件自動記錄，可事後撤銷或修正。異常與不確定事件由 Discord 請原使用者確認，場域外飲水由使用者手動補登。飲食的 Mode A 以 Tare 後的單項食物淨重查表換算。Mode B 以可靠淨重配合適用的餐點資料，無法排除餐盒重量時使用有來源的每份參考值。兩台秤由三位測試使用者共用同一 Active User Session，事件以開始時的身分資訊固定歸屬。

## D. Development

Raspberry Pi 4 接上兩組 Load Cell 與 HX711。Python Sensor Worker 負責校正、濾波、穩定重量判定及 FSM。本機 SQLite 緩衝未同步事件。FastAPI 執行身分驗證、寫入授權、查詢與確定性計算，Supabase PostgreSQL 儲存正式紀錄。飲食查詢優先採臺灣食品營養成分資料，USDA FoodData Central 用作補充。

Discord Bot 提供確認、修正、補登及切換。n8n Domain AI Agent 以 LLM Tool Calling 選用六項唯讀工具，依據後端的結構化結果回答跨日趨勢、目標進度及資料來源。量測與營養計算的規則、工作階段邊界及工具清單見[技術設計](../../design.md)及[核心與取捨](5-cores-3-tradeoffs-10-details.md)。

## E. Experiments

| 實驗 | 研究問題與評估方式 |
| --- | --- |
| E1 — Weight Accuracy | 以砝碼與獨立參考重量檢驗兩台秤的誤差、穩定時間與漂移。 |
| E2 — Hydration Event Reliability | 比較 Simple Weight Difference、Weight Difference + FSM 與 FSM + Discord Confirmation 的事件表現及人工確認負擔，區分自動判定與人工修正後的紀錄。 |
| E3 — Multi-User Attribution | 在三人兩秤情境檢查歸屬錯誤、切換成功率、切換時間及未完成事件的歸屬。 |
| E4 — Logging Efficiency | 以交叉平衡順序進行同一受試者比較，量測手機表單與 SmartIntake 的操作時間、互動次數、遺漏率與完成率。 |
| E5 — Agent Reliability | 以至少 30 個固定情境檢查工具選擇、數值、來源、授權、安全與缺值處理。 |

各項實驗均為規劃中。參考真值、計分方式與適用限制見[實驗計畫](../experiments/experiment-plan.md)。

## F. Findings / Expected Outcomes

預期交付一套可回放的雙秤原型、可核對歸屬與來源的飲水及飲食紀錄，以及涵蓋感測、操作流程與 Agent 回覆的驗證方法。飲水部分將估計 FSM 與人工確認對錯記、漏記及確認成本的影響。飲食部分將評估去皮秤重及每份參考值流程的紀錄效率。Agent 部分將查核回答是否可重算、可追溯且符合權限。效益的方向、幅度與可適用場景須由 E1–E5 的結果判定。
