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

We evaluated **GaV** on a benchmark of real-world datasets, comparing different LLM backbones and architectural configurations. The table below reports the average performance metrics extracted from our experiments.

### Ablation Study & Performance Metrics

| Configuration | Model | Accuracy | Input Tokens (Avg) | Output Tokens (Avg) | Time (s) |
| :--- | :--- | :---: | :---: | :---: | :---: |
| **Vanilla** (Direct Prompt) | gpt-5 | 59.0% | 32k | 9.8k | 43s |
| | gpt-5-mini | 55.0% | 32k | 5.3k | 16s |
| **Description Only** (No Verifier) | gpt-5 | 77.0% | 85k | 40k | 168s |
| **Verifier Only** (No Description) | gpt-5 | 81.0% | 117k | 135k | 583s |
| **GaV (Ours)** | **gpt-5** | **87.0%** | 179k | 164k | 509s |
| | **gpt-5-mini** | **82.0%** | 203k | 96k | 306s |

> **Note:** Metrics are averaged across the benchmark datasets. Time represents the end-to-end processing duration per dataset.

### 📝 Key Findings

The results highlight three critical insights regarding the **GaV** architecture:

1.  **Necessity of the Full Loop:** The **Vanilla** approach fails to reliably capture column semantics (~59% accuracy), proving that simple prompting is insufficient for complex data understanding tasks.
2.  **Efficiency of the "Warm-Start":**
    * The **Verifier Only** configuration achieves good accuracy (81%) but is slower (583s) due to the lack of context, which forces the agent into costly refinement loops.
    * **GaV (Ours)** uses the Description component to "warm-start" the verification. While this increases the input context (~179k tokens), it actually **reduces the total execution time** to 509s (-13% compared to Verifier Only) and boosts accuracy to **87%**, as the Verifier starts with a stronger hypothesis.
3.  **Cost-Effective Scalability:** The **gpt-5-mini** model with the GaV architecture achieves **82% accuracy**, outperforming the larger GPT-5 model in the "Description Only" setup (77%). This demonstrates that our neuro-symbolic architecture enables smaller, cheaper models to perform competitively on complex industrial tasks.

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


