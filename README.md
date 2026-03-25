# AI Web Scraper

An intelligent web scraping tool that combines browser automation with a local LLM to let you extract specific information from any website using plain English — no CSS selectors or XPath required.

---

## What It Does

1. **Scrape** — Enter any URL. The app launches a cloud browser (via Bright Data) that handles JavaScript rendering and CAPTCHA solving automatically.
2. **Clean** — The raw HTML is parsed and stripped of scripts, styles, and noise — leaving only readable text content.
3. **Parse** — You describe what you want in plain English (e.g. *"extract all product prices"*) and the LLM pulls exactly that from the scraped content.

---

## Tech Stack

| Layer | Technology | Purpose |
|---|---|---|
| **UI** | [Streamlit](https://streamlit.io) | Web interface |
| **Browser Automation** | [Selenium](https://selenium.dev) | Controls the remote browser |
| **Cloud Browser** | [Bright Data Scraping Browser](https://brightdata.com) | Handles JS rendering & CAPTCHA solving |
| **HTML Parsing** | [BeautifulSoup4](https://www.crummy.com/software/BeautifulSoup/) | Extracts and cleans page content |
| **LLM Orchestration** | [LangChain](https://langchain.com) | Prompt templating and LLM chaining |
| **Local LLM** | [Ollama](https://ollama.com) + Llama 3.1 | Natural language parsing of scraped data |

---

## Architecture & Tech Flow

```
User enters URL
      │
      ▼
┌─────────────────────────┐
│     Streamlit UI         │  main.py
│  (browser at :8501)      │
└────────────┬────────────┘
             │
             ▼
┌─────────────────────────┐
│   Bright Data Cloud      │  scrape.py
│   Scraping Browser       │◄── Selenium Remote WebDriver
│  (JS render + CAPTCHA)   │
└────────────┬────────────┘
             │  raw HTML
             ▼
┌─────────────────────────┐
│     BeautifulSoup        │  scrape.py
│  Extract <body> content  │
│  Strip scripts & styles  │
│  Normalize whitespace    │
└────────────┬────────────┘
             │  clean text
             ▼
┌─────────────────────────┐
│   Split into 6000-char   │  scrape.py
│        chunks            │
└────────────┬────────────┘
             │  text chunks[]
             ▼
┌─────────────────────────┐
│  LangChain Prompt Chain  │  parse.py
│  + Ollama (Llama 3)      │
│  Parse per user query    │
└────────────┬────────────┘
             │  extracted result
             ▼
┌─────────────────────────┐
│     Streamlit UI         │
│   Display results        │
└─────────────────────────┘
```

---

## Prerequisites

- Python 3.10+
- [Ollama](https://ollama.com) installed and running
- A [Bright Data](https://brightdata.com) account with a Scraping Browser zone

---

## Setup & Installation

### 1. Clone the repository

```bash
git clone <your-repo-url>
cd AIWEBSCRAPER
```

### 2. Create and activate virtual environment

```bash
python -m venv ai
# Windows
ai\Scripts\activate
# macOS/Linux
source ai/bin/activate
```

### 3. Install dependencies

```bash
pip install -r requirement.txt
```

### 4. Configure environment variables

Create a `.env` file in the project root:

```env
SBR_WEBDRIVER=https://<customer-id>-zone-<zone-name>:<password>@brd.superproxy.io:9515
```

Get this URL from your Bright Data dashboard under **Scraping Browser > Access Parameters**.

### 5. Pull the Llama 3 model

```bash
ollama pull llama3
```

### 6. Start Ollama (in a separate terminal)

```bash
ollama serve
```

### 7. Run the app

```bash
streamlit run main.py
```

Open `http://localhost:8501` in your browser.

---

## How to Use

1. Paste a website URL into the input field
2. Click **Scrape Website** — wait for the page to load and content to appear
3. Expand **View DOM Content** to see the cleaned text
4. In the text area below, describe what you want to extract:
   - *"List all the article headlines"*
   - *"Extract product names and their prices"*
   - *"Get all email addresses mentioned on the page"*
5. Click **Parse Content** — the LLM returns only what you asked for

---

## License

MIT
