# AI 飯店營收最佳化系統 — CRISP-DM 專案計劃書

## 0. 執行摘要
本專案建立一個可落地的資料管線，能擷取飯店與競爭對手資料，預測入住率/ADR，提供價格建議，並輸出 KPI 報表（RevPAR、ADR、入住率）。同時提供 **主管專用的 Tableau Dashboard** 與 **分析師專用的 Streamlit AI 助理**。

**核心輸出**
- Tableau KPI Dashboard（RevPAR、ADR、入住率、預測 vs 實際、客群/房型/通路）
- Streamlit App：需求預測、價格建議、情境模擬、LLM 問答
- GitHub Repo：ETL pipeline、notebooks、app、dashboard、artifacts、README（中英文）
- SQL 星型模型、ML pipeline、監控告警

**成功指標**
- 預測 MAPE ≤ 12%
- 價格建議使 RevPAR +3–5%（模擬提升）
- Dashboard 查詢 < 2 秒；每日批次 ETL T+1
- 文件完整：架構圖、數據字典、Runbook

---

## 1. 商業理解
**目標**：營收管理團隊需要準確的需求預測與價格建議，以最大化 RevPAR，同時維持入住率與顧客體驗。

**關鍵問題**
- 每個房型/日期/通路應設定多少 ADR 才能達標？
- 競爭對手價格如何影響？
- 如何快速偵測資料異常並確保即時報表？

**KPI**
- RevPAR、ADR、入住率平衡
- 訂單增長速度（Pickup & Pace）
- 預測準確度（MAPE、sMAPE、WAPE）

---

## 2. 資料理解
**來源**
- 內部：PMS/CRS 訂單、取消、庫存、促銷
- 外部：競品價格（合法公開 API）、天氣、假日
- 模擬：匿名化公開 Demo 資料集

**探索**
- 缺失值（No-show、取消、團體訂單）
- 季節性與事件效應
- 日粒度，必要時加小時粒度

**核心欄位**
- `date_key`, `room_type_id`, `channel_id`, `segment_id`
- `inventory`, `booked`, `occupancy`, `adr`, `revpar`
- `comp_adr_mean`, `event_score`, `weather_idx`, `promo_flag`

---

## 3. 資料準備
**星型模型**
- Fact：`fact_daily_rooms`（飯店 × 房型 × 日期）
- Dimension：`dim_date`, `dim_room_type`, `dim_channel`, `dim_segment`, `dim_event`

**特徵工程**
- 滾動窗口：7 日 ADR、入住率、訂單增長
- 行事曆：假日旗標、季節性
- 競品特徵：ADR 差異、市場排名
- 價格彈性代理：歷史價格變動對需求影響

**資料品質**
- 檢查：完整度、範圍、一致性
- 告警：異常日變化 > 閾值

---

## 4. 建模
**目標**
- 預測需求/入住率
- 提供最佳 ADR 建議以最大化 RevPAR（受商務規則限制）

**方法**
- 預測：Prophet、SARIMAX
- 機器學習：XGBoost/LightGBM + 特徵工程
- Ensemble：時間序列交叉驗證（滑動窗口）

**評估指標**
- 回歸：RMSE、MAE、MAPE、sMAPE
- 商務：模擬 RevPAR 提升

**價格建議邏輯**
- 預測需求曲線 Q(p)
- 套用商務限制（最低/最高 ADR、黑名單日期）
- 優化目標：RevPAR = ADR × Occupancy

---

## 5. 評估
**回測**
- 時序切窗（滾動方式）
- 按房型、通路做分層評估

**驗收標準**
- 平均 MAPE ≤ 12%（主力房型 ≤ 10%）
- 模擬 RevPAR 提升 ≥ +3%
- Dashboard SLA：首頁 < 2 秒，鑽取 < 5 秒

---

## 6. 部署
**架構**
- 資料庫：PostgreSQL 或 Snowflake
- Orchestration：cron / Airflow
- 前端服務：Streamlit Cloud、Tableau Public/Server
- 模型管理：MLflow

**產品**
- Tableau Dashboard：KPI、預測 vs 實際、競品熱力圖
- Streamlit App：需求預測、價格建議、情境模擬、LLM 問答

**監控**
- Data freshness、Pipeline 成功率
- 模型漂移告警
- Runbook 升級與應對

---

## 7. 專案管理
**時程（兩週衝刺）**
- W1：ETL + 基準模型 + Tableau v1
- W2：ML 模型 + Streamlit v1 + 監控 + 文件

**角色**
- Owner：Data Solutions Engineer（你）
- Reviewer：Revenue Manager
- Stakeholders：Ops/IT/Finance

**風險**
- 資料缺漏 → 模擬樣本/插補
- 競品資料合法性 → 僅用公開 API
- 模型漂移 → 每月重訓 + 告警

---

## 8. 資料模型 & SQL
**fact_daily_rooms**
```
(date_key, room_type_id, channel_id, segment_id, 
inventory, booked, occupancy, adr, revpar,
comp_adr_mean, event_score, weather_idx, promo_flag)
```

**維度表**：room_type, channel, segment, event

---

## 9. 視覺化設計
**Tableau**
- KPI 首頁：RevPAR/ADR/入住率、預測 vs 實際
- 鑽取：房型、通路、客群
- 競品熱力圖

**Streamlit**
- 側邊選單：日期、房型、通路
- Tabs：Forecast | Price Advisor | Scenario Lab | Explainability

---

## 10. 文件
- README（中英文）
- Data Dictionary
- Runbook
- 合規聲明

---

## 11. JD 對應
- End-to-end pipeline ✅
- SQL + Data Modeling ✅
- Reporting tools (Tableau) ✅
- Issue resolution & monitoring ✅
- Hospitality domain ✅

---

## 12. 下一步
1. 生成樣本資料與表格
2. 建 ETL 與特徵工程
3. 訓練基準模型
4. 建立 Tableau v1
5. 建立 Streamlit v1
6. 完成文件與 Demo
