# Creating custom AI agent

### 1. Collect and structure your style corpus

#### 1.1. Gather your own texts

Put your real writing in `data/raw/`:

```bash
data/
  raw/
    email_01.txt
    essay_01.txt
    notes_01.txt
    gh_issue_01.txt
```

Keep them:

- **Same language/register:** e.g. concise, technical, explanatory.
- **Long enough:** ideally > 50–80 words each.

#### 1.2. Clean and merge

```python
# scripts/build_corpus.py
from pathlib import Path
import re

raw_dir = Path("data/raw")
out_path = Path("data/clean_corpus.txt")

def clean(text: str) -> str:
    text = text.replace("\r\n", "\n")
    text = re.sub(r'[ \t]+\n', '\n', text)
    text = re.sub(r'\n{3,}', '\n\n', text)
    return text.strip()

docs = []
for p in raw_dir.glob("*.txt"):
    t = clean(p.read_text(encoding="utf-8"))
    if len(t.split()) >= 40:
        docs.append(t)

out_path.write_text("\n\n---DOC---\n\n".join(docs), encoding="utf-8")
print(f"{len(docs)} docs -> {out_path}")
```

Run:

```bash
python scripts/build_corpus.py
```

---

### 2. Explicitly define your style

Create `style_spec.txt`:

```text
You are an assistant that writes exactly like R.

Style:
- Ultra-concise, high information density.
- Hierarchical structure with headings and short sections.
- Uses bullet points and short paragraphs.
- Avoids repetition and filler.
- Neutral, precise tone; no emotional language.
- Uses technical vocabulary but explains briefly when needed.
```

Optional: add 2–3 real Q/A examples in your style at the end.

---

### 3. Baseline: style via prompting (no training yet)

This is your “control” to compare against fine-tuning.

```python
# scripts/baseline_prompt.py
import os, requests

HF_TOKEN = os.environ["HF_TOKEN"]
MODEL = "mistralai/Mistral-7B-Instruct-v0.3"  # or any instruct model
API_URL = f"https://api-inference.huggingface.co/models/{MODEL}"
headers = {"Authorization": f"Bearer {HF_TOKEN}"}

style_spec = Path("style_spec.txt").read_text(encoding="utf-8")

def ask(prompt: str) -> str:
    payload = {
        "inputs": [
            {"role": "system", "content": style_spec},
            {"role": "user", "content": prompt},
        ],
        "parameters": {
            "max_new_tokens": 400,
            "temperature": 0.4,
            "top_p": 0.9,
        },
    }
    r = requests.post(API_URL, headers=headers, json=payload)
    r.raise_for_status()
    data = r.json()
    return data[0]["generated_text"][-1]["content"]

if __name__ == "__main__":
    print(ask("Explain Docker to a CS undergrad in my style."))
```

This will already be “somewhat you”, but not consistent. Next step: train.

---

### 4. Turn your corpus into a supervised dataset

We’ll create instruction-style examples: “neutral instruction + input → your-style output”.

#### 4.1. Split corpus into documents

```python
# scripts/make_dataset.py
from pathlib import Path
import json

corpus = Path("data/clean_corpus.txt").read_text(encoding="utf-8")
docs = [d.strip() for d in corpus.split("\n\n---DOC---\n\n") if d.strip()]

records = []
for i, doc in enumerate(docs):
    # Simple generic instruction; can refine later
    records.append({
        "instruction": "Rewrite the following content in R's style.",
        "input": doc,
        "output": doc,  # your style is the target
    })

out_path = Path("data/style_dataset.jsonl")
out_path.write_text(
    "\n".join(json.dumps(r, ensure_ascii=False) for r in records),
    encoding="utf-8",
)
print(f"{len(records)} examples -> {out_path}")
```

Run:

```bash
python scripts/make_dataset.py
```

---

### 5. LoRA fine-tuning on Linux Mint

Assume you have at least some GPU access (local or remote). If VRAM is small, pick a smaller base model (e.g. `microsoft/Phi-3-mini-4k-instruct`).

#### 5.1. Environment setup

```bash
python -m venv .venv
source .venv/bin/activate

pip install "transformers>=4.40" "datasets" "accelerate" "peft" "bitsandbytes" "trl"
```

#### 5.2. Training script

```python
# scripts/train_lora.py
from datasets import load_dataset
from transformers import AutoTokenizer, AutoModelForCausalLM, TrainingArguments, Trainer
from peft import LoraConfig, get_peft_model

MODEL_NAME = "mistralai/Mistral-7B-Instruct-v0.3"  # or smaller
DATA_PATH = "data/style_dataset.jsonl"
OUTPUT_DIR = "models/r-style-mistral-lora"

tokenizer = AutoTokenizer.from_pretrained(MODEL_NAME)
if tokenizer.pad_token is None:
    tokenizer.pad_token = tokenizer.eos_token

def format_example(ex):
    sys = "You are an assistant that writes exactly like R.\n"
    user = f"Instruction: {ex['instruction']}\nInput:\n{ex['input']}\n"
    assistant = ex["output"]
    # Adjust to your model’s chat format; this works for many instruct models
    return f"<s>[INST] {sys}{user} [/INST]\n{assistant}</s>"

dataset = load_dataset("json", data_files=DATA_PATH, split="train")

def tokenize_fn(ex):
    text = format_example(ex)
    tokens = tokenizer(
        text,
        truncation=True,
        max_length=1024,
        padding="max_length",
    )
    tokens["labels"] = tokens["input_ids"].copy()
    return tokens

tokenized = dataset.map(tokenize_fn, remove_columns=dataset.column_names)

model = AutoModelForCausalLM.from_pretrained(
    MODEL_NAME,
    load_in_4bit=True,
    device_map="auto",
)

lora_config = LoraConfig(
    r=16,
    lora_alpha=32,
    lora_dropout=0.05,
    bias="none",
    task_type="CAUSAL_LM",
)

model = get_peft_model(model, lora_config)
model.print_trainable_parameters()

args = TrainingArguments(
    output_dir=OUTPUT_DIR,
    per_device_train_batch_size=2,
    gradient_accumulation_steps=8,
    num_train_epochs=3,
    learning_rate=2e-4,
    fp16=True,
    logging_steps=10,
    save_steps=200,
    save_total_limit=2,
    warmup_ratio=0.03,
    weight_decay=0.01,
    report_to="none",
)

trainer = Trainer(
    model=model,
    args=args,
    train_dataset=tokenized,
)

trainer.train()
trainer.save_model(OUTPUT_DIR)
tokenizer.save_pretrained(OUTPUT_DIR)
```

Run:

```bash
python scripts/train_lora.py
```

Now you have a local LoRA-adapted model.

---

### 6. Turn it into a “personal writing agent”

You can use it locally or push to HF and call via API. Let’s do both.

#### 6.1. Local agent wrapper

```python
# scripts/agent_local.py
from transformers import AutoTokenizer, AutoModelForCausalLM, TextStreamer

MODEL_DIR = "models/r-style-mistral-lora"

tokenizer = AutoTokenizer.from_pretrained(MODEL_DIR)
model = AutoModelForCausalLM.from_pretrained(MODEL_DIR, device_map="auto")

def build_prompt(user_prompt: str) -> str:
    sys = "You are R. Always answer in R's style: concise, structured, high information density.\n"
    return f"<s>[INST] {sys}{user_prompt} [/INST]"

def generate_r_style(prompt: str, max_new_tokens: int = 400) -> str:
    text = build_prompt(prompt)
    inputs = tokenizer(text, return_tensors="pt").to(model.device)
    out = model.generate(
        **inputs,
        max_new_tokens=max_new_tokens,
        temperature=0.4,
        top_p=0.9,
    )
    return tokenizer.decode(out[0], skip_special_tokens=True)

if __name__ == "__main__":
    while True:
        try:
            q = input("You: ")
        except EOFError:
            break
        ans = generate_r_style(q)
        print("\nR-agent:\n", ans, "\n")
```

This is already a “personal agent”: you type, it answers in your style.

#### 6.2. Push to Hugging Face and use via API

From the model directory:

```bash
huggingface-cli login  # once

huggingface-cli repo create r-style-mistral-lora --private
huggingface-cli upload models/r-style-mistral-lora/ \
  --repo r-style-mistral-lora \
  --repo-type model
```

Then:

```python
# scripts/agent_api.py
import os, requests

HF_TOKEN = os.environ["HF_TOKEN"]
MODEL_ID = "your-username/r-style-mistral-lora"
API_URL = f"https://api-inference.huggingface.co/models/{MODEL_ID}"
headers = {"Authorization": f"Bearer {HF_TOKEN}"}

def r_agent(prompt: str) -> str:
    payload = {
        "inputs": [
            {"role": "system", "content": "You are R. Answer strictly in R's style."},
            {"role": "user", "content": prompt},
        ],
        "parameters": {
            "max_new_tokens": 400,
            "temperature": 0.4,
        },
    }
    r = requests.post(API_URL, headers=headers, json=payload)
    r.raise_for_status()
    data = r.json()
    return data[0]["generated_text"][-1]["content"]

if __name__ == "__main__":
    print(r_agent("Draft a short email declining a meeting but proposing an async alternative."))
```

Now you have a **remote, reusable agent endpoint**.

---

### 7. Make it *consistently* your style (feedback loop)

1. **Create a test set**

   ```python
   test_prompts = [
       "Explain monads to a practical programmer.",
       "Summarize a 10-page paper in my style.",
       "Write a bug report for a failing API endpoint.",
       "Draft a polite but firm email about a missed deadline.",
   ]
   ```

2. **Compare outputs**

   For each prompt, collect:

   - Baseline (prompt-only) output.
   - Fine-tuned agent output.

3. **Correct deviations**

   When the agent is off (too verbose, wrong tone), write your own corrected version and save:

   ```json
   {"instruction": "Answer the following in R's style.",
    "input": "Draft a polite but firm email about a missed deadline.",
    "output": "YOUR CORRECTED TEXT"}
   ```

   Append to `data/style_dataset.jsonl`.

4. **Re-train briefly**

   Re-run `train_lora.py` with 1–2 epochs. Each loop tightens the style.

---

If you tell me your actual GPU (VRAM, local vs remote), I can give you a concrete base-model choice + adjusted hyperparams that will definitely fit your setup.
