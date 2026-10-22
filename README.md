# ClimateTech Sustainability Assessment — QLoRA Fine-Tuning

A parameter-efficient fine-tuning project exploring how a small instruction-tuned language model can generate structured sustainability assessments for ClimateTech company profiles.

The project focuses on industry classification, Sustainable Development Goal (SDG) relationships, evidence assessment, and responsible treatment of environmental impact claims.

## Project Overview

General-purpose language models may produce plausible answers without following a domain-specific output schema. This project fine-tunes Qwen2.5-1.5B-Instruct to follow a structured sustainability-assessment format using a ClimateTech instruction dataset.

The goal is to improve structured output generation and agreement with reference assessments, not to establish independent verification of environmental claims.

## Model and Training

- **Base model:** Qwen/Qwen2.5-1.5B-Instruct
- **Fine-tuning method:** QLoRA
- **Quantization:** 4-bit NF4
- **LoRA rank:** 8
- **LoRA alpha:** 16
- **LoRA dropout:** 0.05
- **Epochs:** 2
- **Training environment:** Google Colab with NVIDIA T4 GPU

QLoRA reduces training memory requirements by loading the base model in 4-bit quantization and training low-rank adapter parameters rather than updating all model parameters.

## Dataset

Dataset: [ClimateTech Instruction Dataset](https://huggingface.co/datasets/nattarkani/climatetech-instruction-dataset)

| Split | Examples |
|---|---:|
| Training | 800 |
| Validation | 100 |
| Test | 100 |
| **Total** | **1,000** |

The dataset contains instructions, company or scenario information, reference assessments, and metadata. It includes synthetic scenarios as well as real-company examples.

## Evaluation

The base and fine-tuned models were evaluated on the same 100-example test set.

| Metric | Base model | Fine-tuned model |
|---|---:|---:|
| Valid JSON rate | 92% | 100% |
| Exact-match agreement | 0% | 97% |
| Source-type agreement | Not measured | 99% |

### Metric definitions

- **Valid JSON rate:** proportion of generated responses that could be parsed as JSON.
- **Exact-match agreement:** proportion of predictions matching the reference after canonical JSON serialization, including keys, values, arrays, and text.
- **Source-type agreement:** agreement between the predicted and reference evidence source types.

The fine-tuned model's top-level schema-match rate was not separately measured.

### Interpretation

The results show improved output validity and agreement with the reference assessments after fine-tuning. The base model produced valid JSON in most cases, but its first inspected prediction used a different structure from the expected schema.

These metrics measure agreement with the dataset, not independently verified sustainability knowledge or factual accuracy.

## Responsible Assessment

The intended assessment workflow distinguishes between:

- Potential contributions and reported outcomes
- Company-reported claims and independently verified evidence
- Supported SDG relationships and relationships that are not established
- Synthetic examples and evidence about real-world companies

Fine-tuning cannot guarantee that these distinctions are always correct. Reference labels and impact claims require independent validation before the model is used for consequential decisions.


## Reproducibility

1. Install the dependencies listed in `requirements.txt`.
2. Load the dataset from Hugging Face.
3. Load the base model and configure 4-bit quantization.
4. Configure the LoRA adapter and train using the documented notebook.
5. Evaluate predictions on the held-out test set.

The exact package versions, hardware, and training configuration should be recorded in the notebook for reproducibility.

## Limitations

- The test set contains 100 examples.
- Exact-match agreement is strict and does not capture partial correctness.
- The reference assessments have not all been independently verified.
- High agreement with synthetic or curated labels does not establish real-world impact accuracy.
- Generation latency and cost were not benchmarked under controlled, identical conditions.
- The reported metrics should be reproduced before being treated as independently verified results.

