# AI-Powered Revenue Optimization System — CRISP-DM Project Plan

## 0. Executive Summary
Build a production-ready pipeline that ingests hotel & competitor data, forecasts occupancy/ADR, recommends prices, and reports KPIs (RevPAR, ADR, Occupancy). Deliver both an executive **Tableau Dashboard** and an analyst-facing **Streamlit AI Assistant**.

**Core Deliverables**
- Tableau KPI Dashboard (RevPAR, ADR, Occupancy, Forecast vs Actual, Segments/Room Types/Channels)
- Streamlit App: demand forecasting, price recommendations, scenario simulations, LLM-powered Q&A
- GitHub repo: ETL pipeline, notebooks, app, dashboards, artifacts, bilingual README
- SQL star schema, ML pipeline, monitoring alerts

**Success Metrics**
- Forecast MAPE ≤ 12%
- Pricing recommendations improve RevPAR by +3–5% (simulated uplift)
- Dashboard queries < 2s; daily batch ETL T+1
- Complete documentation: architecture diagram, data dictionary, runbook

---

## 1. Business Understanding
**Goal**: Revenue managers need accurate demand forecasts and actionable pricing guidance to maximize RevPAR while maintaining occupancy and guest experience.

**Key Questions**
- What ADR should we set for each room type/date/channel to hit targets?
- How do competitor prices affect us?
- How to detect data discrepancies quickly and ensure fast reporting?

**KPIs**
- RevPAR, ADR, Occupancy balance
- Pickup & pace monitoring
- Forecast accuracy (MAPE, sMAPE, WAPE)

---

## 2. Data Understanding
**Sources**
- Internal: PMS/CRS bookings, cancellations, inventory, promotions
- External: Competitor rates (legal/public APIs), weather, holidays
- Synthetic: anonymized demo datasets

**Exploration**
- Missing values (no-show, cancellations, bulk bookings)
- Seasonality and event effects
- Daily granularity, optional hourly for short-term view

**Core Fields**
- `date_key`, `room_type_id`, `channel_id`, `segment_id`
- `inventory`, `booked`, `occupancy`, `adr`, `revpar`
- `comp_adr_mean`, `event_score`, `weather_idx`, `promo_flag`

---

## 3. Data Preparation
**Star Schema**
- Fact: `fact_daily_rooms` (hotel × room type × date)
- Dimensions: `dim_date`, `dim_room_type`, `dim_channel`, `dim_segment`, `dim_event`

**Features**
- Rolling windows: 7-day ADR/occupancy, pickup pace
- Calendar: holiday flags, seasonality
- Competitive features: ADR delta vs competitors, rank
- Price elasticity proxies

**Data Quality**
- Checks: completeness, ranges, consistency
- Alerts for anomalies (e.g., day-over-day > threshold)

---

## 4. Modeling
**Objectives**
- Forecast occupancy/demand
- Recommend ADR to maximize RevPAR subject to business rules

**Methods**
- Forecasting: Prophet, SARIMAX
- ML: XGBoost/LightGBM with engineered features
- Ensemble: sliding window cross-validation

**Metrics**
- Regression: RMSE, MAE, MAPE, sMAPE
- Business: simulated RevPAR uplift

**Price Recommendation**
- Demand curve Q(p)
- Business constraints (min/max ADR, blackout dates)
- Optimize RevPAR = ADR × Occupancy

---

## 5. Evaluation
**Backtesting**
- Time-based rolling windows
- Stratified by room type, channel

**Acceptance Criteria**
- MAPE ≤ 12% (≤10% for top segments)
- Simulated RevPAR uplift ≥ +3%
- Dashboard SLA: main KPI < 2s, drill-down < 5s

---

## 6. Deployment
**Architecture**
- Database: PostgreSQL or Snowflake
- Orchestration: cron/Airflow
- Serving: Streamlit Cloud, Tableau Public/Server
- Model Registry: MLflow

**Products**
- Tableau Dashboard: KPIs, forecast vs actual, competitor heatmap
- Streamlit App: Forecasting, Price Advisor, Scenario Lab, LLM Q&A

**Monitoring**
- Data freshness, pipeline success
- Model drift alerts
- Runbook with escalation paths

---

## 7. Project Management
**Timeline (2-week sprint)**
- Week 1: ETL + baseline model + Tableau v1
- Week 2: ML models + Streamlit v1 + monitoring + docs

**Stakeholders**
- Owner: Data Solutions Engineer (you)
- Reviewer: Revenue Manager
- Stakeholders: Ops/IT/Finance

**Risks**
- Data gaps → synthetic samples, interpolation
- Competitor data compliance → only legal/public APIs
- Model drift → retrain monthly, set alerts

---

## 8. Data Model & SQL (Sample)
**fact_daily_rooms**
```
(date_key, room_type_id, channel_id, segment_id, 
inventory, booked, occupancy, adr, revpar,
comp_adr_mean, event_score, weather_idx, promo_flag)
```

**dim tables**: room_type, channel, segment, event

---

## 9. Visualization Design
**Tableau**
- KPI home: RevPAR/ADR/Occupancy, Forecast vs Actual
- Drill-down: segment, room type, channel
- Competitive heatmap

**Streamlit**
- Sidebar filters: date, room type, channel
- Tabs: Forecast | Price Advisor | Scenario Lab | Explainability

---

## 10. Documentation
- README (bilingual)
- Data Dictionary
- Runbook
- Compliance statement

---

## 11. JD Mapping
- End-to-end pipeline ✅
- SQL + Data Modeling ✅
- Reporting tools (Tableau) ✅
- Issue resolution & monitoring ✅
- Hospitality domain ✅

---

## 12. Next Steps
1. Generate sample data & tables
2. Build ETL + features
3. Train baseline models
4. Build Tableau v1
5. Build Streamlit v1
6. Finalize docs & demo
