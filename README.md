# CyPortQA

> **Anonymous research benchmark for cyclone preparedness in Port Operation (AAAI 2026 submission)**

CyPortQA is the first **multimodal QA benchmark** that evaluates how well multimodal large‑language models (MLLMs) can
1. understand **tropical‑cyclone forecasts**,
2. reason about **port‑level impacts**, and
3. recommend **operational strategies**.

The dataset fuses real‑world NOAA hurricane products, USCG port‑condition bulletins, and AIS‑derived port‑performance metrics collected from **2015 – 2023** to generate **117 k +** question–answer pairs across three task groups:

| Task | Core Ability | Example Question |
|------|--------------|------------------|
| **S1** | Situation Understanding | *“Does Port X fall inside the uncertainty cone at T‑24 h?”* |
| **S2** | Impact Estimation | *“What is the expected recovery duration (days)?”* |
| **S3** | Decision Reasoning | *“Which port‑condition bulletin should be issued now?”* |

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
requirements.txt            # pip alternative to the conda env below
environment.yml             # full Anaconda environment (recommended)
LICENSE                     # MIT
README.md                   # you are here
```

> **Note**  Large files (> 100 MB) are tracked with **Git LFS** to keep the repo lightweight.

---

## Quick start

### 1 · Set up the environment (conda recommended)

```bash
# clone anonymously (no forks that reveal identity)
git clone https://github.com/anon-researcher/CyPortQA-Anon.git
cd CyPortQA-Anon

# create & activate environment
conda env create -f environment.yml
conda activate cyportqa
# ‑ or ‑
# pip install -r requirements.txt
```

<details>
<summary>📦 <code>environment.yml</code> (click to expand)</summary>

```yaml
name: cyportqa
channels:
  - conda-forge
  - defaults
dependencies:
  - python=3.10
  - pip
  - git-lfs
  - pip:
      - torch>=2.2
      - transformers>=4.43
      - datasets>=2.19
      - tiktoken>=0.6
      - numpy
      - pandas
      - matplotlib
      - tqdm
      - scikit-learn
```

</details>

### 2 · Run a baseline (Colab or local)
Open the notebook in **`Codes/Model Runing - Colab/`** *or* the script in **`Model Runing - Local/`** and supply your API key(s) when prompted:

```bash
export OPENAI_API_KEY=xxxxx   # ChatGPT‑4o
export GEMINI_API_KEY=xxxxx   # Gemini 2.5 Flash‑Lite
```

The runner downloads `CyPortQA.JSON`, performs inference, and writes model outputs to `Datasets/Source_data/Experiments_data/<model_name>/`.

### 3 · Judge responses

```bash
python Codes/o3\ LLM\ Judger/judge.py \
       --pred_dir Datasets/Source_data/Experiments_data/<model_name>/ \
       --save_path Datasets/Source_data/Experiments_data/<model_name>_scored.jsonl
```

### 4 · Aggregate scores & plot

```bash
python Codes/Performance\ Eval/aggregate.py --root Datasets/Source_data/Experiments_data/
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
  "metadata": { ... }        // cone geoJSON, forecast table slice, etc.
}
```

> The benchmark *does not* redistribute raw NOAA imagery; scripts in `Data Collection/` download them from the official public endpoints given a scenario timestamp.

---

## Adding new models

1. Place your inference script or notebook under `Codes/Model Runing -*`.
2. Save predictions to `Datasets/Source_data/Experiments_data/<model_name>/` (one JSONL per scenario).
3. Re‑run the judging and aggregation steps above.

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

CyPortQA is released under the **MIT License**. See the [LICENSE](LICENSE) file for the full text.

---

## Citation (to be updated post‑review)

```bibtex
@misc{cyportqa2025,
  title  = {CyPortQA: Benchmarking Multimodal LLMs for Cyclone Preparedness in Port Operation},
  year   = {2025},
  note   = {Anonymous submission, AAAI 2026}
}
```
