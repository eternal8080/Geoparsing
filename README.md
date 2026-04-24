# Geoparsing: Diagram Parsing for Plane and Solid Geometry with a Unified Formal Language

[![Homepage](https://img.shields.io/badge/Homepage-Geoparsing-red)](https://eternal8080.github.io/geoparsing.github.io/)
[![Paper](https://img.shields.io/badge/Paper-ACL%202026-blue)](https://arxiv.org/abs/2604.11600)
[![Dataset](https://img.shields.io/badge/Dataset-GDP--29K-green)](https://huggingface.co/datasets/PeijieWang/GDP29K)
[![Model](https://img.shields.io/badge/Model-GDP--4B-yellow)](https://huggingface.co/PeijieWang/GDP-4B)
[![License](https://img.shields.io/badge/License-MIT-orange)](./LICENSE)

Official implementation for the ACL paper:

> **Geoparsing: Diagram Parsing for Plane and Solid Geometry with a Unified Formal Language**  

---

## 🔍 Overview

<p align="center">
  <img src="image/pipeline.png" width="700"/>
</p>

Multimodal Large Language Models (MLLMs) have shown strong reasoning ability but still struggle with **geometry understanding**, mainly due to **fine-grained perception bottlenecks**.

This project introduces:

- 🔷 A **unified formal language** for both **plane and solid geometry**
- 🔷 A large-scale dataset **GDP-29K** (20K plane + 9K solid)
- 🔷 A training paradigm combining:
  - Supervised Fine-Tuning (SFT)
  - Reinforcement Learning with Verifiable Rewards (RLVR)

Our method significantly improves **geometry diagram parsing** and boosts **downstream reasoning performance**.

---

## 🧠 Key Contributions

- ✅ Unified formal representation for **2D + 3D geometry**
- ✅ First large-scale **solid geometry parsing dataset**
- ✅ RL-based training with **rule-based verifier**
- ✅ Strong improvements on downstream tasks (Geometry3K, PGPS9K, SolidGeo)

---

## 📊 Dataset: GDP-29K

<p align="center">
  <img src="image/GDP29K.png" width="700"/>
</p>

- **Total samples**: 28,977
- **Plane geometry**: 19,965
- **Solid geometry**: 8,917
- Includes:
  - Printed diagrams
  - Handwritten diagrams

Each sample is annotated with:

```json
{
  "points": [...],
  "lines": [...],
  "circles": [...],
  "planes": [...],
  "structure": [...],
  "semantics": [...]
}
```
---

## 🚀 Inference
We follow the official environment setup of Qwen3-VL. Install dependencies by referring to the [Qwen3-VL official repository](https://huggingface.co/Qwen/Qwen3-VL-4B-Instruct).

We provide a minimal example for running inference with the released Geoparsing model.

```bash
import torch
from transformers import Qwen3VLForConditionalGeneration, AutoProcessor

model_path = "YOUR_MODEL_PATH"  # local path or HuggingFace repo id: https://huggingface.co/PeijieWang/GDP-4B

model = Qwen3VLForConditionalGeneration.from_pretrained(
    model_path,
    torch_dtype="auto",
    device_map="cuda:0"
)
processor = AutoProcessor.from_pretrained(model_path)

messages = [
    {
        "role": "user",
        "content": [
            {
                "type": "image",
                "image": "image/example.png",
            },
            {
                "type": "text",
                "text": "Please parse the geometric diagram and provide its formal description.",
            },
        ],
    }
]

inputs = processor.apply_chat_template(
    messages,
    tokenize=True,
    add_generation_prompt=True,
    return_dict=True,
    return_tensors="pt"
)
inputs = inputs.to(model.device)

generated_ids = model.generate(
    **inputs,
    max_new_tokens=1280
)

generated_ids_trimmed = [
    out_ids[len(in_ids):] for in_ids, out_ids in zip(inputs.input_ids, generated_ids)
]

output_text = processor.batch_decode(
    generated_ids_trimmed,
    skip_special_tokens=True,
    clean_up_tokenization_spaces=False
)

print(output_text[0])
```

## 📖 Citation

If you find this work useful in your research, please consider citing:

```bibtex
@article{wang2026geoparsing,
  title={Geoparsing: Diagram Parsing for Plane and Solid Geometry with a Unified Formal Language},
  author={Wang, Peijie and Zhang, Ming-Liang and Cao, Jun and Deng, Chao and Ran, Dekang and Sun, Hongda and Bu, Pi and Zhang, Xuan and Wang, Yingyao and Song, Jun and others},
  journal={arXiv preprint arXiv:2604.11600},
  year={2026}
}
