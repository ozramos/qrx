# QRx 1.2 - An Agentic QR Coding Framework

[![QRx Demo GIF](https://github.com/user-attachments/assets/1af752bc-fcd4-4fec-b133-85d079031e29)](https://github.com/RecursiveFaith/qrcoding)

> ***Ongoing research exploring:*** _Is it possible to create a single self-contained QR code that generates a Large Language Model (LLM) simulated Operating System (OS)? Can an LLM OS be used to generate full-dive mixed realities?_

QRx provides a minimal framework to bootstrap an interactive web environment controlled via URL parameters, often delivered through QR codes. It allows you to use Large Language Models (LLMs) to generate and modify web content (HTML, CSS, JS) stored locally in your browser's IndexedDB. It serves as a portable, generative interface – a potential ritual space for AI-assisted self-discovery and creation.

## Core Concept

1.  **Bootstrap:** You start with a single HTML file (`qrx.html`) containing the core QRx JavaScript code, potentially seeded from a QR code.
2.  **Interact via URL:** You use specific URL parameters (`?p`, `?w`, `?b`) and a URL hash (`#filename`) to command the QRx environment. These URLs are the primary control mechanism and can be encoded into QR codes.
3.  **LLM Integration (Generative Power):** The `?p` parameter allows you to instruct a configured LLM (your "Gnosis Copilot") to generate or modify the content associated with the current `#filename`, enabling creation through dialogue.
4.  **Local Persistence:** Content is saved in your browser's IndexedDB, keyed by the `#filename`, making your creations persistent locally.
5.  **Live Preview:** The environment displays a live preview (in an iframe) of the content associated with the current `#filename`.

## Setup

1.  **Get the Code:**
    * Scan the QR code provided in the original documentation (or copy the source code directly).
    * This code is the entire QRx system.
2.  **Create the File:**
    * Create a new file named `qrx.html` on your computer.
    * Paste the copied HTML/JavaScript code into this file and save it.
3.  **Open in Browser:**
    * Open the `qrx.html` file directly in your web browser (e.g., by double-clicking it or using `File > Open`).

## Configuration (LLM Provider)

QRx needs to know which LLM to talk to. You configure this the first time you use the `?p` parameter, or by setting `localStorage` values beforehand.

**Configuration Options (Priority Order):**

1.  **URL Parameters (Temporary Overrides):** You can specify settings directly in the URL for a single session (e.g., `file:///path/to/qrx.html?apiKey=YOUR_KEY&apiModel=MODEL_NAME#home`).
2.  **Local Storage (Persistent):** QRx checks `localStorage` for `apiKey`, `apiHost`, and `apiModel`. You can set these manually in your browser's developer console:
    ```javascript
    localStorage.setItem('apiKey', 'YOUR_API_KEY');
    localStorage.setItem('apiHost', 'YOUR_API_ENDPOINT'); // e.g., 'http://localhost:11434/api/chat' for Ollama
    localStorage.setItem('apiModel', 'MODEL_NAME'); // e.g., 'llama3' or 'deepseek/deepseek-r1'
    ```
3.  **User Prompts (First Use):** If no configuration is found via URL or `localStorage` when you first use `?p`, QRx will prompt you to enter the API Key and Model Name. These will then be saved to `localStorage` for future use.

**Supported Providers:**

* **Ollama (Local):**
    * Requires Ollama installed and running.
    * **API Host:** Typically `http://localhost:11434/api/chat` (or your Ollama server address).
    * **API Key:** Often not required, but enter `ollama` or leave blank if prompted.
    * **Model:** Any model you have pulled (e.g., `llama3`, `mistral`, `deepseek-coder`).
    * **Important:** You *must* [configure CORS in Ollama](https://github.com/ollama/ollama/blob/main/docs/faq.md#how-do-i-configure-ollama-server) to allow requests from the browser (`file://` or your local server's origin). Set `OLLAMA_ORIGINS=*` or a more specific origin.
* **OpenRouter (Cloud):**
    * Requires an OpenRouter account and API key.
    * **API Host:** `https://openrouter.ai/api/v1/chat/completions`
    * **API Key:** Your OpenRouter API key.
    * **Model:** Any model supported by OpenRouter (e.g., `deepseek/deepseek-r1`, `openai/gpt-4o`).

## Usage

Once `qrx.html` is open in your browser, you interact with it primarily by changing the URL in the browser's address bar (or by scanning QR codes that point to specific URLs).

**Interface:**

* **Top Pane (Iframe):** Shows a live preview of the HTML/CSS/JS content associated with the current `#hash`.
* **Bottom Pane (Textarea):** An editor where you can directly view and modify the raw content for the current `#hash`. Changes are auto-saved to IndexedDB.

**Core Interaction: URL Structure**

The general URL structure is:

`file:///path/to/qrx.html[?parameter=value[&parameter=value]][#filename]`

Or, if using a server:

`http://localhost:9000/qrx.html[?parameter=value[&parameter=value]][#filename]`

* **`#filename` (Hash):** This acts like a filename or a distinct context. QRx saves and loads content from IndexedDB based on this hash.
    * `#home` is the default if no hash is provided.
    * `#calculator` refers to a file/context named "calculator".
    * `#my-styles` refers to a file/context named "my-styles".
    * Changing the hash in the URL loads the corresponding content into the editor and preview.
* **`?parameter=value` (Query Parameters):** These are the commands that trigger actions within QRx.

## QRx URL API: Commanding the Generative Environment

These parameters are the core mechanism for interacting with and controlling the QRx environment. Think of them as commands sent via the URL.

---

1.  **Parameter:** `?p=<prompt>`
    * **Name:** **Prompt**
    * **Action:** Instructs the integrated LLM (your "Gnosis Copilot") to generate or modify the content of the current `#filename` based on the natural language `<prompt>`. The LLM's response *replaces* the entire existing content associated with that `#filename`.
    * **Purpose:** This is the primary engine for **generative creation and evolution** within QRx. Use it to:
        * Create new interfaces, applications, text, or code from scratch (`?p=create a simple counter app#counter`).
        * Iteratively refine existing content (`?p=change the button color to blue#counter`).
        * Translate ideas into functional code or structured text.
        * Engage in a dialogue with the AI to sculpt the digital environment stored under the `#filename`.
    * **Example:**
        `file:///path/to/qrx.html?p=Create%20a%20basic%20HTML%20page%20with%20a%20red%20heading%20saying%20Hello#hello`
    * **Flow:** QRx sends the prompt and current page context (`#hello`'s content) to the configured LLM. It receives the response, updates the editor and IndexedDB storage for `#hello` with the new content, and then removes the `?p=...` parameter from the URL, reloading the page to reflect the change cleanly.

---

2.  **Parameter:** `?w=<url_encoded_content>`
    * **Name:** **Write**
    * **Action:** Directly injects the URL-decoded `<content>` into the IndexedDB storage for the current `#filename`. This **completely overwrites** any previous content for that `#filename` and **bypasses the LLM entirely**.
    * **Purpose:** Provides precise, direct control over content. Essential for:
        * Bootstrapping initial content or templates.
        * Loading raw code snippets or data structures exactly as provided.
        * Pasting specific configurations or text without LLM interpretation or modification.
        * Setting a known state for a `#filename`.
    * **Example (writes `<h1>Direct Write</h1>` to `#direct`):**
        `file:///path/to/qrx.html?w=%3Ch1%3EDirect%20Write%3C%2Fh1%3E#direct`
    * **Flow:** QRx URL-decodes the value of `?w`, writes it directly to the IndexedDB storage associated with `#direct`, and then removes the `?w=...` parameter from the URL, reloading the page.

---

3.  **Parameter:** `?b=<key1>,<key2>,...`
    * **Name:** **Boot Dependencies**
    * **Action:** Loads content from one or more other IndexedDB keys (specified by `<key1>`, `<key2>`, etc.) and **prepends it to the preview iframe** *before* the main content of the current `#filename`. This happens during the preview rendering; it **does not modify the saved content** of the current `#filename` itself. Content from a special `boot` key (if it exists) is loaded first, followed by the keys specified in `?b`.
    * **Purpose:** Enables modularity and code reuse. Use it to:
        * Inject shared CSS libraries (like Tailwind, Bootstrap) stored under a specific key (e.g., `#tailwind_setup`).
        * Load common JavaScript utility functions stored under another key (e.g., `#utils`).
        * Include standard headers, footers, or UI components across multiple "pages" (`#filename` contexts) without duplicating code in each one.
    * **Example (loads `#styles` and `#utils` before rendering `#app`):**
        `file:///path/to/qrx.html?b=styles,utils#app`
    * **Flow:** When QRx renders the preview for `#app`, it first fetches content from the `boot` key (if any), then `#styles`, then `#utils`, and finally `#app`. It concatenates these and writes them into the iframe. The editor pane still only shows the content directly saved under `#app`. The `?b=...` parameter remains in the URL as it's read during rendering, not actively removed by a specific action.

---

4.  **Parameters:** `?apiKey=<key>`, `?apiHost=<host>`, `?apiModel=<model>`
    * **Name:** **Environment Overrides**
    * **Action:** Temporarily override the LLM configuration settings (API Key, Host endpoint, Model name) for the current session. These take precedence over settings stored in `localStorage`.
    * **Purpose:** Useful for:
        * Quickly testing different LLM models or endpoints without changing persistent settings.
        * Using specific API keys for certain tasks.
        * Sharing a QRx link that uses a specific configuration.
    * **Example:**
        `file:///path/to/qrx.html?apiModel=google/gemini-pro&p=summarize%20this#summary`
    * **Flow:** QRx reads these parameters when making an API call via `?p`. They persist in the URL unless manually removed or superseded by a new URL without them.

---

## Data Persistence (IndexedDB)

* QRx uses your browser's IndexedDB to store the content for each `#filename`.
* **`file://` Protocol:** When you open `qrx.html` directly, the database is tied to that specific file path. If you move or rename the file, you might lose access to the previously saved data (though the old data might still exist in the browser's storage).
* **`http://` Protocol:** If you run QRx through a local web server, the database is tied to the server's origin (e.g., `http://localhost:9000`). This is more stable and recommended for serious use.

## Running a Simple Server (Recommended)

Using a local server avoids issues related to the `file://` protocol.

**Using Python (if installed):**

1.  Open a terminal or command prompt.
2.  Navigate (`cd`) to the directory containing your `qrx.html` file.
3.  Run the command: `python3 -m http.server 9000` (or `python -m SimpleHTTPServer 9000` for older Python 2).
4.  Open your browser and go to `http://localhost:9000/qrx.html`.

Now you can use URLs like `http://localhost:9000/qrx.html?p=create...#mything`.

## Development Notes

* To generate QR Codes for URLs:
    * Construct the full URL (e.g., `http://your-server.com/qrx.html?p=prompt#file`).
    * Use a QR code generator (like [QR Code Generator Library](https://www.nayuki.io/page/qr-code-generator-library)) to encode the URL.
* QR Code Limits: Be mindful of URL length, as QR codes have data limits (around 2,953 bytes or 4,296 alphanumeric characters maximum, but practically less for reliable scanning). URL-encoding content for `?w` can quickly increase length.
* HTML Compression: Compressing the initial `qrx.html` source (e.g., using [HTMLCompressor](https://htmlcompressor.com/compressor)) helps minimize the size for the bootstrap QR code.

## Changelog

* **250406:** Base version described in this README. Enhanced URL API documentation.
* **250405 (QRx v1.2):** Fixed missing semicolon in `qrx.png` source.

