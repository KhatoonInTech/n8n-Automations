<div align="center">
  <h1>AI Podcast Generator Bot via Telegram</h1>
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

 ![portal ](../main/worflow.png)

</div>

---

# AI Podcast Generator Bot via Telegram

## 1. Overview

This n8n workflow transforms a simple user prompt from a Telegram message into a complete, dual-speaker podcast episode. The user can provide a topic via a text message or a voice note. The workflow then uses a series of AI agents to generate a podcast topic, write a script with distinct host and guest personas, convert the script to audio using ElevenLabs' text-to-speech, and sends the final, combined audio file back to the user.

This project is a powerful demonstration of orchestrating multiple AI services (OpenAI for transcription and script generation, ElevenLabs for voice synthesis) within a conversational interface (Telegram).

## 2. Features

-   **Multi-Modal Input:** Accepts both text messages and voice notes as input for the podcast topic.
-   **AI-Powered Topic Extraction:** Intelligently identifies the core topic from the user's input.
-   **Dynamic Script Generation:** Uses an OpenAI model to generate a conversational podcast script between a pre-defined host and guest.
-   **Dual-Speaker Voice Synthesis:** Leverages ElevenLabs to generate distinct voices for the host and guest, making the podcast engaging.
-   **Automated Audio Production:** Stitches individual audio clips for each line of dialogue into a single, seamless MP3 file.
-   **Interactive Telegram Bot:** Communicates its status (e.g., "typing...") and sends the final product directly to the user's chat.

## 3. Setup & Configuration

Before running this workflow, you must configure the necessary credentials and settings.

### Required Credentials

You will need API keys for the following services. Add them in your n8n instance under **Credentials**.

1.  **Telegram:**
    -   **Credential Name:** `telegramApi`
    -   **Purpose:** To listen for and send messages from your Telegram bot.
2.  **OpenAI:**
    -   **Credential Name:** `openAiApi`
    -   **Purpose:** To transcribe voice notes and generate the podcast topic and script.
3.  **ElevenLabs:**
    -   **Credential Name:** `httpHeaderAuth` for Elevenlabs
    -   **Purpose:** To convert the generated script into speech. The header name should be `xi-api-key`.

### Core Configuration (`SETUP` Node)

The most important settings are located in the **SETUP (Set)** node. This is where you define the characters for your podcast.

-   `hostId`: The **Voice ID** from ElevenLabs for the podcast host.
-   `guestId`: The **Voice ID** from ElevenLabs for the podcast guest.
    -   *You can find your Voice IDs in the [ElevenLabs Voice Lab](https://elevenlabs.io/app/voice-lab).*
-   `topic`: This is set dynamically by the AI Agent, but you could hardcode a topic here for testing.
-   `lengthInMin`: A number representing the desired approximate length of the podcast in minutes.
-   `backgroundContextHost`: A short description of the host's persona (e.g., "Marketing Professional, Name: Jakob Smith"). This gives the AI context for writing their dialogue.
-   `backgroundContextGuest`: A short description of the guest's persona (e.g., "Successful Entrepreneur, Name: Joanah Leth").

## 4. How the Workflow Works

The workflow follows a logical progression from receiving input to delivering the final audio file.

1.  **Trigger & Input Handling:**
    -   The **Telegram Trigger** node listens for new messages sent to the bot.
    -   A `Send Typing action` node immediately signals to the user that the bot is processing their request.
    -   A `Switch` node determines if the message is a `Text` message or a `Voice` message. Unsupported types are handled with an error message.

2.  **Content Preparation:**
    -   **If Voice:** The voice message is downloaded and sent to **OpenAI (Whisper)** to be transcribed into text.
    -   **If Text:** The text is used directly.
    -   The `Combine content...` node consolidates the text (whether from the original message or the transcription) into a single data stream.

3.  **AI Topic & Script Generation:**
    -   The first **AI Agent** takes the user's text and extracts a concise `topic` for the podcast.
    -   The **SETUP** node gathers the extracted topic, your pre-defined voice IDs, and persona contexts.
    -   The second **AI Agent** receives all this information and generates a complete podcast script, structured as a JSON array where each object contains an `actorId` (host or guest) and their line of `text`.

4.  **Audio Synthesis Loop:**
    -   The `Split Out` node takes the JSON array of dialogue and splits it into individual items, one for each line.
    -   The `Loop Over Items` node begins processing each line of dialogue one by one.
    -   Inside the loop, the **Elevenlabs Request** node makes an API call to convert the text for that line into speech, using the `actorId` to select the correct voice.
    -   A `Wait` node adds a small delay to avoid hitting API rate limits.

5.  **Audio Stitching & Delivery:**
    -   After the loop completes, the **Code** node executes. This powerful JavaScript snippet gathers all the individual audio clips generated by ElevenLabs.
    -   It concatenates the binary data of each clip into a single, combined MP3 buffer.
    -   The final `Merge` node ensures the `chatId` from the original trigger is available.
    -   The final **Telegram** node sends the `combined` audio file back to the user who made the initial request.

## 5. How to Use

1.  Start a chat with the Telegram bot connected to this workflow.
2.  Send a message with your desired podcast topic (e.g., "The future of renewable energy").
3.  Alternatively, send a voice note explaining the topic you want to discuss.
4.  Wait for the bot to process your request. It will show a "typing..." status.
5.  Receive the final, generated podcast as an audio file in your chat.

## 6. Node Breakdown

| Node Name | Node Type | Purpose |
| :--- | :--- | :--- |
| Listen for incoming events | `Telegram Trigger` | Starts the workflow when a message is received. |
| Send Typing action | `Telegram` | Provides immediate user feedback. |
| Determine content type | `Switch` | Routes the workflow based on input (text vs. voice). |
| Download voice file | `Telegram` | Downloads the audio from a voice message. |
| Convert audio to text | `OpenAI` | Transcribes the voice message to text using Whisper. |
| Combine content... | `Set` | Normalizes text input from different sources. |
| AI Agent (Topic) | `Agent` | Extracts a concise podcast topic from the user's input. |
| SETUP | `Set` | Configures all parameters for the podcast script. |
| AI Agent1 (Script) | `Agent` | Generates the full, dual-speaker podcast script. |
| Structured Output Parser | `Output Parser` | Ensures the script is in a valid JSON format. |
| Split Out | `Split Out` | Breaks the script into individual lines of dialogue. |
| Loop Over Items | `SplitInBatches` | Initiates the loop to process each line of dialogue. |
| Elevenlabs Request | `HTTP Request` | Converts a single line of text to speech. |
| Wait | `Wait` | Prevents API rate-limiting. |
| Code | `Code` | Stitches all individual audio clips into one file. |
| Merge | `Merge` | Combines the final audio with original message data. |
| Telegram (Send Audio) | `Telegram` | Sends the completed podcast back to the user. |
