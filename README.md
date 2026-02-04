# ABCU Benchmark — Aspect-Based Column Understanding

**ABCU (Aspect-Based Column Understanding)** is a benchmark created for the homonymous paper introducing this novel task.  
ABCU focuses on the **semantic interpretation of table attributes**, aiming to uncover what each column represents in the real world and how its values are expressed, aggregated, or related to others.

---

## 🧩 Overview

Unlike traditional column-type annotation tasks, ABCU provides **fine-grained semantic statements** for each attribute, describing its meaning across multiple conceptual facets.  
All annotations have been **produced and reviewed by human annotators**, ensuring semantic precision and interpretability.

The benchmark allows researchers to evaluate and develop systems capable of generating or verifying human-understandable statements about table attributes — a core step for data understanding and schema-level AI reasoning.

---

## 📂 Repository Structure

```
Column Meaning Ontology.pdf        # definitions and examples of the 7 ontology types
v2_final_attributes_json.json              # human-annotated benchmark data
```

---

## 🏙️ Data Source

All datasets used in ABCU are derived from the **NYC Open Data** portal:  
👉 https://opendata.cityofnewyork.us/  

A curated subset of heterogeneous municipal datasets was selected to ensure domain diversity, schema complexity, and richness of attribute semantics. 
The subset of ABCU can be found here:
👉 https://drive.google.com/drive/folders/1emdGsmkhdfj8Da3ozuYLvMQpDbfgamAD?usp=sharing

---

## 📘 Ontology Facets

Each attribute is annotated according to one of **seven ontology facets**, each describing a different semantic aspect:

1. **Entity or Attribute Meaning** – what real-world entity, event, or property the column represents.  
2. **Value Representation** – the syntactic or format conventions of values (e.g., date pattern, measurement unit).  
3. **Value Semantics** – the meaning behind categorical or numeric codes.  
4. **Row Context** – how the attribute value relates to the row’s granularity or entity.  
5. **Aggregation and Derivation** – whether the value is computed or aggregated from others.  
6. **Relational Context** – structural or semantic links between attributes (e.g., foreign keys, dependencies).  
7. **Temporal Scope** – the time instant, duration, or validity period represented by the value.

All facets, along with examples and annotation guidelines, are detailed in `Column Meaning Ontology.pdf`.

---

## 🧾 Annotation Format

The benchmark annotations are stored in a single JSON file, `v2_final_attributes_json.json`, following this structure:

```json
[
  {
    "dataset_name": "For_Hire_Vehicles__FHV__-_Active_20251003",
    "dataset_description": "This dataset, which includes all TLC licensed for-hire vehicles which are in good standing and able to drive, is updated every day in the evening between 4-7pm. Please check the 'Last Update Date' field to make sure the list has updated successfully. 'Last Update Date' should show either today or yesterday's date, depending on the time of day.",
    "labels": [
      [
        "Active",
        "Value Semantics",
        "Active is a categorical (boolean-like) status flag set by the TLC indicating regulatory operational status: ‘YES’ means the vehicle’s for-hire license is current, in good standing, and the vehicle is authorized to operate; by design this dataset contains only ‘YES’ values (no nulls), and while not present here, a ‘NO’ in other contexts would indicate the vehicle is not currently authorized (e.g., expired, suspended, or otherwise inactive)."
      ],
      ...
      [
        "Active",
        "Temporal Scope",
        "Active is a categorical status flag (here always “YES”) indicating that, at the moment of the dataset snapshot—anchored by the row’s Last Date Updated/Last Time Updated—the vehicle is currently licensed and in good standing to operate; it is not a timestamp or duration but a point-in-time operational status that should be interpreted alongside regulatory dates such as Expiration Date and may change in subsequent daily updates."
      ]]
  }
]
```

Each record includes:
- the dataset name and short description,
- a list of annotation triples (attribute, ontology type, statement).

---

## ✍️ Human Annotation Process

All statements were produced through a **manual annotation workflow** by trained annotators.  
Annotators analyzed:
- the schema and metadata of the dataset,
- column statistics (e.g., distribution, frequency, data type),
- and representative row samples.

Each annotation expresses a **verifiable hypothesis** about the attribute’s semantics, grounded in the actual dataset content.  
All statements underwent review for **semantic correctness, clarity, and grounding**.

---

## 📊 Experimental Results

We evaluated **GaV** on an industrial benchmark of real-world datasets from Snowflake source, comparing different LLM backbones and architectural configurations. The table below reports the average performance metrics extracted from our experiments.

### 🤖 GPT Family Performance

| Configuration | Model | Accuracy | Input Tokens | Output Tokens | Time |
|:---|:---|:---:|:---:|:---:|:---:|
| **Vanilla** (Direct Prompt) | gpt-5 | 59.0% | 32k | 9k | 42s |
| | gpt-5-mini | 55.0% | 32k | 5k | 16s |
| | gpt-5-nano | 52.0% | 32k | 12k | 22s |
| **Description Only** (No Verifier) | gpt-5 | 77.0% | 85k | 39k | 167s |
| | gpt-5-mini | 75.0% | 92k | 24k | 67s |
| | gpt-5-nano | 65.0% | 90k | 51k | 78s |
| **Verifier Only** (No Description) | gpt-5 | 81.0% | 117k | 135k | 583s |
| | gpt-5-mini | 76.0% | 140k | 74k | 305s |
| | gpt-5-nano | 66.0% | 132k | 176k | 415s |
| **GaV (Ours)** | **gpt-5** | **87.0%** | **178k** | **163k** | **508s** |
| | **gpt-5-mini** | **82.0%** | **202k** | **95k** | **305s** |
| | **gpt-5-nano** | **72.0%** | **201k** | **221k** | **399s** |

### 🧠 Qwen Family Performance

| Configuration | Model | Accuracy | Input Tokens | Output Tokens | Time |
|:---|:---|:---:|:---:|:---:|:---:|
| **Vanilla** (Direct Prompt) | qwen-plus | 42.0% | 36k | 1k | 5s |
| | qwen-plus (thinking) | 53.0% | 46k | 13k | 51s |
| | qwen3-max | 43.0% | 36k | 0k | 7s |
| **Description Only** (No Verifier) | qwen-plus | 56.0% | 69k | 3k | 20s |
| | qwen-plus (thinking) | 68.0% | 96k | 48k | 173s |
| | qwen3-max | 59.0% | 67k | 3k | 34s |
| **Verifier Only** (No Description) | qwen-plus | 57.0% | 135k | 34k | 101s |
| | qwen-plus (thinking) | 71.0% | 118k | 173k | 531s |
| | qwen3-max | 59.0% | 122k | 25k | 151s |
| **GaV (Ours)** | **qwen-plus** | **61.0%** | **168k** | **36k** | **126s** |
| | **qwen-plus (thinking)**| **75.0%** | **169k** | **196k** | **522s** |
| | **qwen3-max** | **63.0%** | **153k** | **26k** | **209s** |

> **Note:** Metrics are averaged across the benchmark datasets. Time represents the end-to-end processing duration per dataset.

### 📝 Key Findings

The results highlight three critical insights regarding the **GaV** architecture:

1.  **Necessity of the Full Loop:** The **Vanilla** approach (standard LLM prompting) fails to capture column semantics reliably (~59% accuracy with GPT-5), proving that column understanding requires iterative reasoning and evidence gathering.
2.  **Efficiency of the "Warm-Start":**
    * The **Verifier Only** configuration is accurate but slow (e.g., 583s for GPT-5) because the lack of initial context forces the agent into costly refinement loops.
    * **GaV (Ours)** uses the Description component to "warm-start" the verification. While this increases the input context, it significantly **reduces the total execution time** (e.g., down to 508s for GPT-5) and boosts accuracy to **87%**, as the Verifier starts with a stronger hypothesis.
3.  **Cost-Effective Scalability:** Remarkably, **GaV with gpt-5-mini** achieves **82.0% accuracy**, outperforming the larger GPT-5 model in the "Description Only" setup (77%). This demonstrates that our agentic architecture enables smaller, cheaper models to perform effectively on complex industrial tasks.

---

## 🎯 Intended Use

ABCU is intended for evaluating and developing systems that perform:
- column-level meaning discovery,
- ontology-based reasoning on tabular data,
- LLM-based hypothesis generation and validation (e.g., multi-agent reasoning systems).

It supports research in:
- data semantics and table understanding,  
- schema mapping and ontology alignment,  
- explainable AI for data profiling and cataloging.


