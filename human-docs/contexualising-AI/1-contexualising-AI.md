# Guide: Contextualising your AI friend

## Contextualising saves money

- Rule of thumb: output token 4X more expensive then input token

## Garbage in, garbage out

### Be concise

- It's nice to be polite. It's nicer to be direct.
- gibberish dilutes attention

### Speak English

- Discrimitory rule. But most public AI models are trained with English as the primary language.

### Be consistent with abbreviations

- create an abbreviation glossary in system prompt:

```
ABBREVIATION GLOSSARY (authoritative)
tok = token
ctx = context window
emb = embedding vector
RAG = retrieval-augmented generation
tel = terminological encoding layer
enc = .enc semantic file format
```

- Authors do this for humans. You do this for your AI friend as well. Common decency to avoid confusion.

### Signpost it

- rambling confuses AI just like humans
  - and yourself too
- don't leave AI to guess what do you want
  - or you will be disappointed

### AIs don't like abstraction

- `Follow best practices`>`If ruff linting errors, must fix`
- `Use good structure`> `Use helper functions in autish/utils.py whenver possible. Create new, reusable helper functions if necessary.`

### Show, don't tell

- Describe patterns > Reference actual files 
- show postive(+) and negative (-) examples

### Respect your AI friend

- rules with "Why" are followed better
  - explanation allow AI to infer and adapt to new situations
```
- **No `print()` in production**
  Why: Performance impact, potential information leakage
  Instead: Use proper logging library with levels
```

### Iterate and cooperate

- add rules when your AI friend make repeated mistakes 

### Use AI, but don't stop to think

- must test and review before releasing into production
  - security critical: manual review + penetration testing
  - algorithms logic: unit test + performance benchmarks
  - business logic: integration tests + stakeholder feedback
  - boiler plate: quick common-sense review + automated tests

```
Low Trust                                          High Trust
├─────────────┼─────────────┼─────────────┼─────────────┤
Security      Algorithms    Business       Boilerplate
Critical      Logic         Code

<─── More Review Required        Less Review Required ───>
```

## holistic approach

### Multiplex contextualising

- LLM models receive much more than user prompt

### system prompt

- role
- rules
- constraints

### user input

- what you asked AI to do. Remember, garbage in, garbage out.

### conversation history

### MCP

#### MCP Knowledge retrieval

- vector DB
- search 
- codebase
  -  RAG (vector search) injection for large codebases

#### MCP Tool descriptions

- available tools for AI agent

```
tool: read_file
params: { path: string }
returns: file contents
```

#### Task metadata

- client-side orchestration layer for MCP tools: AI's `todo.txt`
- help AI agent keep track what tools to use and when
