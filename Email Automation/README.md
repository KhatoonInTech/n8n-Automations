<div align="center">
  <h1>AI Email Assistant via Telegram</h1>
  <a class="header-badge" target="_blank" href="https://www.linkedin.com/in/khatoonintech/">
  <img src="https://img.shields.io/badge/style--5eba00.svg?label=LinkedIn&logo=linkedin&style=social">
  </a>
  

<sub>Author:
<a href="https://www.linkedin.com/in/Khatoonintech/" target="_blank">Ayesha Noreen</a><br>
<small> <i>#KhatoonInTech CE' 27 @BZU |Agentic AI & Automation Engr @ DevRolin | ByteWise Certified ML/DL Engineer|GSoC Contributor | SWEfellow: Confiniti. ,HeadStarterAI</i> </small>
</sub>
<br>
<br>
<br>

 ![portal ](../LinkedIn%20Post%20Automation/workflow.png)

</div>

---

# AI Email Assistant via Telegram

## 1. Overview

This n8n workflow creates an intelligent AI email assistant that you can command directly from Telegram. The agent can read your emails, search your inbox, send new messages, reply to threads, and manage labels in your Gmail account, all based on natural language commands.

The user can interact with the bot using either text messages or voice notes, making it a highly accessible and powerful productivity tool. The workflow uses an AI Agent equipped with a suite of Gmail "tools," allowing it to understand a user's intent and execute the corresponding action in their Gmail account.

## 2. Features

-   **Conversational Interface:** Users interact with the agent via Telegram, a familiar chat app.
-   **Multi-Modal Input:** Accepts both text and voice commands for maximum flexibility.
-   **AI-Powered Intent Recognition:** The core AI Agent understands user requests like "send an email to...", "find my last email from...", or "reply to this thread...".
-   **Full Gmail Integration:** Equipped with a comprehensive set of tools to:
    -   Send new emails.
    -   Search and retrieve emails (`getAll`, `get`).
    -   Reply to existing email threads.
    -   Add labels to messages.
    -   List all available labels.
-   **Stateful Conversation:** A `Memory` module allows the agent to remember the context of the conversation, enabling follow-up questions and more natural interactions.
-   **Real-time Feedback:** The bot sends a "typing..." status to let the user know their command is being processed and delivers the AI's final response or confirmation back to Telegram.

## 3. Setup & Configuration

To get this workflow running, you must configure the necessary credentials.

### Required Credentials

You will need API keys and OAuth connections for the following services. Add them in your n8n instance under **Credentials**.

1.  **Telegram:**
    -   **Credential Name:** `telegramApi`
    -   **Purpose:** To listen for and send messages from your Telegram bot.
2.  **OpenAI (for Transcription):**
    -   **Credential Name:** `openAiApi`
    -   **Purpose:** To transcribe voice notes into text using the Whisper API.
3.  **OpenRouter:**
    -   **Credential Name:** `openRouterApi`
    -   **Purpose:** To provide the Large Language Model (LLM) that powers the main AI Agent. OpenRouter allows for flexible model selection.
4.  **Gmail:**
    -   **Credential Name:** `gmailOAuth2`
    -   **Purpose:** This is the most critical credential. It grants the workflow permission to access and manage your Gmail account on your behalf. You must authorize it via the OAuth2 flow.

## 4. How the Workflow Works

The workflow is centered around a powerful AI Agent that decides which tool to use based on the user's command.

1.  **Trigger & Input Processing:**
    -   The **Telegram Trigger** listens for any new message.
    -   The bot immediately sends a `Send Typing action` to acknowledge the message.
    -   A `Switch` node checks if the input is a `Text` message or a `Voice` message.
    -   If it's a voice message, it's downloaded and transcribed to text by the **Convert audio to text (OpenAI)** node.
    -   The `Combine content...` node ensures that regardless of the input source, the command is available as a single text string.

2.  **The AI Agent Core:**
    -   The **AI Agent** node is the heart of the workflow. It receives the user's text command.
    -   **Memory:** The `Simple Memory` node is connected to the agent, allowing it to remember previous turns in the conversation. This is session-keyed by the user's `chat.id` to keep conversations separate.
    -   **LLM:** The `OpenRouter Chat Model` provides the intelligence, processing the user's prompt and the conversation history.
    -   **Tools:** A suite of **Gmail Tool** nodes are connected to the agent. The agent does not execute them directly but *knows they exist*. Based on the user's request, the LLM will decide which tool is appropriate (e.g., `send`, `reply`, `get`) and what parameters to use (e.g., recipient, subject, message content). The n8n agent framework then executes that specific tool.

3.  **Execution and Response:**
    -   Once the AI Agent has processed the command and potentially used a Gmail tool, it generates a final text response.
    -   This response could be a confirmation ("Email sent successfully!"), the content of a requested email, or a clarifying question.
    -   The final **Telegram** node sends this response back to the user in their chat.

## 5. How to Use

1.  Start a chat with the Telegram bot connected to this workflow.
2.  Give it a command in natural language.
    -   **Send an email:** "Send an email to john.doe@example.com with the subject 'Meeting Follow-up' and the message 'Hi John, just following up on our meeting...'"
    -   **Search for an email:** "Find the last email I got from Jane."
    -   **Reply to an email:** "Reply to that last email and say 'Thanks, I'll take a look!'" (This uses the conversation memory).
    -   **Label an email:** "Add the 'Urgent' label to my last email from my boss."
3.  The bot will process your request, perform the action in your Gmail account, and respond with a confirmation or the information you requested.

## 6. Node Breakdown

| Node Name | Node Type | Purpose |
| :--- | :--- | :--- |
| Listen for incoming events | `Telegram Trigger` | Starts the workflow when a message is received. |
| Determine content type | `Switch` | Routes logic based on text vs. voice input. |
| Download voice file | `Telegram` | Downloads audio from a voice message. |
| Convert audio to text | `OpenAI` | Transcribes voice commands to text. |
| Combine content... | `Set` | Normalizes the user command into a single text field. |
| **AI Agent** | **`Agent`** | **The core brain; understands user intent.** |
| OpenRouter Chat Model | `lmChatOpenRouter`| The LLM that powers the agent's reasoning. |
| Simple Memory | `memoryBufferWindow` | Remembers the history of the conversation. |
| **Gmail Tools (x6)** | **`gmailTool`** | **The "hands" of the agent; performs actions in Gmail.** |
| Telegram (Response) | `Telegram` | Sends the agent's final response back to the user. |

---

<div align="center">
<h3>For any query/help ,please contact our developer:</h3>  
Developer : <a href="https://www.linkedin.com/in/Khatoonintech/" target="_blank">Ayesha Noreen</a><br>
   <small> ByteWise Certified ML/DL Engineer|GSoC Contributor | SWEfellow: Confiniti. ,HeadStarterAI </small>
<br> <a href="https://www.github.com/Khatoonintech/" target="_blank"> Don't forget to ⭐ our repo </a><br>


</div>
