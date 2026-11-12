# SmartIntake｜5 Cores、3 Trade-offs、10 Details

以下項目為已確認的 DIC3 設計。驗證方式與指標均為規劃，詳見[實驗計畫](../experiments/experiment-plan.md)。

## 5 Cores

| 編號 | 核心 | 規格與驗證 |
| --- | --- | --- |
| C1 | Weight Sensing | Raspberry Pi 4 分別讀取兩組 Load Cell 與 HX711。以 E1 驗證杯墊及食物秤的誤差、穩定時間與漂移。 |
| C2 | Hydration Event Detection and Logging | 前後穩定重量與離杯狀態形成飲水候選。一般事件自動記錄並可更正，不確定事件由 Discord 確認。以 E2 評估錯漏與確認負擔。 |
| C3 | Multi-User Session Management | 三人共用兩台秤與同一 Active User Session，事件綁定開始時的 Session ID、User ID 與 Session Version。以 E3 檢查切換及事件歸屬。 |
| C4 | Dietary Logging and Nutrition Estimation | Mode A 以實測淨重與每 100 g 資料計算。Mode B 以可靠淨重及適用資料估算，或採有來源的每份參考值。以 E4 評估操作流程。 |
| C5 | Grounded Domain AI Agent | n8n 與 LLM 呼叫六項唯讀工具，FastAPI 授權、查詢並計算，回答保留來源。以 E5 檢驗可靠性。 |

## 3 Trade-offs

| 編號 | 取捨 | 採用方式與評估 |
| --- | --- | --- |
| T1 | Low-Cost Weight Sensing vs. Limited Behavioral Observability | 以低成本雙秤取得穩定重量。相同減重可對應多種行為，故以 E1 衡量感測品質，以 E2 衡量事件可辨識程度。 |
| T2 | Automation vs. Logging Reliability and Human Confirmation Cost | 允許一般候選自動記錄並事後修正，其餘要求確認。以 E2 評估自動與修正後的品質及確認負擔，以 E4 檢查整體操作成本。 |
| T3 | LLM Flexibility vs. Deterministic Calculation and Data Traceability | 語言模型處理意圖與表達，後端保有授權、計算及來源規則。以 E5 評估工具覆蓋、數值、來源及缺資料時的行為。 |

## 10 Details

| 編號 | 細節 | 設計與查核 |
| --- | --- | --- |
| D01 | 雙 Load Cell 與 HX711 | 杯墊與食物秤分別讀取、校正及記錄裝置來源，E1 分秤驗證。 |
| D02 | 校正、濾波及穩定重量偵測 | 各秤保存校正版本，透過濾波及穩定窗取得可用讀值，E1 檢查反應與漂移。 |
| D03 | 重量 FSM 與飲水候選 | 以前後穩定重量及離杯時間判定，E2 涵蓋飲用、倒水、灑出及其他交互。 |
| D04 | Discord 確認及修正 | 異常候選由原使用者確認，已入帳事件支援撤銷及修正，E2 分階段計分。 |
| D05 | 食物選擇、Tare、秤重及換算 | 使用者選擇食物條目，完成去皮與淨重取得後由 FastAPI 計算，在 Discord 檢視並確認。E4 比較操作效率。 |
| D06 | 臺灣食品資料庫與 USDA | 優先選擇適用的臺灣食品資料，必要時以 USDA FoodData Central 補充，保留條目、資料版本與估算依據。 |
| D07 | 三人共用 Active User Session | Discord 切換同一個工作階段。兩台秤的新事件讀取切換後的工作階段，既有事件沿用其快照。E3 測試交錯操作。 |
| D08 | FastAPI、PostgreSQL 與 SQLite | FastAPI 驗證、查詢與計算，Supabase PostgreSQL 保存正式紀錄，Pi 的 SQLite 緩衝待同步事件。以 E3 檢查延遲送達時的歸屬。 |
| D09 | n8n Tool Calling 與後端授權 | 六項唯讀工具透過 FastAPI 依請求者權限查詢。E5 檢查身分隔離、數值與引用來源。 |
| D10 | E1–E5 實驗驗證 | 依序檢驗重量準確度、飲水事件可靠性、多人歸屬、紀錄效率及 Agent 可靠性。 |

資料流、事件狀態、兩種飲食模式與工具契約詳見 [Technical Design v0.5](../../design.md)。
