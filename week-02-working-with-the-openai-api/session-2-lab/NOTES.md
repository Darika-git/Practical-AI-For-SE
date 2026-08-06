# Lab notes

*Fill this in as you go — it's part of your submission.*

## Reflection questions

**What happens when temperature is changed from 0.2 to 1.0?**

Lower temperature (0.2) produces more deterministic, focused, and repeatable outputs. Higher temperature (1.0) increases randomness, making outputs more creative and varied but less predictable.

**Why should an application not retry every API error?**

Some errors are terminal and cannot be fixed by retrying, such as an invalid API key, a malformed request, or a context-length overflow. Retrying these wastes resources, increases latency, and may trigger additional rate limits. Only transient errors like rate limits or temporary network failures should be retried.

**Why should the API key not be stored directly in the source code?**

Hard-coding the API key exposes it to anyone who reads the source or accesses the repository. If the code is committed or shared, the key is compromised. Keys should be stored in environment variables or a `.env` file that is excluded from version control.

**Why does conversation history increase token usage?**

The Chat Completions API is stateless, so the full conversation history must be resent with every request. Each additional user and assistant message adds tokens, causing token usage to grow linearly with conversation length.

**What is the main advantage of streaming?**

Streaming returns tokens progressively as they are generated, so the user sees partial output immediately. This improves perceived responsiveness and user experience, especially for long replies.

**If 10,000 users use your application, what engineering problems might appear?**

At scale, you would face rate limiting and higher API costs, need for request caching and connection pooling, efficient handling of concurrent requests, monitoring and alerting for failures, and infrastructure scaling to maintain low latency.

## Stretch goal

**S1 — Handle "context too long" as its own error**

I added a dedicated `ContextLengthError` in `llm.py` that is treated as terminal (never retried) and mapped from API responses containing "context length" or "maximum context". In `main.py`, this error is caught along with other `LLMError`s, which rolls back the unanswered user message so the history stays clean. The main lesson was that not all failures are retryable: context-length overflow is a terminal error that requires the user to reduce input or clear history, and distinguishing it from transient errors makes the application more robust and user-friendly.
