# SmartIntake

SmartIntake 是面向實驗室與辦公室固定工作場域的半自動飲水與飲食紀錄系統（IoT- and AI Agent-Based Semi-Automated Hydration and Dietary Logging System）。本研究擬以秤重減少使用者手動估量與輸入份量的負擔，並保留紀錄的歸屬、計算來源與修正歷程。研究貢獻聚焦於 IoT 與 Domain AI Agent 的整合及實驗驗證。

## 系統架構

- 一台 Raspberry Pi 4 連接兩組 Load Cell 與 HX711，分別作為飲水杯墊及食物秤。Python Sensor Worker 處理校正、濾波與穩定判定，有限狀態機（FSM）評估杯子拿起和放回前後的重量。
- FastAPI 驗證身分與權限、查詢資料及執行確定性計算。Supabase PostgreSQL 儲存正式紀錄，Raspberry Pi 本機 SQLite 緩衝待同步事件。
- Discord Bot 提供使用者切換、事件確認、撤銷、修正與手動補登。n8n AI Agent 透過 LLM Tool Calling 使用六項唯讀工具，解釋 FastAPI 回傳的結果與來源。

## MVP 功能與範圍

| 功能 | 已確認的設計 |
| --- | --- |
| 飲水紀錄 | 重量下降產生候選事件。符合規則的一般事件自動記錄並可撤銷或修正，異常或不確定事件交由 Discord 人工確認。場域外飲水以手動補登處理。 |
| 飲食紀錄 | Mode A（Single Food Weighing）以去皮後的單項食物淨重及每 100 g 成分資料估算。Mode B（Whole-Meal Estimation）優先使用可靠淨重與適用的餐點資料，餐盒重量未知時使用有來源的每份參考值。 |
| 多人共用 | 三位測試使用者透過 Discord 切換兩台秤共用的 Active User Session。已開始的事件保留開始時的 Session ID、User ID 與 Session Version。 |
| Domain AI Agent | 支援單日、跨日、目標與來源查詢。LLM 理解問題、選擇工具與整理回覆，FastAPI 負責授權、查詢及計算。 |

MVP 排除 IMU、相機、自行訓練的飲水辨識模型、自動食物影像辨識與吸管飲水模式。固定杯墊僅能觀察重量，喝水、倒水與灑出可能留下相同的減重訊號。混合餐點的營養值取決於參考條目與實際餐點的匹配程度。

## 文件導航

| 文件 | 內容 |
| --- | --- |
| [A–F 研究提案](docs/proposal/A-F-proposal.md) | 研究問題、系統方法、實驗與預期成果 |
| [5 Cores、3 Trade-offs、10 Details](docs/proposal/5-cores-3-tradeoffs-10-details.md) | 核心、取捨與實驗對應 |
| [Technical Design v0.5](design.md) | 系統資料流、功能邊界與規格 |
| [Introduction](docs/proposal/introduction.md) | 研究動機、方法與預期貢獻 |
| [Related Work](docs/proposal/related-work.md) | 文獻、比較、研究缺口與參考來源 |
| [E1–E5 實驗計畫](docs/experiments/experiment-plan.md) | 測試方法、參考真值、指標與限制 |

## DIC3 狀態

| 項目 | 狀態 |
| --- | --- |
| DIC3 文件 | 已整理成冊。架構與功能為已確認的設計規格。 |
| 系統原型與 E1–E5 | 規劃中。待建置原型、校正設備、定案測試參數並執行驗證。 |
| OpenSpec | 安裝與 Workflow 轉換暫緩。 |
| 未來延伸 | 行動場景、多站點與額外感測方式留待後續研究。 |
