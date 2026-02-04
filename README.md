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
| **Vanilla** (Direct Prompt) | qwen-plus | 42.0% | 36k | 2k | 5s |
| | qwen-plus (thinking) | 53.0% | 46k | 13k | 51s |
| | qwen3-max | 43.0% | 36k | 1k | 7s |
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

### 📝 Deep Dive & Key Findings

Our ablation study reveals critical insights into the interplay between Large Language Model reasoning and neuro-symbolic execution. By isolating the **Description** and **Verification** components, we observed four distinct behavioral patterns:

#### 1. The Limitations of Direct Reasoning (Vanilla Baseline)
* **Configuration:** *No Description, No Verifier (Direct Prompting)*
* **Observation:** This baseline relies purely on semantic inference from column names and headers. While it is the most efficient in terms of resources (fastest time, lowest token usage), it yields the lowest accuracy (**~59%** with GPT-5).
* **Technical Insight:** The massive gap in accuracy compared to GaV (**-28%**) demonstrates that LLMs cannot reliably infer column semantics without grounding in actual data statistics. The lack of a feedback loop results in "semantic hallucinations," where the model confidentially asserts incorrect meanings based on superficial naming patterns.

#### 2. The Hallucination Problem (Description Only)
* **Configuration:** *Description ON, Verifier OFF*
* **Observation:** Injecting statistical summaries (distributions, min/max values) significantly improves accuracy (**77%**) compared to the baseline.
* **Technical Insight:** While the "Description" component provides necessary context, the absence of an execution-based verifier means the system cannot confirm its intuitions. The model creates a coherent narrative around the data but lacks the mechanism to perform **empirical validation**, leading to errors in ambiguous cases (e.g., distinguishing between *Created Date* vs *Updated Date* without checking value monotonicity).

#### 3. The Cost of "Cold-Start" Verification (Verifier Only)
* **Configuration:** *Description OFF, Verifier ON*
* **Observation:** This configuration provides a massive accuracy boost (**81%**) but at the highest computational cost in terms of time (**583s**) and output tokens (**135k**).
* **Technical Insight:** Without a preliminary description, the Verifier operates in a "Cold-Start" mode. The initial hypotheses are often weak or generic. Our log analysis reveals that this forces the **Data Analyst** agent into multiple costly **Refinement Loops**: the Verifier rejects the initial guess, requests new code execution, and iterates. This "trial-and-error" process explains the high output token consumption and extended execution time.

#### 4. The "Warm-Start" Synergy (Ours - Full Architecture)
* **Configuration:** *Description ON, Verifier ON*
* **Observation:** Activating both components achieves the state-of-the-art accuracy (**87%**) while actually *reducing* execution time compared to the "Verifier Only" setup (~509s vs 583s for GPT-5).
* **Technical Insight:** This result highlights a critical efficiency trade-off. Although the Description component increases the input context overhead (**~178k input tokens**), it acts as a **"Warm-Start" mechanism**. It primes the Generator with a high-quality context, leading to a much stronger initial hypothesis. Consequently, the Verifier accepts the hypothesis with significantly fewer refinement steps and fewer calls to the Data Analyst code interpreter. The initial investment in input tokens pays off by preventing expensive iterative corrections.

### 💡 Conclusion: Architectural Robustness
The full GaV architecture offers the optimal balance between precision and computational overhead.

Crucially, this architectural robustness **democratizes high-end performance**. As shown in the results, the medium-sized **GPT-5-mini** model using GaV achieves **82% accuracy**, outperforming the larger GPT-5 model in the "Description Only" (77%) and "Vanilla" (59%) configurations. This proves that a well-designed **Agentic Workflow** (Generate $\rightarrow$ Verify $\rightarrow$ Refine) can compensate for the lower raw reasoning capabilities of smaller, more cost-effective models.

---

### 🔎 Qualitative Analysis: A Real-World Example

To illustrate the specific contribution of each component, we present a comparison on the **FDIC Branch Locations** dataset.

* **Target Column:** `FDIC_INSTITUTION_CERTIFICATE_NUMBER`
* **Ground Truth (Fact):** A unique certification identifier assigned by the FDIC. Verified to map 1:1 to `FDIC_INSTITUTION_ID`, serving as an alternative unique key for the institution entity.

| Configuration | Prediction / Insight | Analysis of the Result |
| :--- | :--- | :--- |
| **Vanilla**<br>*(LLM Only)* | *"A unique numeric identifier assigned to each row. It serves as the Primary Key for the branch records."* | ❌ **Semantic Hallucination.** The model relies solely on the column name ("Number/ID"). It blindly assumes it is a unique key for the table, failing to recognize that multiple branches can share the same bank certificate. |
| **Description Only**<br>*(Summary Stats)* | *"Numeric column ranging from 58 to 99,999. It contains non-unique integer codes representing the certificate."* | ⚠️ **Superficial.** The description is statistically accurate (it notes the range and type) but lacks relational depth. It fails to identify the *functional dependency* (i.e., that this number identifies the *Bank*, not the *Branch*). |
| **Verifier Only**<br>*(Code Execution)* | *"Data Analysis: Values are NOT unique. There are 78,324 rows but only 4,345 distinct values. The column cannot be used as a Primary Key."* | ⚠️ **Context-Free.** The agent correctly executes code to find duplicates, but interprets this as a negative result ("Not a Key"). Without the semantic "warm-start," it misses that the duplicates are a feature (grouping key) rather than a bug. |
| **GaV (Ours)**<br>*(Full Architecture)* | *"**Institution-level identifier.** It functions as the Institution Key, identifying the chartered bank that owns the branch.<br><br>**Observed behavior:**<br>• Many branches share the same certificate (4,345 certs vs 78,324 branches), consistent with an institution-level key.<br>• **Maps 1:1 to `FDIC_INSTITUTION_ID`** and `INSTITUTION_NAME`.<br>• Use to group/aggregate branch rows by institution."* | ✅ **Perfect Understanding.** GaV combines the semantic hint ("Certificate" $\approx$ Bank License) with deep data verification. It explicitly discovers the **1:1 mapping** with the Institution ID, correctly classifying it as a Foreign Key for the institution entity. |

---

## 🧠 Architectural Insights & Hyperparameter Analysis

Following the feedback from the ICDE review process, we provide an extended analysis of specific architectural behaviors, hyperparameter sensitivity, and the ablation of the verification components.

### 1. Sensitivity of Verification Rounds (`max_steps`)
A key hyperparameter in GaV is the maximum number of refinement rounds allowed for the Verifier agent. Our experiments show that this is not a static parameter but depends on two factors: **Model Personality** and **Context Quality**.

* **Model Sensitivity (Skepticism Level):** We observed that "Reasoner" models or those with specific fine-tuning (e.g., Qwen-Plus Thinking) exhibit higher skepticism. They require more iteration steps to accept a hypothesis compared to models like Qwen-Plus, which tend to converge faster.
* **Role of Description (The "Warm-Start"):** When the *Description* component is disabled, the initial hypotheses are often generic. This forces the Verifier to reject them frequently, increasing the number of rounds needed to reach a conclusion.

> **Guideline:** This parameter should be tuned based on the backbone. Increase `max_steps` for skeptical models or when operating in "Blind Mode" (no description).

![Placeholder: Chart showing Average Rejection Rate - GPT Family vs Qwen Family]
*(Figure: Comparative rejection rates showing higher iteration needs for Qwen models)*

![Placeholder: Chart showing Rejection Rate - With Description vs Without Description]
*(Figure: Impact of description on convergence speed)*

### 2. The "Easy" vs. "Hard" Classification Dilemma
The Verifier agent autonomously decides whether a verification task is "Easy" (solvable via metadata/reflection) or "Hard" (requires Python code execution).

* **Behavior of Non-Reasoner Models:** Models like `qwen-plus` (without thinking mode) tend to **underestimate complexity**. They frequently classify ambiguous tasks as "Easy," bypassing the Data Analyst to save tokens. While this reduces cost, it leads to superficial verification and lower accuracy.
* **Valid "Easy" Cases:** The "Easy" path is legitimate only when the hypothesis is trivial or directly verifiable against the statistical summary provided by the Description component (e.g., checking value ranges or null counts).
* **Impact of Description on Complexity:**
    * We observed (as illustrated in *Figure [Placeholder]* below) that the **Description component** plays a fundamental role in this classification.
    * *With Description:* For simple hypotheses, the initial statistical summary is often sufficient proof, allowing the Verifier to correctly choose the "Easy" path (Reflection).
    * *Without Description:* In the absence of this context, the Verifier rarely proposes an "Easy" verification. Lacking basic metadata, it is forced to default to "Hard" verification (Code Execution) to gather evidence from scratch, significantly increasing the computational overhead.

![Placeholder: Chart showing Easy/Hard classification ratio - With vs Without Description]
*(Figure: Analysis of verification complexity classification relative to context availability)*

### 3. Ablation: Why "Reflection" is Not Enough
We investigated the necessity of the **Data Analyst** (Code Execution) compared to a simple "Reflection" mechanism (LLM self-correction without tools).

* **Reflection (Verifier without Tools):** As established in recent literature, self-reflection helps reduce syntax errors and basic hallucinations. However, our ablation study confirms that for **Data Profiling**, reflection is insufficient. An LLM cannot "think" its way to the truth about a dataset's distribution or hidden constraints.
* **Execution (Verifier with Data Analyst):** The ability to execute code allows the system to empirically validate specific constraints (e.g., *"Is this truly a Foreign Key?"*, *"Are dates strictly sequential?"*).
    * *Result:* Disabling the Data Analyst forces the system into a "Reflection-Only" mode, which fails to capture granular semantic details, reverting to the performance levels of the "Description Only" configuration.
      
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


