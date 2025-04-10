# vcai-tutorial

Open this repository in a GitHub Codespace, then run the following commands in the terminal:

### Setup

```bash
bash setup.sh
```

That will install [llm](https://llm.datasette.io/en/stable/), a Python tool for interacting with different LLMs, and llm-groq, which specifically connects to [Groq](https://groq.com/). You will need to create an account and generate an API key at https://console.groq.com/keys. Copy that key, then do the following command in the terminal:

```bash
llm keys set groq
```

You will paste your copied key in the terminal (it won't appear, and you might get a small popup with the word "paste" in grey. Wait a couple of seconds and you'll be able to click on it.). To make sure it worked, run this command in the terminal:

```bash
llm -m groq-llama-3.3-70b "10 names for a pet turtle"
```

You can see all of the models you have access to using llm with the following command:

```bash
llm models
```

### Text Models

The llm library allows us to chat with LLMs, but also to pass things to those LLMs _and_ to log our conversations in a SQLite database. Here's one way we can start: asking it to summarize the specific contents of a web page that we'll fetch using curl and a tool called [strip-tags](https://github.com/simonw/strip-tags) that allows us to narrow in on single div in the HTML, which reduces the amount of tokens we send to the LLM (they have limits):

```bash
 curl -s https://vcai.umd.edu/about/ | strip-tags .container | llm -m groq-llama-3.3-70b "summarize this text in 3 paragraphs"
```

Text models can do more than summarize; they can restructure information to make it more useful. We'll take a recent document describing sanctions of Maryland attorneys (the sanctionsfy25.txt file in this repository) and send it to Llama 3.3 using the `cat` command, asking the LLM to produce JSON data of some of the contents.

```bash
cat sanctionsfy25.txt | llm -m groq-llama-3.3-70b "produce only an array of JSON objects based on the text with the following keys: name, sanction, date, description. The date should be in the yyyy-mm-dd format. No yapping." 
```

That "no yapping" thing? That's one of the ways you get LLMs to stop narrating their output and just give you what you specify. There are other ways - most newer models support "structured output" in JSON format.

### Vision Models

One of the options we have with Groq is to use a "vision" model that can describe the information in certain types of images. Let's test that out using an image from this Maryland physician discipline report: https://www.mbp.state.md.us/BPQAPP/orders/D002592202.265.pdf. The specific vision model we're using only accepts jpg, gif and png files, so I've created a PNG from the first page. Run this command in the terminal:

```bash
llm -m groq/llama-3.2-90b-vision-preview --attachment md_doc.png "what is the license number from this image?"
```

You should get something like: "The license number in the image is D25922." as a response. New models that support video and audio also are available and can provide similar capabilities.