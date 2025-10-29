# 🩺 Apple Watch Health Analytics Dashboard

> **A Power BI–based end-to-end analytics solution that transforms Apple Health data into actionable wellness insights.**

[![Power BI](https://img.shields.io/badge/Built%20With-Power%20BI-yellow?logo=powerbi)]()
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![GitHub stars](https://img.shields.io/github/stars/yajurved987/Apple-Watch-Health-Analytics-Dashboard-?style=social)](https://github.com/yajurved987/Apple-Watch-Health-Analytics-Dashboard-/stargazers)

---

## 🧭 Overview

This project automates health data transformation and visualization using **Power BI**.  
It converts Apple Health exports (CSV/XML) into an **interactive dashboard** for tracking activity, recovery, and sleep — featuring **zero-touch data refresh**.

### ✨ Key Features
- ⚙️ Automated ETL pipeline using Power Query  
- 📊 Star-schema data model for analytical efficiency  
- 📈 Dynamic DAX measures for real-time insights  
- 🔁 One-click data refresh – just drop new exports  

---

## 🧱 Architecture
Apple Health App → CSV Export → Power BI Pipeline
├─ Power Query (Transform & Clean)
├─ Star Schema (Facts + Dimensions)
├─ DAX Measures (Analytics Logic)
└─ Interactive Dashboards (Activity • Heart • Sleep)


---

## 📊 Dashboard Pages

| Page | Focus | Example Visuals |
|------|--------|----------------|
| 🏠 **Overview** | Daily wellness KPIs | Activity summary, energy trends |
| 🏃 **Activity** | Movement & exercise | Steps, energy, exercise minutes |
| 💓 **Heart & Recovery** | Cardiovascular health | HRV, resting HR, SpO₂ |
| 🌙 **Sleep** | Sleep architecture | Stages, efficiency, trends |

---

## ⚙️ Tech Stack

| Layer | Tool | Purpose |
|-------|------|----------|
| **Data Export** | Apple Health CSV | Source data |
| **ETL** | Power Query (M) | Cleaning & transformation |
| **Modeling** | Power BI Data Model | Star schema design |
| **Analytics** | DAX | Measures & time intelligence |
| **Visualization** | Power BI Desktop | Interactive dashboards |
| **Version Control** | Git / GitHub | Collaboration & documentation |

---

## 🚀 Quick Start

1. **Export data** from Apple Health using the [Health Export CSV app](https://apps.apple.com/).  
2. **Place files** in your designated source folder (e.g., `/HealthData/`).  
3. **Open** `AppleWatch_Health_Dashboard.pbix` in Power BI Desktop.  
4. Click **Refresh** — your dashboard updates automatically.  

---

## 📈 Sample Insights

- 🏃 **Average Daily Steps:** ~8,500  
- 💓 **Average HRV:** Improving weekly trend  
- 🌙 **Average Sleep Duration:** 7.2 hours  
- ⚡ **Sleep Efficiency:** ~88%  

*(Metrics shown depend on dataset used)*

---

## 🧩 Performance

| Metric | Result |
|--------|---------|
| Dataset size | ~50,000 rows |
| Refresh time | <30 sec |
| Report size | ~8 MB |
| Render time | <2 sec |

---

## 🌟 Roadmap

- [ ] Predictive sleep quality (HRV + activity correlation)  
- [ ] Anomaly detection for heart rate trends  
- [ ] Nutrition & VO₂ Max integration  
- [ ] Power BI Copilot insights and automation  

---

## 🎓 Learning Outcomes

✅ Automated ETL pipelines (Power Query)  
✅ Star-schema modeling & DAX optimization  
✅ Advanced Power BI dashboarding  
✅ Health & wellness data interpretation  

---

## 🤝 Contributing

Contributions are welcome!  
You can improve by adding:
- New health metrics or visualizations  
- Additional device integrations  
- Documentation enhancements  

---

## 📬 Contact

**Yajurved Jayavarapu**  
_Data Scientist • BI Developer_

📧 yjayavarapu@gmail.com  
💼 [LinkedIn](https://www.linkedin.com/in/yajurved-jayavarapu/)   
📂 [GitHub](https://github.com/yajurved987)

---

## 📄 License

Released under the [MIT License](LICENSE).

---

> 💡 *“Data without action is just noise — this dashboard turns data into decisions for better health.”*

---

⭐ **If you found this project helpful, please consider starring the repository!**
