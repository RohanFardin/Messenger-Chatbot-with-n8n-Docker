# Human-Like AI Sales Agent for Facebook Messenger

This n8n project outlines a sophisticated, human-like AI agent designed for Facebook Messenger. The agent's primary function is to interact with users, understand their inquiries, and provide them with perfect matches for their needs from a connected data source.

## Prerequisites

Before deploying this workflow, you must have a Meta Developer App configured correctly with the Facebook Messenger API. Upon successful setup, you will receive a **Page Access Token**. This token is critical and will be used in the `HTTP Request` nodes to authorize communication with the Facebook Graph API.

## Workflow Nodes Explained

This workflow is composed of several interconnected nodes, each with a specific function. Below is a detailed breakdown of each component.

### Webhook & Initial Request Handling

*   **`Webhook`**
    *   This is the entry point of the workflow. It triggers whenever a user sends a message to the connected Facebook Page.
    *   It handles both `GET` (for initial Facebook verification) and `POST` (for incoming messages) requests.

*   **`GET --> IF`**
    *   This conditional node is used exclusively for the initial webhook verification process.
    *   It checks if the `hub.verify_token` from the `GET` request matches the one you've set, confirming that the request is legitimately from Facebook.

*   **`RTW-1 (Respond to Webhook)`**
    *   If the `GET` request is successfully verified, this node responds with a `200 OK` status code, completing the webhook setup.

*   **`RTW-2 (Respond to Webhook)`**
    *   If the verification fails, this node responds with a `404 Not Found` error code.

### Message Routing & Pre-processing

*   **`Switch`**
    *   This node inspects the incoming message from the user.
    *   It routes the workflow based on the message type, primarily checking if it is `Text` or an `Image`.

*   **`Image Edit Fields` & `Send Image MSG`**
    *   If the incoming message is an image, the workflow currently does not support it.
    *   The `Image Edit Fields` node prepares the necessary sender/receiver IDs.
    *   The `Send Image MSG` node sends a pre-defined message to the user stating that images cannot be processed at this time.

*   **`POST --> IF`**
    *   This node acts as a user filter for incoming text messages.
    *   You can configure it to block or ignore messages from specific user IDs, preventing them from interacting with the chatbot.

*   **`RTW (Respond to Webhook)`**
    *   This is a critical node. It immediately sends a `200 OK` response back to Facebook to acknowledge that the message has been received.
    *   **Importance:** Without this, Facebook's API might time out and resend the same message multiple times, causing the chatbot to send duplicate replies to the user.

*   **`Edit Fields`**
    *   This node extracts and standardizes key information from the incoming message payload, such as the `sender_id`, `receiver_id`, and the `message` text, making it easily accessible for downstream nodes.

### AI-Powered Intent Classification

*   **`Classify (Agent)`**
    *   This is the first AI agent in the workflow. Its sole purpose is to classify the user's intent.
    *   It determines whether the user's message is a general "conversational" query or a specific "apartment inquiry."

*   **`OpenAI 4.1 Nano`**
    *   This is the Language Model (LLM) that powers the `Classify` agent. The nano model is chosen for this task because it is fast and efficient for simple classification.

*   **`Code`**
    *   This node takes the JSON output from the `Classify` agent and formats it correctly so it can be used by the subsequent conditional node.

*   **`Conversation or Apartment (IF)`**
    *   Based on the output from the `Code` node, this `IF` node splits the workflow into two distinct paths: one for handling normal conversations and one for handling apartment inquiries.

### Core AI Agents & Logic

*   **`HTTP MSG Seen` & `HTTP MSG Seen1`**
    *   These nodes send an API request to Facebook to create the "seen" receipt, letting the user know their message has been read by the chatbot.

*   **`HTTP MSG Typing On` & `HTTP MSG Typing On1`**
    *   These nodes trigger the "typing..." indicator in the chat window. This simulates a human-like interaction and informs the user that a response is being generated.

*   **`Normal Conversation (Agent)`**
    *   This agent is responsible for handling all general, non-sales-related conversations. It can engage in small talk and answer general questions, providing a friendly user experience.

*   **`Apartment Agent`**
    *   This is the main sales agent. It is designed to handle all apartment-related inquiries, extract user requirements, search for matches, and present the findings.

*   **`Google Sheets (as a Tool)`**
    *   Both agents use a Google Sheet as their primary knowledge base. This sheet stores the data for apartment listings and FAQs, which the agents can search and reference to answer user questions accurately.

*   **`OpenAI 4.1 Mini`**
    *   The powerful `gpt-4.1-mini` model is used for both the `Normal Conversation` and `Apartment Agent`.
    *   Its parameters (like `temperature`, `maxTokens`, etc.) are fine-tuned to ensure response consistency and quality while managing costs.

### Sending the Final Response

*   **`Send Apartment MSG` / `Send Conversational MSG`**
    *   These are the final nodes in the workflow.
    *   Depending on the path taken, the appropriate node takes the generated response from its respective AI agent and sends it as a message to the user via the Facebook Graph API.
