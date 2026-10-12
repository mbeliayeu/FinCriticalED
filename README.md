---
pretty_name: FinCriticalED
license: apache-2.0
language:
- en
task_categories:
- image-to-text
size_categories:
- n<1K
source_datasets:
- original
annotations_creators:
- expert-generated
multilinguality: monolingual
tags:
- finance
- ocr
- document-understanding
- vision-language
- benchmark
- thefinai
- fincriticaled
configs:
- config_name: default
  data_files:
  - split: train
    path: raw_input.csv
dataset_info:
  features:
  - name: id
    dtype: int64
  - name: image
    dtype: string
  - name: matched_html
    dtype: string
  splits:
  - name: train
    num_examples: 848
extra_gated_heading: Request access to FinCriticalED
extra_gated_description: FinCriticalED is released by The Fin AI for research. Access is granted automatically after you complete this short form.
extra_gated_button_content: Agree and access
extra_gated_prompt: By accessing this dataset you agree to use it in accordance with the Apache 2.0 license and the terms of the original sources (SEC EDGAR), and to cite the FinCriticalED paper in any resulting publication.
extra_gated_fields:
  Full name: text
  Affiliation: text
  Country: country
  Intended use:
    type: select
    options:
    - Research
    - Education
    - Commercial evaluation
    - Other
  I agree to the terms above and will cite the paper: checkbox
---


# FinCriticalED

📄 [Paper](https://arxiv.org/abs/2511.14998) · 🌐 [The Fin AI](https://thefin.ai)

> **FinCriticalED** (Financial Critical Error Detection) is a visual benchmark for evaluating OCR and vision-language models on financial documents at the **fact level** — checking whether numbers, dates and other financially critical values survive OCR intact, rather than only measuring surface text overlap ([arXiv:2511.14998](https://arxiv.org/abs/2511.14998)).


## 📋Dataset Summary
The FinCriticalED dataset contains 2 parts: 

(1) **raw_input**: Raw HTML from financial documents retrieved from SEC EDGAR company filings and the corresponding images of the HTML chunk derived from intermediate rendered pdfs; and

(2) **gold_annotation_html**: Annotated and post-processed dataset that contains the raw image input, human expert annotated information on financially meaningful numeric and temporal entities in the gold standard HTML format of the financial documents in (1).Each txt file represents one post-processed HTML gold standard file for one image.

This dataset is used for FinCriticalED framework on evaluating Large Language Models ability on identifying financially critical values in complex financial documents, such as profit and revenue, important legal compliance due dates, contract durations etc. 

## 🚀 Quick Start

```python
from datasets import load_dataset

ds = load_dataset("TheFinAI/FinCriticalED", split="train")
print(ds[0]["id"], ds[0]["matched_html"][:200])
```

Gold annotations: `gold_annotation_html/gold_{id}.txt` (download with `huggingface_hub.hf_hub_download`).

## 💼Supported Tasks

**Task**
- **Task:** Financial OCR
- **Evaluation Metrics:** ROUGE1, ROUGEL, Edit Distance, Financial Fact Accuracy (FFA)

## 🌐Language

- English
  
## 🧬Dataset Structure

### Repository Layout

| Path | Content |
|---|---|
| `raw_input.csv` | 848 image–HTML pairs (the `default` config / `train` split in the viewer) |
| `gold_annotation_html/gold_{id}.txt` | Expert-annotated gold HTML, one file per `id` |
| `Archived/AnnotationWithSpan/` | Archived span-annotation files (incl. `Additional_Data/`) |

### Data Fields

#### (1) `raw_input.csv` — OCR input pairs

| Field | Type | Description |
|---|---|---|
| `id` | int | Zero-based sample index |
| `image` | string | Base64-encoded PNG image of the rendered financial document page |
| `matched_html` | string | HTML chunk corresponding to the rendered page image |

#### (2) `gold_annotation_html/` — Ground truth annotations

Each file is named `gold_{id}.txt` where `id` matches the `id` field in `raw_input.csv`. Files contain the source HTML with financially critical entities wrapped in custom span tags:

| Tag | Entity Type | Description | Example |
|---|---|---|---|
| `<number>` | Number | Financially critical numeric values (quantities, percentages, ratios) | `<number>3.5</number>` |
| `<temporal>` | Date / Time | Dates, years, durations, and other temporal expressions | `<temporal>December 31, 2024</temporal>` |
| `<monetaryunit>` | Monetary Unit | Monetary amounts with or without explicit currency symbols | `<monetaryunit>$1,200,000</monetaryunit>` |
| `<reportingentity>` | Reporting Entity | Company names, legal entities, and organizational identifiers | `<reportingentity>Apple Inc.</reportingentity>` |
| `<financialconcepts>` | Financial Concept | Key financial terms and concepts (revenue, EBITDA, etc.) | `<financialconcepts>net income</financialconcepts>` |

**Example snippet:**
```html
<p>For the fiscal year ended <temporal>December 31, 2024</temporal>,
<reportingentity>Acme Corp.</reportingentity> reported
<financialconcepts>net revenue</financialconcepts> of
<monetaryunit>$4.2 billion</monetaryunit>, up
<number>12.3%</number> year-over-year.</p>
```

## 💡Dataset Creation

### Curation Rationale

The FinCriticalED dataset was curated to support research and development on key information extraction techniques and factual reliability for unstructured documents in financial domain. 
By providing annotated real-world financial documents in unstructured format with ground truth, the dataset seeks to address challenges in maintaining factual consistency in complex financial OCR results.

### Source Data
#### Initial Data Collection

- The source data covers a broad range of real-world financial document structures.
- Subset of financial documents in 2023-2026, including both tabular and narrative-heavy formats, with examples such as 10-K and 10-Q reports, Form 4 and Form 8-K filings, Form 990, securities transaction records, and legal agreements such as M&A documents, credit agreements, and bond indentures.
- Data is downloaded in html format, and converted into corresponding images from intermediate rendered pdfs.
- The extracted text are used for matching html chunk and correct image.

#### Who are the Source Language Producers?

- The source data are financial documents from SEC EDGAR system: https://www.sec.gov/search-filings
- Source for tax forms (Form 990) from non-profit organizations: https://app.candid.org/

### Annotations

#### Annotation Process
- The dataset was prepared by collecting, matching, splitting, and converting financial documents in English
- Annotators are provided with HTML chunks, rendered in Label Studio, along with its corresponding image as assistance
- The annotators are asked to label critical numeric and temporal entities (including dates and durations) on HTML by referring to a carefully curated annotation guideline. 

#### Who are the Annotators?

- The dataset stems from publicly available financial documents.
- The annotator team contains finance researchers and senior analysts with rich financial background.

### Personal and Sensitive Information

- The FinCriticalED dataset does not contain any personally identifiable information (PII) and is strictly focused on English-language open-source financial documents. No personal or sensitive information is present in the dataset.

## 💭Considerations for Using the Data

### Social Impact of Dataset
This dataset enables AI models to extract structured information from scanned financial documents, promoting transparency and accessibility. 
By aligning page-level images with HTML data accurately labeled with financially critical numerical and temporal entities, it supports the development of more factual reliable and accurate financial OCR work in the finance domain. 

### Discussion of Biases
- The source data is limited to financial documents, it may underrepresent other domains, potentially limiting model generalizability.
- The data may over-represent English financial contexts.
- The data emphasizes on numerical and temporal data, may underrepresent other critical financial information, such as company names, person names, etc. 

### Other Known Limitations
- While the dataset covers wide range of financial documents, it may lack sufficient variety in layout styles (e.g., handwritten notes, non-standard financial forms, embedded charts), which could limit a model’s ability to generalize to less structured or unconventional financial documents.

## 🗒️Additional Information

### Dataset Curators
- Yueru He
- Xueqing Peng
- Yan Wang
- Yi Han
- The Fin AI Team

### Licensing Information

- **License:** Apache License 2.0
- **Open-source scope:** The `raw_input.csv` file distributed in this repository contains **848 samples** sourced exclusively from SEC EDGAR filings and other publicly redistributable sources, all released under Apache 2.0.
- **Excluded samples:** 11 Tax & Compliance Forms samples (Form 990) were sourced from [Candid (candid.org)](https://candid.org/). Due to Candid's data redistribution restrictions, these 11 cases are **not included** in the public dataset release. The paper benchmark uses all 859 samples; the HuggingFace dataset (`raw_input.csv`) contains only the 848 redistributable samples.

### Citation Information

If you use this dataset, please cite:

```bibtex
@misc{he2026fincriticaledvisualbenchmarkfinancial,
      title={FinCriticalED: A Visual Benchmark for Financial Fact-Level OCR}, 
      author={Yueru He and Xueqing Peng and Yupeng Cao and Yan Wang and Lingfei Qian and Haohang Li and Yi Han and Shuyao Wang and Ruoyu Xiang and Fan Zhang and Zhuohan Xie and Mingquan Lin and Prayag Tiwari and Jimin Huang and Guojun Xiong and Sophia Ananiadou},
      year={2026},
      eprint={2511.14998},
      archivePrefix={arXiv},
      primaryClass={cs.CV},
      url={https://arxiv.org/abs/2511.14998}, 
}
```