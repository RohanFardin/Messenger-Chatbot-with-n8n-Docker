📩 Messenger Chatbot (n8n Project)

This project is an automated Messenger chatbot built with n8n
.
It replies to customer queries based on our company’s Google Sheet dataset.

📌 Overview

✅ Connects Messenger with our company dataset on Google Sheets.

✅ Reads customer messages and matches them against the dataset.

✅ Automatically replies with the correct information.

✅ Helps reduce manual work and improve response time.

🚀 How It Works

A user sends a message on Messenger.

The workflow checks the dataset in Google Sheets.

If a matching entry is found → the chatbot replies with the corresponding answer.

If no match → a default fallback message is sent.

⚡ Benefits

Faster customer support.

Consistent and accurate replies.

Easy to update answers (just update the Google Sheet).

🔧 Usage

Import the JSON workflow into your n8n instance.

Configure Messenger and Google Sheets credentials.

Update the dataset in Google Sheets as needed.


Details of the Workflow:
Webhook: This node is connect msgr 
