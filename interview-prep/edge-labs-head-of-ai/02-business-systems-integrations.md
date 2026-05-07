# Deep Dive: Business Systems & Integrations

## 🏗️ Connecting the Stack
The Head of AI must be an expert at "System Glue".
*   **Salesforce (CRM):** The "Source of Truth". Use the **Salesforce REST API** or **Bulk API 2.0** for syncing data. Focus on `Opportunity` and `Lead` objects.
*   **Slack:** The "Control Center". Use **Slack Block Kit** to create interactive UI elements (buttons, modals) for AI-human collaboration.
*   **Email:** The "External Interface". Use **SendGrid** or **Microsoft Graph API** for programmatically reading/writing emails.

## 🔗 Integration Architecture
*   **Event-Driven:** Using Webhooks to trigger AI actions (e.g., "New Lead Created" in Salesforce -> Trigger AI research).
*   **ETL/ELT:** Extracting data from these systems into a centralized **Vector Database** (Pinecone) to provide context for RAG.

## 🛠️ Specialized Automation: Client Health Tracking
*   **Input:** Activity logs from Slack, support tickets, and CRM meeting notes.
*   **Process:** AI analyzes sentiment and frequency of interaction.
*   **Output:** A "Health Score" (0-100) updated daily. If the score drops below 40, trigger a Slack alert to the Account Manager.

## 💡 Interview Q&A
**Q: How do you handle Salesforce API rate limits when building a real-time AI monitoring system?**
**A:** Use a **Polling/Webhook Hybrid**. Use Webhooks for "important events" and a scheduled **Bulk API** job for high-volume background data sync. Implement a **Change Data Capture (CDC)** pattern so you only process modified records.

**Q: Slack is noisy. How do you ensure your AI insights are "actionable" and not just more noise?**
**A:** **Thresholding and Summarization**. Don't send every detail. Use the AI to categorize alerts into "Critical", "Warning", and "FYI". Only "Critical" items should @mention a user. Use Slack's **Threaded replies** to keep the conversation organized.

## 🛠️ Code Snippet: Slack Interactive Message (JSON)
```json
{
	"blocks": [
		{
			"type": "section",
			"text": {
				"type": "mrkdwn",
				"text": "*AI Alert:* Lead 'Acme Corp' hasn't been contacted in 5 days."
			}
		},
		{
			"type": "actions",
			"elements": [
				{
					"type": "button",
					"text": { "type": "plain_text", "text": "Draft Email" },
					"value": "draft_email_acme",
					"action_id": "draft_action"
				}
			]
		}
	]
}
```
