# AI Chat with Ollama - Single File Application

This is a single HTML file web application that provides a chat interface with an AI model powered by Ollama. It includes features like message streaming, Markdown rendering, image uploads, and configurable Ollama settings.

## Features

*   **Direct Ollama Integration**: Connects to a running Ollama instance.
*   **Streaming Responses**: AI responses can be streamed token by token.
*   **Markdown Support**: AI responses can be rendered as Markdown for better formatting (lists, bold, italics, code blocks).
*   **Image Uploads**: Send images along with your text prompts to multimodal models.
*   **Chat History**: Saves chat history in the browser's LocalStorage.
*   **Configurable Settings (via UI Pop-up)**:
    *   Dark Mode toggle.
    *   Enable/disable response streaming.
    *   Enable/disable Markdown rendering.
    *   Check Ollama server status.
    *   Configure Ollama endpoint URL.
    *   Configure Ollama model name.
    *   Configure Ollama temperature.
    *   Clear chat history.
*   **Neumorphic UI Design**: A soft, modern user interface.
*   **Responsive (Basic)**: Designed for desktop but should adapt to smaller viewports.

## Prerequisites

1.  **Ollama**: You need to have Ollama installed and running on your system.
    *   Download and install from [ollama.com](https://ollama.com/).
2.  **Ollama Model**: A compatible AI model pulled into Ollama. The application defaults to `gemma3:4b`, but you can configure this in the settings. For image support, ensure your chosen model is multimodal (e.g., llava, bakllava).
3.  **Modern Web Browser**: Chrome, Firefox, Edge, Safari, etc.

## Setup Instructions

To ensure the chat application can communicate with your Ollama instance, especially when running the HTML file directly from your local filesystem (`file:///...`), you need to configure Ollama to accept connections from all network interfaces and all origins.

### 1. Install Ollama

If you haven't already, download and install Ollama from [ollama.com](https://ollama.com/).

### 2. Configure Ollama Environment Variables

Ollama needs to be started with specific environment variables to allow the `file:///` page to connect to it (typically running on `http://localhost:11434`).

*   **`OLLAMA_HOST=0.0.0.0`**: This tells Ollama to listen on all available network interfaces, not just `localhost`.
*   **`OLLAMA_ORIGINS=*`**: This configures Ollama's CORS (Cross-Origin Resource Sharing) policy to allow requests from any origin, including `file:///`.

**How to set environment variables (examples):**

**Linux / macOS (Terminal):**
You can set these temporarily before starting Ollama, or add them to your shell's configuration file (e.g., `.bashrc`, `.zshrc`).

```bash
export OLLAMA_HOST=0.0.0.0
export OLLAMA_ORIGINS='*' # It's good practice to quote the asterisk
ollama serve
```
Or, if you are using `systemd` to manage Ollama (common on Linux):
1. Edit the Ollama service file. The location might vary, but a common path is `/etc/systemd/system/ollama.service`.
   ```bash
   sudo systemctl edit ollama.service
   ```
2. This will open an override file. Add the following lines under the `[Service]` section:
   ```ini
   [Service]
   Environment="OLLAMA_HOST=0.0.0.0"
   Environment="OLLAMA_ORIGINS=*"
   ```
3. Save the file, then reload the systemd daemon and restart Ollama:
   ```bash
   sudo systemctl daemon-reload
   sudo systemctl restart ollama
   ```

**Windows (Command Prompt / PowerShell):**

*   **Command Prompt (temporary for the current session):**
    ```cmd
    set OLLAMA_HOST=0.0.0.0
    set OLLAMA_ORIGINS=*
    ollama serve
    ```
*   **PowerShell (temporary for the current session):**
    ```powershell
    $env:OLLAMA_HOST="0.0.0.0"
    $env:OLLAMA_ORIGINS="*"
    ollama serve
    ```
*   **To set them permanently on Windows:**
    1.  Search for "environment variables" in the Start Menu.
    2.  Click "Edit the system environment variables".
    3.  In the System Properties window, click the "Environment Variables..." button.
    4.  Under "System variables" (or "User variables" if you prefer), click "New..."
    5.  Add `OLLAMA_HOST` with value `0.0.0.0`.
    6.  Add `OLLAMA_ORIGINS` with value `*`.
    7.  Click OK on all windows. You may need to restart your Command Prompt/PowerShell or even your PC for these to take full effect, especially for services. Then restart the Ollama application/service.

**Docker:**
If running Ollama via Docker, you can pass these environment variables when starting the container:
```bash
docker run -d --gpus=all -v ollama:/root/.ollama -p 11434:11434 \
  -e OLLAMA_HOST=0.0.0.0 \
  -e OLLAMA_ORIGINS='*' \
  --name ollama ollama/ollama
```

**Important Security Note:** Setting `OLLAMA_HOST=0.0.0.0` and `OLLAMA_ORIGINS=*` makes your Ollama instance accessible from any device on your local network and allows requests from any web page. This is generally acceptable for local development but be mindful of security implications in less trusted network environments.

### 3. Pull an Ollama Model

```bash
ollama pull gemma3:4b
```
You can then change the "Model Name" in the chat application's settings panel.

## Running the Chat App

1.  Ensure Ollama is running with the environment variables configured as described above.
2.  Download the `ai_chat.html` file.
3.  Open the HTML file directly in your web browser (e.g., by double-clicking it, or using "File > Open" in your browser and navigating to the file). The URL in your browser will look like `file:///path/to/ai_chat.html`.

## Settings Overview

Click the three-dots (kebab menu) icon in the top-right corner to access settings:

*   **Dark Mode**: Toggles between light and dark themes.
*   **Stream Responses**: If checked, AI responses will appear token by token. If unchecked, the full response will appear at once.
*   **Use Markdown**: If checked, AI responses will be rendered using Markdown.
*   **Ollama Status**:
    *   **Check Ollama**: Pings the Ollama server (root endpoint) to verify it's reachable.
    *   Status text will indicate if Ollama is running, reachable, or not reachable.
*   **Ollama Configuration**:
    *   **Endpoint URL**: The URL for the Ollama API (defaults to `http://localhost:11434/api/generate`).
    *   **Model Name**: The name of the Ollama model to use (defaults to `gemma3:4b`).
    *   **Temperature**: Controls the randomness of the AI's output (0.1 to 1.0).
*   **Chat Data**:
    *   **Clear Chat History**: Removes all saved messages from the browser's local storage.

## Troubleshooting

*   **AI is not responding / "Not Reachable" status**:
    1.  **Check Ollama Server**: Ensure Ollama is running.
    2.  **Environment Variables**: Verify `OLLAMA_HOST` is set to `0.0.0.0` (or your network IP) and `OLLAMA_ORIGINS` is set to `*` (or includes `file://` appropriately, though `*` is easiest for `file:///` testing). Restart Ollama after setting these.
    3.  **Endpoint URL**: In the chat app's settings, confirm the "Endpoint URL" is correct (usually `http://localhost:11434/api/generate`). If Ollama is on a different machine or port, adjust this.
    4.  **Model Name**: Ensure the "Model Name" in settings corresponds to a model you have pulled in Ollama (e.g., `ollama list`).
    5.  **Browser Console**: Open your browser's developer console (usually F12) and check for any error messages, especially CORS errors or network errors.
    6.  **Firewall**: Ensure your firewall is not blocking connections to Ollama on port 11434.

*   **Images not working**:
    *   Make sure the Ollama model you've selected in the settings (e.g., `gemma3:4b`) is a multimodal model capable of processing images. Standard text models will ignore the image.

