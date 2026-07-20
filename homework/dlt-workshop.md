## Christopher Guy Slater
### LLM-Zoomcamp-2026 - dlt workshop
#### July 20, 2026

## Question 1. Instrument the agent with Logfire

Sign up for a free [Logfire](https://logfire.dev) account, create a
project, and generate a write token. Put it in `.env` as
`LOGFIRE_TOKEN`.

Instrument the agent:

```python
logfire.configure()
logfire.instrument_pydantic_ai()
```

Run the agent a few times with different questions and open your
project on Logfire to see the traces.

For the following query

> How do I run Ollama locally?

how many spans does a single agent run produce?

Each span is either the agent run itself, an LLM call, or a tool call.
The number can vary between runs because the model decides how many
times to search.

* 1
* 5
* 15
* 30

## Question 2. Load traces into DuckDB with dlt

Generate a read token for your Logfire project and set it as
`LOGFIRE_READ_TOKEN` in `.env`.

Initialize a dlt-hub project like in the workshop. Then ask your coding
agent to pull the data from Pydantic Logfire and save it into DuckDB.

The dltHub AI workbench has a ready-made context for Logfire. Point your
agent to it: https://dlthub.com/context/source/logfire

If you don't currently use a coding agent, you can use something like OpenCode:
you should be able to complete one session with the free account.

Alternatively, you can do it in the old way (using ChatGPT or your favorite search engine).

If you don't currently use a coding agent, you can use something like OpenCode:
you should be able to complete one session with the free account. 

Alternatively, you can do it in the old way (using ChatGPT or your favorite search engine).

The logfire traces contain deeply nested JSON (span attributes with
LLM messages, tool calls, token usage, etc.). dlt automatically
normalizes this into a set of tables - one for the main records, plus
child tables for each nested level.

How many tables did dlt create? Check with:

```sql
SELECT COUNT(*) FROM information_schema.tables 
WHERE table_schema = 'agent_traces';
```

* 1
* 3
* 24
* 100

## Question 3. Query traces with an agent

Using a coding agent (you can also write the code by hand) find the
input token usage for the agent run from Q1.

The token counts are stored in the span attributes as
`gen_ai.usage.input_tokens`. Sum them across all LLM calls within the
trace. The number depends on how many searches the agent made, so
report the range it falls into:

* 100 - 500
* 1500 - 5000
* 10000 - 20000
* 50000 - 100000