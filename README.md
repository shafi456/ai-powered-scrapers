# AI Powered Scrapers

A few small projects I built while working through an LLM engineering course, playing around with local LLMs (via Ollama) and web scraping. Nothing fancy — just me getting comfortable with prompting, structured outputs, and pointing the OpenAI SDK at a local model instead of paying for API calls.

All three scripts follow the same basic pattern: scrape a webpage, hand the raw text to a locally-running model with a very opinionated system prompt, and get back something more useful than the wall of text I started with.

## What's in here

**1. Genshin Impact event parser**
Feeds in patch notes / event pages and strips out all the lore and dialogue, leaving just what actually matters — how many Primogems are up for grabs, what you need to have done already to qualify, a checklist of what to actually go do, and the deadline. Built this because I was tired of reading three paragraphs of story text to find out an event needed AR 30.

**2. E-commerce hype-buster**
Give it a product page and it ignores all the marketing copy and pulls out the real specs, then roasts the listing and gives you three blunt reasons not to buy it right now. Made this half as a joke, half because I actually do impulse-buy stuff off Amazon.

**3. Competitive programming problem stripper**
Same idea, different domain — takes a competitive programming problem (the kind with a whole story wrapped around a math problem) and spits out just the core challenge, input/output format, and constraints. No narrative, no character names.

## How it actually works

Every project uses the same setup: the `openai` Python package, but pointed at `http://localhost:11434/v1` instead of OpenAI's servers, which is Ollama's OpenAI-compatible endpoint. So you're calling local models through familiar `client.chat.completions.create()` syntax, with no API key or billing involved (the `api_key="ollama"` in the code is just a placeholder, it doesn't need to be real).

Scraping is handled by `scraper.py`, which grabs a page's content with `requests` + `BeautifulSoup` before it gets passed to the model.

## Running this yourself

You'll need:
- Python 3.10+
- [Ollama](https://ollama.com/download) installed and running
- The specific models each notebook uses pulled locally, e.g.:
  ```
  ollama pull gemma3:12b
  ollama pull deepseek-r1:1.5b
  ollama pull qwen3.5:4b
  ```
  (check the top of each notebook, since they each use a different model)

Then:
```
python -m venv .venv
.venv\Scripts\activate      # Windows
pip install -r requirements.txt
```

Open whichever notebook you want in VS Code / Cursor / Jupyter, pick your `.venv` as the kernel, and run it top to bottom.

## Notes to self / known rough edges

- Scraping breaks on JS-heavy sites since there's no headless browser involved, just a plain GET request. Fine for the static pages I was testing on, not a general-purpose scraper.
- Smaller models occasionally ignore the "ignore the lore" instruction and summarize the story anyway. Bigger models are more reliable but slower on my machine.
- No error handling yet if Ollama isn't running or a page fails to load — on the to-do list.

Built while learning, not trying to be production software. If you clone this and something's broken, it's probably because a model got renamed on Ollama's end or I forgot to note a dependency — open an issue and I'll take a look.
