# Asset-Cocerage-Pipeline
Unified Data Pipeline for Asset Coverage across ADUC, Lansweeper, SecureWorks, Cortex, and Tenable with Delta Lake + Power BI.

# Asset Coverage Pipeline

This project integrates multiple enterprise security and IT tools (ADUC, Lansweeper, SecureWorks, Cortex, Tenable) into a **single data lake architecture** using **PySpark + Delta Lake** and visualizes coverage in **Power BI**.

## Features
- Collects data from 5 sources:
  - ADUC
  - Lansweeper
  - SecureWorks
  - Cortex
  - Tenable
- Cleans and normalizes JSON payloads.
- Writes directly to **Databricks SQL Delta Lake tables**.
- Unified **Power BI Dashboard** for asset coverage visibility.

## 📂 Structure
- `src/` → Python collectors for each source.
- `notebooks/` → Databricks notebooks for ingestion and orchestration.
- `powerbi/` → Power BI `.pbix` dashboard + screenshots.
- `requirements.txt` → Python dependencies.

