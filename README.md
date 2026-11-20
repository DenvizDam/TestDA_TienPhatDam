HCVN – Data Analyst Test
After extracting the ZIP file, open the HCVN/ folder. It contains two PDFs:
- HCVN_Bai_lam.pdf — Open this first to read the submission for all three sections: SQL, Power BI, and Technical.
- 00_Huong_dan.pdf — Follow this guide to run each section. It includes dashboard screenshots and sample results for reference.
All paths below are relative to the HCVN/ folder.




1. Files to open for each section
Section	File to open	Instructions
Original test	de_bai/DA - Test - HCVN.xlsx	Open the workbook in Excel to review the three sheets: SQL, PBI, and Technical.
SQL	sql/HCVN.sqlite	Open the database in DBeaver using a SQLite connection. Run the three SQL scripts in order, starting with 01_, 02_, and 03_. Sample results are in results/.

Power BI — Customer Retention	powerbi/HCVN.pbip	Open the project in Power BI Desktop on Windows, then select Home → Refresh. With all filters cleared, 2 out of 10 calls resulted in successful retention (20%).

Technical — Quick preview	technical/live/Technical_dashboard_demo.gif	Open the animated GIF to see how the dashboard changes across 10 simulated events. You can view it on Windows or Mac without running Docker.

Technical — Run the live dashboard	technical/live/Start_dashboard.cmd	Run this script on Windows to start Kafka and Oracle and open Entertainment.pbip. Follow the setup steps below.


Power BI project files. When copying or extracting a project, keep each .pbip file alongside its corresponding .Report and .SemanticModel folders. Power BI needs all three components to open the report.



2. Run the Technical dashboard on Windows
Step 1. Start the system
You will need Docker Desktop and Power BI Desktop. Open Docker Desktop in Linux containers mode and wait for it to finish starting.

In technical/live/, double-click Start_dashboard.cmd and leave the command window open. The first run may take a few minutes to download the Kafka and Oracle images. Once the data is ready, the script will open Entertainment.pbip. Or in another way , you can read the readme file in /technical/
Step 2. Enable Oracle DirectQuery in Power BI
If you are using Power BI Desktop’s built-in Oracle connector, complete this one-time setup:
1. Go to File → Options and settings → Options → Preview features.
2. Select Enable Oracle Bundled Provider for DirectQuery Mode.
3. Click OK, close Power BI Desktop completely, and reopen technical/live/Entertainment.pbip. Keep Docker and the command window from Step 1 running.
When Power BI asks how to sign in, select Database in the left pane and enter the demo credentials below.
Field	Value
Server	localhost:1521/FREEPDB1
User name	HCVN
Password	HcvnDemo2026


Click Save or Connect to open the report.
Step 3. Enable automatic page refresh and replay the sample events
1. On the Live entertainment operations page, click the blank area between the two tables.
2. In the Visualizations pane, select Format page → Page refresh → On → Fixed interval and set the interval to 5 seconds.
3. Save the report and leave the page open. Return to the Start_dashboard.cmd window and press any key to replay the 10 sample events, one every 8 seconds.
Watch the Latest activity table to follow each action. Active bookings decreases when a customer cancels a ticket. Net receipts decreases once the refund is confirmed.
3. Verify that the dashboard refreshes automatically
After 10 events, the dashboard should show 1 active booking, VND 180,000 in net receipts, 20 minutes watched, and 1 check-in. The event producer stops at 10 and keeps the data for the next session.

To verify that the dashboard updates automatically, leave Power BI open and open PowerShell in technical/live/. When Events received shows 10, run:
docker compose --profile demo run --rm --no-deps producer publish --interval 1 --limit 11

This command brings the total to 11 events by publishing one additional 12-minute viewing event for City Lights. Watch the following values update without clicking Refresh.
Metric	Before replay	After event 11
Events received	10	11
Minutes watched	20	32
Net receipts	VND 180,000	VND 180,000


When you finish, run the following command from the same folder to stop the services and retain the data.
docker compose --profile demo down
