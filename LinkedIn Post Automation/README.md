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

 ![portal ](../LinkedIn%20Post%20Automation/workflow.png)

</div>

---

# Automated AI News to LinkedIn Post Generator

## 1. Overview

This n8n workflow automates the entire process of content creation and social media management. It runs on a weekly schedule, automatically finds the latest AI news, scrapes the content, uses a Large Language Model (LLM) to write a detailed LinkedIn post, generates a unique accompanying image, and posts everything to a specified LinkedIn company page.

This is a fire-and-forget solution for maintaining an active, relevant social media presence with minimal human intervention.

## 2. Features

-   **Scheduled Execution:** Automatically runs every week (configurable to any schedule).
-   **Dynamic News Sourcing:** Uses Google Programmable Search to find the most relevant, recent AI news article.
-   **Robust Web Scraping:** Extracts the full HTML content of the news article.
-   **Intelligent Text Cleaning:** A dedicated Python script aggressively cleans the raw HTML, removing scripts, styles, tables, and navigation to isolate the core article text.
--   **Advanced Content Generation:** Leverages a powerful LLM via Groq's fast inference API to write a high-quality, long-form LinkedIn post based on the article's content.
-   **AI Image Generation:** Uses Google's Imagen model to create a custom visual (meme/image) based on the post's title and visual idea.
-   **Automated Social Posting:** Publishes the generated title, post text, and unique image directly to a LinkedIn Company Page.

## 3. Setup & Configuration

Before activating this workflow, several credentials and parameters must be set up correctly.

### Required Credentials

Add the following credentials in your n8n instance under **Credentials**.

1.  **Groq:**
    -   **Credential Name:** `groqApi`
    -   **Purpose:** To access the LLM for content generation.
2.  **LinkedIn:**
    -   **Credential Name:** `linkedInOAuth2Api`
    -   **Purpose:** To post the final content to your company page. Ensure it has the necessary `w_organization_social_sharing` permissions.
3.  **Google API Key (for both Search and Imagen):**
    -   You will need a single Google Cloud API key. This key will be used in two places.
    -   **Enable these APIs** in your Google Cloud Project:
        -   `Custom Search API`
        -   `Vertex AI API` (for Imagen)
    -   Paste this key into the `AI NEWS Fetcher` and `Visuals Generator` nodes.

### Node-Specific Configuration

You must manually edit the following nodes:

1.  **AI NEWS Fetcher (`HTTP Request`):**
    -   `key`: Your Google Cloud API Key.
    -   `cx`: Your Google Programmable Search Engine ID. Make sure it's configured to search the websites you trust for news.

2.  **Visuals Generator (`HTTP Request`):**
    -   `x-goog-api-key`: Your Google Cloud API Key.

3.  **Create a post (`LinkedIn`):**
    -   `organization`: Replace `YOUR_URN` with the actual URN of your LinkedIn Company Page. You can find this in your page's URL (e.g., `urn:li:organization:12345678`).

## 4. How the Workflow Works

The workflow executes in a logical, linear sequence.

1.  **Trigger:**
    -   The **Schedule Trigger** starts the workflow every Saturday at 11:00 AM.

2.  **Information Gathering:**
    -   **AI NEWS Fetcher:** Queries the Google Custom Search API for the top AI news article from the last day (`dateRestrict=d1`).
    -   **Web Scrapper:** Takes the link from the search result and fetches the full HTML content of the article.

3.  **Content Processing:**
    -   **plain_text (Code Node):** This Python script performs a crucial cleanup. It takes the raw HTML, removes all scripts, styles, tables, navigation, and other non-content elements, and truncates the result to a maximum of 5000 characters. This provides a clean, focused input for the AI.

4.  **AI Content Generation:**
    -   **AI Agent:** This is the brain of the operation. It receives the cleaned text and, using a powerful prompt, instructs the **Groq Chat Model** to:
        -   Create a catchy `title`.
        -   Write a detailed, long-form `post_text` in a conversational tone, using Unicode for formatting.
        -   Suggest a `visual_idea` for an accompanying image.
    -   The **Structured Output Parser** ensures the AI's response is in the correct JSON format.

5.  **Visual Creation:**
    -   **Visuals Generator:** Takes the `title` and `visual_idea` from the AI Agent and sends a prompt to the Google Imagen API to generate an image.
    -   **bs64 decoder:** Converts the Base64 image data returned by the API into a binary file that can be uploaded.

6.  **Publication:**
    -   **Create a post:** The final node takes all the generated assets—the `title`, the `post_text`, the original article `link` for reference, and the newly created binary `image`—and publishes them as a new post on the configured LinkedIn Company Page.

## 5. Node Breakdown

| Node Name | Node Type | Purpose |
| :--- | :--- | :--- |
| Schedule Trigger | `ScheduleTrigger` | Starts the workflow automatically on a weekly basis. |
| AI NEWS Fetcher | `HTTPRequest` | Finds the latest AI news article using Google Search. |
| Web Scrapper | `HTTPRequest` | Fetches the raw HTML content of the news article. |
| plain_text | `Code` | Cleans and sanitizes the HTML to extract core text. |
| AI Agent | `Agent` | Orchestrates the LLM to generate post title, text, and visual idea. |
| Groq Chat Model | `lmChatGroq` | The LLM used for high-speed text generation. |
| Structured Output Parser | `OutputParser` | Enforces a strict JSON output from the AI. |
| Visuals Generator | `HTTPRequest` | Generates a custom image using Google Imagen. |
| bs64 decoder | `ConvertToFile` | Decodes the image from Base64 to a usable binary format. |
| Create a post | `LinkedIn` | Publishes the final content to the LinkedIn Company Page. |

---

<div align="center">
<h3>For any query/help ,please contact our developer:</h3>  
Developer : <a href="https://www.linkedin.com/in/Khatoonintech/" target="_blank">Ayesha Noreen</a><br>
   <small> ByteWise Certified ML/DL Engineer|GSoC Contributor | SWEfellow: Confiniti. ,HeadStarterAI </small>
<br> <a href="https://www.github.com/Khatoonintech/" target="_blank"> Don't forget to ⭐ our repo </a><br>


</div>
