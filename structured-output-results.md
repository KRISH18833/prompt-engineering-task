# Structured Output (JSON) Results

Model: gemini-3.8-flash (Google AI Studio)

Expected answers:

| # | name | date | amount |
|---|------|------|--------|
| 1 | Rahul Mehta | 2026-03-03 | 1250.5 |
| 2 | Priya Nair | 2026-07-12 | 480 |
| 3 | Arjun Kumar | 2026-01-21 | 7500 |
| 4 | Meena Iyer | 2026-09-14 | 2500 |
| 5 | Sofia Rossi | no date | 900 |
| 6 | D. Okafor | 2026-08-30 | 15000 |

## Schema (AI Studio Visual Editor, used in v3 and v4)
records: array of objects { name: string, date: string, amount: number }, all required

---

## v1: Plain instruction (Structured outputs OFF)

### Prompt
```
Extract the name, date and amount from each text below and give the result as JSON.

1. Hi, this is Rahul Mehta. I paid the electricity bill of Rs. 1,250.50 on 3rd March 2026, thanks.
2. Invoice from Priya Nair dated 12/07/2026 for a total of $480. Please process soon.
3. Received ₹7,500 from Arjun Kumar on 21 Jan 2026 via UPI.
4. Meena Iyer sent two thousand five hundred rupees on the 14th of September 2026.
5. Payment of 900 USD was made by Sofia Rossi, no date mentioned.
6. ref#4471 -- D. Okafor // 2026-08-30 // amt: 15,000.00 INR (partial)
```

### Raw Output
```
[
  {
    "name": "Rahul Mehta",
    "date": "3rd March 2026",
    "amount": "Rs. 1,250.50"
  },
  {
    "name": "Priya Nair",
    "date": "12/07/2026",
    "amount": "$480"
  },
  {
    "name": "Arjun Kumar",
    "date": "21 Jan 2026",
    "amount": "₹7,500"
  },
  {
    "name": "Meena Iyer",
    "date": "14th of September 2026",
    "amount": "two thousand five hundred rupees"
  },
  {
    "name": "Sofia Rossi",
    "date": null,
    "amount": "900 USD"
  },
  {
    "name": "D. Okafor",
    "date": "2026-08-30",
    "amount": "15,000.00 INR"
  }
]
```

### Score
Valid JSON 6/6, names 6/6, dates in YYYY-MM-DD 2/6, amounts as numbers 0/6, full match 0/6.

### Analysis
The model returned valid JSON and found every value, but copied them as written. Without a defined format it had no reason to normalize. "Give JSON" gets syntax, not a schema.

---

## v2: Explicit schema + rules + example (Structured outputs OFF)

### Prompt
```
Extract the name, date and amount from each text below.

Return ONLY a JSON array, no explanation and no markdown fences. One object per text, in order, with exactly these keys:
- "name": string
- "date": string in YYYY-MM-DD format, or null if no date is given
- "amount": number only (no currency symbol, no commas, no words), or null if missing

Dates like 12/07/2026 are DD/MM/YYYY.

Example input: Ravi Kumar paid Rs. 2,000 on 5th Feb 2026.
Example output: [{"name": "Ravi Kumar", "date": "2026-02-05", "amount": 2000}]

Texts:
1. Hi, this is Rahul Mehta. I paid the electricity bill of Rs. 1,250.50 on 3rd March 2026, thanks.
2. Invoice from Priya Nair dated 12/07/2026 for a total of $480. Please process soon.
3. Received ₹7,500 from Arjun Kumar on 21 Jan 2026 via UPI.
4. Meena Iyer sent two thousand five hundred rupees on the 14th of September 2026.
5. Payment of 900 USD was made by Sofia Rossi, no date mentioned.
6. ref#4471 -- D. Okafor // 2026-08-30 // amt: 15,000.00 INR (partial)
```

### Raw Output
```
[{"name": "Rahul Mehta", "date": "2026-03-03", "amount": 1250.5}, {"name":
"Priya Nair", "date": "2026-07-12", "amount": 480}, {"name": "Arjun Kumar",
"date": "2026-01-21", "amount": 7500}, {"name": "Meena Iyer", "date":
"2026-09-14", "amount": 2500}, {"name": "Sofia Rossi", "date": null,
"amount": 900}, {"name": "D. Okafor", "date": "2026-08-30", "amount": 15000}]
```

### Score
Full match 6/6 (valid JSON, correct dates incl. DD/MM/YYYY, numeric amounts, null for the missing date).

### Analysis
The explicit schema, format rules and example output told the model what "correct" looks like. The DD/MM/YYYY hint fixed the ambiguous date in #2.

---

## v3: Structured outputs ON, short prompt (no rules)

### Prompt
```
Extract the name, date and amount from each text below.

1. Hi, this is Rahul Mehta. I paid the electricity bill of Rs. 1,250.50 on 3rd March 2026, thanks.
2. Invoice from Priya Nair dated 12/07/2026 for a total of $480. Please process soon.
3. Received ₹7,500 from Arjun Kumar on 21 Jan 2026 via UPI.
4. Meena Iyer sent two thousand five hundred rupees on the 14th of September 2026.
5. Payment of 900 USD was made by Sofia Rossi, no date mentioned.
6. ref#4471 -- D. Okafor // 2026-08-30 // amt: 15,000.00 INR (partial)
```

### Raw Output
```
{"records":[{"name":"Rahul Mehta","date":"3rd March 2026","amount":1250.5},{"name":"Priya Nair","date":"12/07/2026","amount":480},{"name":"Arjun Kumar","date":"21 Jan 2026","amount":7500},{"name":"Meena Iyer","date":"14th of September 2026","amount":2500},{"name":"Sofia Rossi","date":"no date mentioned","amount":900},{"name":"D. Okafor","date":"2026-08-30","amount":15000.0}]}
```

### Score
Valid JSON and schema match 6/6, amounts as numbers 6/6, dates in YYYY-MM-DD 1/6, full match 1/6.

### Analysis
The schema guaranteed structure and types (amounts became numbers, even "two thousand five hundred rupees" became 2500). It did not guarantee format: "string" accepts "3rd March 2026". The AI Studio editor could not mark `date` as nullable, so the model invented "no date mentioned" instead of null.

---

## v4: Structured outputs ON + rules + example

### Prompt
```
Extract the name, date and amount from each text below. Put all results in the "records" array, one object per text, in order.

Rules:
- "date": YYYY-MM-DD format. Dates like 12/07/2026 are DD/MM/YYYY. If no date is given, use an empty string "".
- "amount": number only (no currency symbol, no commas, no words).

Example input: Ravi Kumar paid Rs. 2,000 on 5th Feb 2026.
Example output: {"records": [{"name": "Ravi Kumar", "date": "2026-02-05", "amount": 2000}]}

Texts:
1. Hi, this is Rahul Mehta. I paid the electricity bill of Rs. 1,250.50 on 3rd March 2026, thanks.
2. Invoice from Priya Nair dated 12/07/2026 for a total of $480. Please process soon.
3. Received ₹7,500 from Arjun Kumar on 21 Jan 2026 via UPI.
4. Meena Iyer sent two thousand five hundred rupees on the 14th of September 2026.
5. Payment of 900 USD was made by Sofia Rossi, no date mentioned.
6. ref#4471 -- D. Okafor // 2026-08-30 // amt: 15,000.00 INR (partial)
```

### Raw Output
```
{"records":[{"name":"Rahul Mehta","date":"2026-03-03","amount":1250.5},{"name":"Priya Nair","date":"2026-07-12","amount":480},{"name":"Arjun Kumar","date":"2026-01-21","amount":7500},{"name":"Meena Iyer","date":"2026-09-14","amount":2500},{"name":"Sofia Rossi","date":"","amount":900},{"name":"D. Okafor","date":"2026-08-30","amount":15000}]}
```

### Score
Full match 6/6 (missing date returned as "" because the schema could not hold null).

---

## Summary

| Version | Technique | Valid JSON | Dates correct | Amounts as numbers | Full match |
|---|---|---|---|---|---|
| v1 | Plain instruction | 6/6 | 2/6 | 0/6 | 0/6 |
| v2 | Explicit schema + rules + example | 6/6 | 6/6 | 6/6 | 6/6 |
| v3 | Structured outputs only | 6/6 | 1/6 | 6/6 | 1/6 |
| v4 | Structured outputs + rules + example | 6/6 | 6/6 | 6/6 | 6/6 |
