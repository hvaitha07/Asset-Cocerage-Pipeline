📊 Power BI Gap Analysis Dashboard

The Gap Analysis Dashboard provides a unified view of asset coverage across multiple enterprise security and IT management tools. By integrating data pipelines from Cortex, Baramundi, SecureWorks, Lansweeper, ADUC, and Tenable into a Delta Lake architecture, the dashboard enables stakeholders to quickly identify coverage gaps and ensure complete visibility of organizational assets.

🔹 Key Features

Region & Site Filters – Slice and dice data by region (e.g., Asia, Europe, North America) and by business sites (Acuna, Burlington, Celaya, Cincinnati).

Coverage Category – Assets are categorized as:

Fully Covered – Reported in all systems

Partially Covered – Reported in some systems

Not Covered – Missing from all major tools

Asset Type Segmentation – Breakdowns by Workstation, Laptop, Server, and Domain Controller for better IT governance.

Real-time Asset Count – Total assets tracked across tools, with quick indicators showing whether a given source (Cortex, ADUC, Baramundi, Lansweeper, SecureWorks) is reporting assets.

Interactive Tables – Asset-level details showing Computer Name, Coverage Category, and which tools are reporting each system.

Coverage Breakdown Charts – Pie charts for each tool (Cortex, Baramundi, Lansweeper, ADUC, SecureWorks) clearly showing the proportion of Fully/Partially/Not Covered systems.

🔹 Business Impact

Single Pane of Glass – Provides IT security teams with a consolidated view across disparate systems.

Improved Governance – Identifies blind spots in monitoring, ensuring no asset is left unprotected.

Actionable Insights – Helps prioritize remediation by showing which tools are missing asset reporting.

Scalable Architecture – Powered by Databricks + Delta Lake ingestion pipelines, enabling enterprise-wide coverage at scale.
