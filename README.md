# 🤖 Logitrix Customer Support Automations (n8n)

An intelligent, multi-channel customer support system built in [n8n](https://n8n.io/). This repository contains three distinct workflows that handle incoming support requests across **WhatsApp**, **Gmail**, and **Slack**. 

The system leverages Google Gemini (PaLM) for intelligent query resolution, cross-references an internal FAQ database, and seamlessly escalates complex issues to human agents while tracking everything in Google Sheets.

## ✨ Core Features (All Channels)

* **Multi-Channel Triggers:** Dedicated workflows for WhatsApp, Gmail, and Slack.
* **Intelligent Routing:** Automatically detects intents like "human", "refund", "urgent", "billing", or "complaint" to bypass AI and immediately escalate to human agents.
* **FAQ Matching:** Queries a Google Sheet FAQ database for instant resolution on common questions, saving API costs and reducing response time.
* **AI Agent Resolution:** If no FAQ match is found, an AI Agent (powered by Google Gemini) processes the query and generates a context-aware response.
* **Human Escalation Fallback:** If the AI agent is not confident or fails, the system automatically opens a support ticket in Google Sheets, notifies the team, and sends a holding message to the user.
* **Contextual Memory:** Uses LangChain's Buffer Window Memory to remember conversation history (up to 24 hours) based on the user's phone number or email address.
* **Centralized Logging:** All chats and generated support tickets are recorded in a centralized Google Sheet CRM.

## 🏗 Repository Structure

* `Logitrix Customer Support.json` - The **WhatsApp** support workflow.
* `Logitrix Customer Support - Gmail.json` - The **Email (Gmail)** support workflow.
* `Logitrix Customer Support - Slack.json` - The **Internal/B2B (Slack)** support workflow.

## 📋 Prerequisites

To run these pipelines, you will need:

1. [**n8n**](https://n8n.io/) (Self-hosted or Cloud).
2. **Google Cloud Platform Account** (For Google Sheets and Gmail API credentials).
3. **Google AI Studio / Vertex AI Key** (For the Gemini 3.1 Flash-Lite language model).
4. **Meta Developer Account** (For the WhatsApp Cloud API integration).
5. **Slack Workspace** (For bot integration and agent notifications).

## 🚀 Setup & Installation

### 1. Database Setup

1. Create a new Google Sheet named `Customer Support`.
2. Create three tabs:
    * `Support Tickets` (Columns: Phone, Name, Message, Timestamp)
    * `FAQ` (Columns: User Intent, Approved AI / FAQ Response)
    * `Chatlogs` (Columns: currentDate)
3. Note the Google Sheet ID (the long string in the URL).

### 2. Import Workflows

1. Clone this repository.
2. Open your n8n workspace and go to **Workflows > Import from File**.
3. Import the three `.json` files.

### 3. Configure Credentials & Nodes

For each imported workflow, you will need to update the credentials and specific node parameters:

* **Triggers:** Connect your respective accounts (WhatsApp OAuth, Gmail OAuth, Slack OAuth).
* **AI Model:** Connect your Google Gemini API key to the LangChain Chat Model node.
* **Google Sheets:** Go through every Google Sheets node in the workflows, authenticate your account, and update the Document ID to point to your newly created Google Sheet.
* **Notifications:** 
    * In the WhatsApp workflow, update the `Notify Agent` node with your team's support number.
    * In the Gmail workflow, update the `Notify Agent` node with your team's support email address.
    * In the Slack workflow, update the `Notify Agent` node with your team's designated triage channel ID.

## 💻 How It Works (The Logic Flow)

1. **Trigger:** A user sends a message via WhatsApp, Email, or Slack.
2. **Standardization:** The `Edit Fields` node normalizes the incoming payload (Name, ID, Message, Timestamp) so the rest of the workflow is platform-agnostic.
3. **Triage (`If3` Node):** Checks for trigger words (e.g., "human", "urgent"). If triggered, it skips AI and logs an escalation.
4. **FAQ Check:** Cross-references the query with the Google Sheet `FAQ` tab. If a match is found, it sends the pre-approved response.
5. **AI Processing:** If no FAQ match exists, it fetches the current date and document context, passing it to the Gemini AI Agent to draft a response.
6. **Delivery or Fallback:** 
    * If the AI generates a response, it is cleaned and sent back to the user on their original platform.
    * If the AI fails or the conversation has expired past the 24-hour memory window, a Support Ticket is created, and the user receives a fallback "We will contact you soon" message.

*Created to streamline omni-channel customer support operations.*
```eof

This consolidated README will give anyone looking at your GitHub repository a clear understanding of the entire customer support ecosystem you have built.
