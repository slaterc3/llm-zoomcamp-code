## LLM Zoomcamp 2026 Homework: Monitoring
## Christopher Guy Slater
## 2026-07-20

## Q1. First trace

Wrap the `rag()` method so each call produces a span. The simplest way
is to create a `RAGTraced` subclass of `RAGBase` that wraps `rag()`,
`search()`, and `llm()` each in their own span.

Run this query:

> How does the agentic loop keep calling the model until it stops?


* 1
* ✅ 3 ✅
* 5
* 7

```bash
Running query...
{ 
    "name": "search", # <---- #1
    "context": {
        "trace_id": "0x827d6aa80e71ab1e9b6bd95a195d26ff",
        "span_id": "0xa74da6fcfc53fd18",
        "trace_state": "[]"
    },
    "kind": "SpanKind.INTERNAL",
    "parent_id": "0xe080d6fa24c776fc",
    "start_time": "2026-07-20T18:43:32.476563Z",
    "end_time": "2026-07-20T18:43:32.477182Z",
    "status": {
        "status_code": "UNSET"
    },
    "attributes": {},
    "events": [],
    "links": [],
    "resource": {
        "attributes": {
            "telemetry.sdk.language": "python",
            "telemetry.sdk.name": "opentelemetry",
            "telemetry.sdk.version": "1.44.0",
            "service.instance.id": "9d1d2380-8adc-47d3-a753-17e6c3747dec",
            "service.name": "unknown_service"
        },
        "schema_url": ""
    }
}
{ 
    "name": "llm", # <---- #2
    "context": {
        "trace_id": "0x827d6aa80e71ab1e9b6bd95a195d26ff",
        "span_id": "0x38ef47daad2c50e5",
        "trace_state": "[]"
    },
    "kind": "SpanKind.INTERNAL",
    "parent_id": "0xe080d6fa24c776fc",
    "start_time": "2026-07-20T18:43:32.477321Z",
    "end_time": "2026-07-20T18:43:35.180556Z",
    "status": {
        "status_code": "UNSET"
    },
    "attributes": {},
    "events": [],
    "links": [],
    "resource": {
        "attributes": {
            "telemetry.sdk.language": "python",
            "telemetry.sdk.name": "opentelemetry",
            "telemetry.sdk.version": "1.44.0",
            "service.instance.id": "9d1d2380-8adc-47d3-a753-17e6c3747dec",
            "service.name": "unknown_service"
        },
        "schema_url": ""
    }
}
{
    "name": "rag", # <---- #3
    "context": {
        "trace_id": "0x827d6aa80e71ab1e9b6bd95a195d26ff",
        "span_id": "0xe080d6fa24c776fc",
        "trace_state": "[]"
    },
    "kind": "SpanKind.INTERNAL",
    "parent_id": null,
    "start_time": "2026-07-20T18:43:32.476536Z",
    "end_time": "2026-07-20T18:43:35.180778Z",
    "status": {
        "status_code": "UNSET"
    },
    "attributes": {},
    "events": [],
    "links": [],
    "resource": {
        "attributes": {
            "telemetry.sdk.language": "python",
            "telemetry.sdk.name": "opentelemetry",
            "telemetry.sdk.version": "1.44.0",
            "service.instance.id": "9d1d2380-8adc-47d3-a753-17e6c3747dec",
            "service.name": "unknown_service"
        },
        "schema_url": ""
    }
}
```

## Q2. Capturing metrics as span attributes

Read the token usage from the LLM response (the `llm()` method in the
starter already returns the raw response object) and set them as
attributes on the `llm` span:


Now re-run the query. How many input tokens do we see?

* 700
* 7000
* 70000
* 700000

> These numbers vary between runs. Pick the closest option.

## Q3. Span timing

Each span automatically records its duration. Look at the console output
from Q1 and find the durations for the `search` span and the `llm` span.

For a typical query, roughly how long does the LLM call take?

* Under 100ms
* 100-500ms
* 500-2000ms
* Over 2000ms

> The first call can be slower (cold start). Pick the range you see
> most often.

## Q4. Saving traces to SQLite

Right now the spans are printed to the terminal and then gone. We don't
save them.

We want to persist them so we can query them later.

In this homework, we'll use SQLite - it's a more lightweight option than
Postgres, so we don't need to set up any docker containers in this homework.

Our instrumentation is already done, we don't need to change anything there.
But we need to create a custom exporter. Instead of printing the spans,
it will save them to the database.

OTel calls the exporter through the same span processor we already use,
we just swap the destination.

Now we will create a custom exporter that saves each finished span to a
SQLite database. The exporter extends `SpanExporter`. It has the following methods:

- `export` method that receives a list of `ReadableSpan` objects
- `shutdown` and `force_flush` methods

Let's implement it:

```python
import sqlite3
from opentelemetry.sdk.trace.export import SpanExporter, SpanExportResult


class SQLiteSpanExporter(SpanExporter):

    def __init__(self, db_path="traces.db"):
        self.conn = sqlite3.connect(db_path)
        self.conn.execute("""
            CREATE TABLE IF NOT EXISTS spans (
                name TEXT,
                start_time INTEGER,
                end_time INTEGER,
                input_tokens INTEGER,
                output_tokens INTEGER,
                cost REAL
            )
        """)
        self.conn.commit()

    def export(self, spans):
        for span in spans:
            attrs = dict(span.attributes or {})
            self.conn.execute(
                "INSERT INTO spans VALUES (?, ?, ?, ?, ?, ?)",
                (
                    span.name,
                    span.start_time,
                    span.end_time,
                    attrs.get("input_tokens"),
                    attrs.get("output_tokens"),
                    attrs.get("cost"),
                ),
            )
        self.conn.commit()
        return SpanExportResult.SUCCESS

    def shutdown(self):
        self.conn.close()

    def force_flush(self):
        return True
```

Replace the console exporter with this new exporter:

```python
provider.add_span_processor(
    SimpleSpanProcessor(SQLiteSpanExporter("traces.db"))
)
```

Re-run the query from Q1. Which span names appear in the `spans` table?

* Only `rag`
* `rag` and `llm`
* `rag`, `search`, and `llm`
* `search`, `llm`, and `judge`

## Q5. Querying trace data

The traces are now in SQLite. Run one more query through the traced
RAG, then query the database.

The `rag` span wraps everything, so its duration includes both
`search` and `llm`. To see where time actually goes, exclude the
`rag` span and compare the children.

Using SQL (or pandas), compute the total duration for each span name
excluding `rag`. Which span type takes the most total time?

* `search`
* `llm`
* They're all about the same

## Q6. Token stability across runs

Load the SQLite data with pandas. One thing a dashboard can tell you
is how stable your system is. If the same query always produces the
same number of input tokens, the context your RAG retrieves is
consistent. If it varies a lot, something in the search may be
unstable.

Run the same query from Q1 three more times (so you have 4 RAG calls
total in the database). Then compute the input tokens for each `llm`
span.

How much do the input tokens vary across these 4 runs?

* They're identical
* Within 10% of each other
* Within 50% of each other
* They vary more than 50%