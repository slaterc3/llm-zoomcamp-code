## Christopher Guy Slater
### LLM-Zoomcamp-2026 - dlt workshop
#### July 20, 2026

## Question 1. Instrument the agent with Logfire


how many spans does a single agent run produce?

Each span is either the agent run itself, an LLM call, or a tool call.
The number can vary between runs because the model decides how many
times to search.

* 1
* ✅ 5
* 15
* 30

## Question 2. Load traces into DuckDB with dlt


How many tables did dlt create? Check with:

```sql
SELECT COUNT(*) FROM information_schema.tables 
WHERE table_schema = 'agent_traces';
```

* 1
* 3
* ✅ 24
* 100

## Question 3. Query traces with an agent

Using a coding agent (you can also write the code by hand) find the
input token usage for the agent run from Q1.

The token counts are stored in the span attributes as
`gen_ai.usage.input_tokens`. Sum them across all LLM calls within the
trace. The number depends on how many searches the agent made, so
report the range it falls into:

* 100 - 500
* ✅ 1500 - 5000
* 10000 - 20000
* 50000 - 100000