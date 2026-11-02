# Prompt Engineering Report

## Setup
- Tool: Google AI Studio
- Model: gemini-3.8-flash
- Temperature: default (not adjustable for this model in AI Studio)
- Thinking level: Low or Minimal
- Every prompt was run in a fresh chat, one run each.

Full prompts and unedited raw outputs are in these files:
- [test-data.md](test-data.md)
- [zero-shot-output.md](zero-shot-output.md)
- [few-shot-output.md](few-shot-output.md)
- [chain-of-thought-output.md](chain-of-thought-output.md)
- [structured-output-results.md](structured-output-results.md)

---

## 1. Zero-shot vs few-shot (sentiment classification)

Task: classify 8 reviews as positive / negative / neutral / mixed, including sarcasm (#5) and a negative-to-positive trap (#8).

| | Zero-shot | Few-shot (4 examples) |
|---|---|---|
| Accuracy | 8/8 | 8/8 |
| Output format | Capitalized labels, extra "(sarcastic)" on #5 | Exactly as instructed: lowercase, label only |

**Analysis**
- Accuracy did not improve. The task is easy for a strong model, so zero-shot was already enough.
- Few-shot improved format consistency. The examples and the output-format line removed the extra "(sarcastic)" note.
- Where it didn't help: correctness. Few-shot is most useful when you need predictable output for parsing.

---

## 2. Chain-of-thought (multi-step reasoning)

Task: two multi-step word problems (discount + coupon + GST + split + delivery fee; train catch-up).

| | Direct prompt | Step-by-step prompt |
|---|---|---|
| Problem 1 (₹679) | Correct | Correct |
| Problem 2 (11:10 AM) | Correct | Correct |
| Output | Final answers only | Full working shown |

**Analysis**
- Accuracy did not improve: both prompts got 2/2.
- Likely reason: Gemini 3.8 Flash has built-in thinking, so it reasons internally even when told to answer only.
- What CoT added was transparency. Each step can be checked.
- Where it didn't help: accuracy on these problems. "Think step by step" matters less on modern reasoning models.

---

## 3. Structured output (JSON extraction)

Task: extract `{name, date, amount}` from 6 messy texts (mixed currencies, date formats, number words, one missing date).

| Version | Technique | Valid JSON | Dates correct | Amounts as numbers | Full match |
|---|---|---|---|---|---|
| v1 | Plain instruction | 6/6 | 2/6 | 0/6 | 0/6 |
| v2 | Explicit schema + rules + example | 6/6 | 6/6 | 6/6 | 6/6 |
| v3 | Structured outputs feature only | 6/6 | 1/6 | 6/6 | 1/6 |
| v4 | Structured outputs + rules + example | 6/6 | 6/6 | 6/6 | 6/6 |

**Analysis**
- v1: valid JSON, but values were copied as written. "Give JSON" gives syntax, not a schema.
- v2: explicit keys, format rules, a DD/MM/YYYY hint and one example output fixed every field.
- v3: the schema guaranteed structure and types (amounts became numbers), but not format. "3rd March 2026" is a valid string. The editor could not mark `date` as nullable, so the model wrote "no date mentioned" for the missing date.
- v4: combining the schema with prompt rules was the most robust. v2 matched it on this sample.
- Failures documented honestly: v1 and v3 were the failed runs, and v4 uses "" instead of null because of the editor limitation.

---

## Overall conclusions
- Few-shot examples improve format consistency more than accuracy on easy tasks.
- Chain-of-thought gave no accuracy gain on a model with built-in thinking, but it makes reasoning visible.
- For structured output, use both: an enforced schema for structure and types, and prompt rules plus an example for format and meaning.
- Limits: small samples (8, 2 and 6 inputs), one run each, default temperature.
