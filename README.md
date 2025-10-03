Asset Coverage Gap Analytics (Databricks + Power BI)

Unifies asset inventories from ADUC, Cortex XDR, Lansweeper, Secureworks Taegis, and Tenable into Delta tables on Databricks, computes a coverage gap dataset, and visualizes it in Power BI.

Outcome: a single table that tells you whether each endpoint is seen by each tool (Yes/No), how many tools cover it (0–5), and a Coverage Category (Fully / Partially / Not Covered).

🗂️ Repository layout
asset-coverage-gap-analytics/
├─ collectors/
│  ├─ aduc/fetch_assets.py
│  ├─ cortex/cortex_endpoints_to_delta.py
│  ├─ lansweeper/lsdatapuller.py
│  ├─ secureworks/secureworks_assets_to_delta.py
│  └─ tenable/tenable_hosts_to_delta.py
├─ notebooks/
│  └─ gap_analysis_databricks.py
├─ powerbi/
│  └─ GapCoverageDashboard.pbix
├─ docs/
│  ├─ architecture.md
│  ├─ data_dictionary.md
│  └─ security.md
├─ infra/
│  ├─ requirements.txt
│  └─ .env.example
└─ README.md

🔍 Architecture (ELT)
Collectors call vendor APIs (or LDAP for ADUC).
Normalize JSON → pandas DataFrame → Spark DataFrame → Delta in the raw layer.
Gap notebook reads raw tables, normalizes hostnames, parses timestamps, joins sources, computes flags/metrics, and writes raw.gap_data.
Power BI connects to raw.gap_data via the Databricks SQL connector.

[ADUC] ─┐
[Cortex] ├─ collectors → Delta (raw) ─> gap notebook ─> Delta (raw.gap_data) ─> Power BI
[Lansweeper] │
[Secureworks]│
[Tenable] ───┘

🧱 Delta tables (targets written by collectors)
Source	Delta Table
ADUC	security_nprod.db.raw.aduc_assets
Cortex XDR	security_nprod.db.raw.cortex_assets
Lansweeper	security_nprod.db.raw.lansweeper_assets
Secureworks	security_nprod.db.raw.secureworks_assets
Tenable	security_nprod.db.raw.tenable_assets
Gap Output	security_nprod.db.raw.gap_data

Change names if needed, but keep them consistent across scripts and the notebook.

🚀 Quick start (Databricks)
1) Cluster & libraries

Databricks Runtime: 13.x–14.x (or similar)
Python: 3.10+
Install libs (attach to cluster or %pip install -r):

infra/requirements.txt
requests
pandas
ldap3
urllib3
(PySpark is included with Databricks.)

2) Configure secrets (Databricks secret scope)

Create a scope (example security-ingest) and set:

Cortex
CORTEX_API_KEY
CORTEX_FQDN (e.g., api-<tenant>.xdr.eu.paloaltonetworks.com)

Lansweeper
LS_TOKEN
LS_SITE_ID

Secureworks
SW_CLIENT_ID
SW_CLIENT_SECRET=
SW_TENANT_ID

Tenable
TEN_USER
TEN_PASS
TEN_BASE_URL (e.g., https://tenable.yourdomain.com)

ADUC (LDAP)
AD_SERVER
AD_USER (e.g., DOMAIN\\service.user)
AD_PASSWORD
Access in notebooks:
dbutils.secrets.get(scope="security-ingest", key="CORTEX_API_KEY")

3) Run collectors
Each collector:
Pulls data → pandas.json_normalize(...)
Converts to Spark
Writes to Delta

End of every collector script:
spark_df = spark.createDataFrame(df)
spark_df.write.mode("overwrite").saveAsTable("security_nprod.db.raw.<source>_assets")
print("✅ Data saved to: security_nprod.db.raw.<source>_assets")


Run all collectors first so the gap notebook sees fresh inputs.

4) Build the gap table
Open notebooks/gap_analysis_databricks.py and Run All.
It writes:
security_nprod.db.raw.gap_data
You’ll also see a coverage summary and unique host counts per tool.

5) Power BI
Open powerbi/GapCoverageDashboard.pbix
Connect via Databricks SQL to security_nprod.db.raw.gap_data
Slicers: Region, Site, Coverage Category, Asset Type
Cards: Asset Count, tool presence
Pies: coverage breakdown per tool

🧠 Gap logic (high-level)
Hostname normalization: upper(substring_index(trim(col), ".", 1))
Date parsing: per-source formats (epoch ms, string timestamps)
Base table: Cortex superset (hostname_norm, last seen, status, etc.)
Joins: left-join Baramundi/ADUC/Lansweeper/Secureworks/Tenable by hostname_norm

Flags:
Covered in <Tool> = Yes if last-seen/logon present else No
Tools Covered = sum of flags (0–5)
Coverage Category:
5 → Fully Covered
0 → Not Covered
else → Partially Covered

🧾 Standard imports (collectors)
import requests, json, time, os
import pandas as pd
from pandas import json_normalize
# Databricks provides the SparkSession as 'spark'
# ADUC only:
# from ldap3 import Server, Connection, NTLM, ALL, SUBTREE

🔐 Security & compliance
Do not hardcode credentials—use Databricks secret scopes.
Minimize PII; if needed for EU sites, hash identifiers or host EU data in an EU workspace (see docs/security.md).

🧪 Testing (optional)
Unit tests for JSON → dataframe normalization (mocked API payloads)
Integration tests against a dev Delta catalog

🛠️ Troubleshooting
Auth 401/403 → wrong/expired token or tenant headers
Empty joins → hostname normalization mismatch; verify domains & casing
Timestamp off → ms vs s epoch conversion
Power BI empty → refresh permissions or SQL endpoint catalog/schema

📅 Roadmap
Promote raw.gap_data → curated.gap_coverage (view or DLT)
Add CI (lint/format/tests) under .github/workflows/ci.yml
Orchestrate collectors + gap notebook via Databricks Jobs


🙌 Credits
Built by Harsha Vardhan Aitha using Python, PySpark (Databricks), Delta Lake, and Power BI.
Screenshots of the dashboard/notebook can go in docs/screenshots/ and be embedded
