# LlamaTron-RS1-Rolex

[![View on Hugging Face](https://huggingface.co/datasets/huggingface/badges/resolve/main/model-on-hf-sm.svg)](https://huggingface.co/Rumiii/LlamaTron-RS1-Rolex)

Fine-tuned Llama-3.2-1B-Instruct for chain-of-thought medical reasoning, trained on the ReasonMed-370K dataset.

## Overview

This repository provides a merged, GGUF-format version of `meta-llama/Llama-3.2-1B-Instruct` fine-tuned via LoRA on **ReasonMed**, a 370,000-example medical reasoning dataset. The model produces step-by-step (chain-of-thought) reasoning before arriving at a final answer to clinical and medical questions.

**Disclaimer**: This model is for research, education, and prototyping only. It is not a medical device, diagnostic tool, or substitute for professional clinical judgment. Always consult a qualified healthcare professional for medical decisions.

## Usage

First, authenticate with the Hugging Face Hub:

```bash
hf auth login
```

**Using a pipeline (high-level helper):**

```python
from transformers import pipeline

pipe = pipeline("text-generation", model="Rumiii/LlamaTron-RS1-Rolex")
messages = [
    {"role": "user", "content": "Who are you?"},
]
pipe(messages)
```

**Loading the model directly:**

```python
from transformers import AutoModel

model = AutoModel.from_pretrained("Rumiii/LlamaTron-RS1-Rolex", dtype="auto")
```

## Repository Contents

This repository also contains a standalone GGUF file for local, framework-free inference:

```
llama3.2-1b-medical-reasonmed-fp16.gguf
```

This is the merged model in FP16 GGUF format, ready to run with any GGUF-compatible runtime.

## Local GGUF Inference

Download `llama3.2-1b-medical-reasonmed-fp16.gguf` and load it directly in any GGUF-compatible tool — no additional setup is required.

**With llama.cpp:**
```bash
./llama.cpp/main \
  -m llama3.2-1b-medical-reasonmed-fp16.gguf \
  --color --temp 0.7 --top-p 0.9 \
  -p "A patient presents with fever, cough, and shortness of breath. What is the most appropriate initial investigation?\nA. ECG\nB. Chest X-ray\nC. Blood culture\nD. CT pulmonary angiogram"
```

It also works out of the box with **Ollama**, **LM Studio**, and **KoboldCPP**.

If you need a smaller file size or faster inference on lower-end hardware, you can quantize it yourself locally using llama.cpp (e.g. to `Q4_K_M`, `Q5_K_M`, or `Q8_0`).

## Training Details

- **Base model**: `meta-llama/Llama-3.2-1B-Instruct`
- **Dataset**: [ReasonMed](https://arxiv.org/abs/2506.09513) — 370,000 multi-agent-generated medical reasoning examples
- **Method**: LoRA (rank 8, alpha 16, dropout 0.05) on `q_proj`, `k_proj`, `v_proj`, `o_proj`
- **Optimizer**: Adafactor, 3 epochs, learning rate 2e-4
- **Post-training**: LoRA adapters merged into the base model, then converted to GGUF (FP16)

## Limitations

- 1B-parameter base model — best suited for lightweight or edge use cases, not a replacement for larger (7B–70B) medical models
- No preference optimization (DPO/ORPO) applied yet
- Not formally evaluated on benchmark suites such as MedQA, PubMedQA, or MMLU-clinical

## License

- Code (training/merging scripts): MIT
- Base model: Meta Llama 3.2 Community License
- Fine-tuned weights and GGUF file: same as base model, plus ReasonMed dataset terms

## Acknowledgments

- ReasonMed authors (Yu Sun et al.) for the dataset and paper
- Meta AI for the Llama 3.2 base model
- The `llama.cpp` and Hugging Face PEFT communities
