# Agent Playbook for Threat Intel Aggregator

This playbook guides the agent through building the **Threat Intel Aggregator & Alerting** workflow in n8n and preparing it for deployment. Follow each step in order and use the provided environment variables where necessary.

## Prerequisites

- Access to an n8n instance with the ability to create and edit workflows.
- API keys for AbuseIPDB, VirusTotal, and AlienVault OTX.
- Slack credentials (Webhook URL or Bot token) for sending alerts.
- Google Sheets credentials (or PostgreSQL connection) for logging data.

## Steps

1. **Create a new workflow** and name it `Threat Intel Aggregator`.
2. **Add a Cron node** set to run every hour. This will trigger the workflow on a schedule.
3. **Add three HTTP Request nodes**:
   - **AbuseIPDB Feed** – configure a GET request to the AbuseIPDB API endpoint with your `ABUSEIPDB_API_KEY`.
   - **VirusTotal Feed** – configure a GET request to the VirusTotal API endpoint with your `VIRUSTOTAL_API_KEY`.
   - **AlienVault OTX Feed** – configure a GET request to the OTX Pulse API with your `OTX_API_KEY`.
4. **Merge the responses**:
   - Use a **Merge node** in *append* mode to combine the results from the three HTTP nodes.
   - Add a **Function or Set node** to deduplicate items based on IP/domain and unify the data structure.
5. **Score and filter threats**:
   - Add a **Function node** to calculate a risk score (e.g., based on reputation, number of reports, or risk category).
   - Use an **IF node** to branch high‑risk threats (e.g., score ≥ 70) versus all other results.
6. **Send Slack alerts**:
   - For the high‑risk branch, add a **Slack node** configured with your Slack credentials. Compose a message that includes the indicator (IP or domain), the source, and the risk score.
7. **Log all results**:
   - In the main branch (all results), add a **Google Sheets node** or **PostgreSQL node** to store the aggregated data.
8. **Test the workflow** by running it manually. Ensure Slack alerts are delivered and that data appears in Google Sheets or PostgreSQL.
9. **Export the workflow** via the *Download* button in n8n. Save the resulting JSON file as `workflow.json` in this project directory.
10. **Commit and push** all project files (including `workflow.json`) to the GitHub repository.

## Notes

- Avoid hard‑coding secrets; use credentials in n8n or environment variables.
- Adjust the risk scoring logic to fit your threat model.
- Use the provided Mermaid diagram (`diagram.md`) for a visual overview.
