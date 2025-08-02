# CyPortQA

> **Anonymous research benchmark for cyclone preparedness in Port Operation (AAAI 2026 submission)**

CyPortQA is the first multimodal QA benchmark that evaluates how well multimodal large‑language models (MLLMs) understand **tropical‑cyclone forecasts**, reason about **port‑level impacts**, and recommend **operational strategies**. The dataset fuses real‑world NOAA hurricane products, USCG port‑condition bulletins, and AIS‑derived port performance metrics from 2015‑2023, expanding them into 117 k+ question–answer pairs across three task groups:

| Task | Ability | Example Question |
|------|---------|------------------|
| **S1** | Situation Understanding | *"Does Port X fall inside the uncertainty cone at T‑24 h?"* |
| **S2** | Impact Estimation | *"What is the expected recovery duration (days)?"* |
| **S3** | Decision Reasoning | *"Which port‑condition bulletin should be issued now?"* |

---

## Repository layout

```
Datasets/
  CyPortQA/                 # benchmark JSON + templates
  Source_data/              # raw NOAA / USCG / experiment data (⇢ Git LFS)
Codes/
  Data Collection/          # scripts that build Source_data/
  Senario Encoding/         # converts raw data → Encoded_senario.JSON
  Model Runing - Colab/     # Colab notebooks for baseline runs
  Model Runing - Local/     # local inference scripts (Python ≥ 3.10)
  o3 LLM Judger/            # evaluation prompts + judge harness
  Performance Eval/         # aggregation & plotting utilities
.gitignore                  # see below
requirements.txt            # minimal runtime deps (torch, transformers, etc.)
LICENSE                     # MIT
README.md                   # you are here
```

*Large files (> 100 MB) are tracked with **Git LFS** to keep the repo lightweight.*

---

## Quick start

### 1 · Clone and set up environment

```bash
# clone anonymously (no forks that reveal identity)
git clone https://github.com/anon-researcher/CyPortQA-Anon.git
cd CyPortQA-Anon

# create env (conda or venv)
conda create -n cyportqa python=3.10 -y
conda activate cyportqa
pip install -r requirements.txt
```

### 2 · Run a baseline on Colab (no local GPU needed)
Open the notebook in [`Codes/Model Runing - Colab/`](Codes/Model%20Runing%20-%20Colab/) and follow the cells; supply your API key(s) when prompted:

```text
export OPENAI_API_KEY=xxxxx   # ChatGPT‑4o
export GEMINI_API_KEY=xxxxx   # Gemini 2.5 Flash‑Lite
```

The notebook downloads the benchmark JSON from this repo and writes model outputs to `Experiments_data/`.

### 3 · Judge responses

```bash
python Codes/o3\ LLM\ Judger/judge.py \
       --pred_dir Experiments_data/<model_name>/ \
       --save_path Experiments_data/<model_name>_scored.jsonl
```

### 4 · Aggregate scores & plot

```bash
python Codes/Performance\ Eval/aggregate.py --root Experiments_data/
```

---

## Dataset schema

* `CyPortQA.JSON`              root list of QA items
* `CyPortQA_template.JSON`     template library (48 templates)
* `Encoded_senario.JSON`       encoded scenario metadata (weather + port info)

Each QA record contains:
```jsonc
{
  "qa_id": "S1.1-000042",
  "scenario_id": "HARVEY_2017_PORT001_Tm24h",
  "task": "S1.1",           // task category
  "question": "Does Port X …?",
  "answer": "True",         // ground‑truth
  "metadata": {...}          // cone geoJSON, forecast table slice, etc.
}
```

The benchmark *does not* distribute raw NOAA imagery; scripts in `Data Collection/` download them directly from public endpoints given a scenario timestamp.

---

## Adding new models
1. Place inference script or notebook under `Codes/Model Runing -*`.
2. Ensure outputs are saved to `Datasets/Source_data/Experiments_data/<model_name>/` with one JSONL per scenario.
3. Re‑run the judging and aggregation steps.

---

## .gitignore (excerpt)
```
# bookkeeping
__pycache__/
*.log
*.ipynb_checkpoints/

# credentials
.env
*.key

# large or generated artefacts
Datasets/Source_data/Experiments_data/
Datasets/Source_data/NOAA*/
Datasets/Source_data/CyPort*Events/
```

---

## License

This repository is released under the **MIT License**. See [`LICENSE`](LICENSE) for details.

---

## Citation

> *Citation details will be released after peer‑review.*

```bibtex
@misc{cyportqa2025,
  title  = {CyPortQA: Benchmarking Multimodal LLMs for Cyclone Preparedness in Port Operation},
  year   = {2025},
  note   = {Anonymous submission, AAAI 2026}
}
```

---
