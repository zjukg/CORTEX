# CORTEX: High-Quality Cross-Domain Organization of Web-Scale Corpora through Ontological Corpus Graph

**English** | [简体中文](./README_CN.md)

<p align="center">
  <img src="./figs/title.png" alt="CORTEX paper title and authors" width="88%">
</p>

<p align="center">
  <a href="https://arxiv.org/abs/2606.30175"><img src="https://img.shields.io/badge/arXiv-2606.30175-b31b1b.svg" alt="Paper"></a>
  <a href="https://arxiv.org/abs/2606.30175"><img src="https://img.shields.io/badge/EMNLP-2026%20Main-4c78a8.svg" alt="Venue"></a>
  <a href="https://huggingface.co/datasets/zjukg/CORTEX"><img src="https://img.shields.io/badge/Hugging_Face-Dataset-FFD21E.svg" alt="Hugging Face Dataset"></a>
</p>

## Abstract

The continuous evolution of large language models drives escalating demands on data scale and quality, and as different training stages impose increasingly tailored data requirements, systematic organization of high-quality corpora becomes indispensable. Existing corpus construction pipelines confine the resulting corpora to flat, undifferentiated document collections, universally lacking systematic knowledge organization. We present Cortex, to our knowledge the first framework that elevates web-scale corpus construction from flat document filtering to structured knowledge organization through an Ontological Corpus Graph (OCG), a three-layer heterogeneous structure unifying a quality-refined content layer, a hierarchical lightweight ontology layer via LLM-driven automated evolution, and a cross-domain alignment layer enabling inter-domain association at arbitrary taxonomic resolution. Comprehensive experiments confirm the effectiveness of Cortex. In particular, we leverage the OCG to synthesize CortexBench, a cross-domain search-and-reasoning benchmark whose evaluation across eight frontier LLMs validates the effectiveness of quality refinement, domain organization, and cross-domain data synthesis. We will publicly release the complete codebase, a 24.14B-token refined corpus with its OCG, and CortexBench.

## CORTEX Framework

<p align="center">
  <img src="./figs/CORTEX_overview.png" alt="Overview of the CORTEX framework" width="100%">
</p>

## Dataset Organization

```text
.
├── CORTEXBench/
│   ├── CORTEXBench_QA.jsonl
│   └── candidate_pool/
│       └── *.jsonl                         # 917 question-specific pools
├── High_Quality_Content/
│   ├── README.md
│   ├── time_NOT_Related/
│   │   └── <ontology hierarchy>/
│   │       ├── <concept_chain>.jsonl       # full document records
│   │       └── Ranked_id/
│   │           └── <concept_chain>.jsonl   # compact index records
│   └── time_Related/
│       └── <ontology hierarchy>/
│           ├── <concept_chain>.jsonl       # full document records
│           └── Ranked_id/
│               └── <concept_chain>.jsonl   # compact index records
├── Hight_Quality_Content_to_Ontology/
│   ├── README.md
│   ├── Code/5_ConceptGraphConstruct/
│   │   ├── 1_ConceptGraphConstruct_TextGraph.py
│   │   ├── 2_ConceptGraphConstruct_Graph.py
│   │   └── 3_ConceptGraphConstruct_PrefixAssociation.py
│   ├── all/
│   │   ├── 01_raw_mountings.jsonl
│   │   ├── 02_chain_stats.json
│   │   ├── 03_keyword_stats.json
│   │   ├── 04_global_weights.jsonl
│   │   ├── 05_chain_profiles.json
│   │   ├── 06_neighborhood_associations.json
│   │   └── 07_summary_statistics.json
│   ├── time_NOT_Related/
│   └── time_Related/
└── Lightweight_Ontology/
    ├── README.md
    ├── Concept_Chain_Set.txt
    └── Concept_Chain_Set_Flatten.txt
```

## Folder Sizes

- `CORTEXBench`: 37.72 GB, 918 files
- `High_Quality_Content`: 768.74 GB, 1,613 files
- `Hight_Quality_Content_to_Ontology`: 31.59 GB, 25 files
- `Lightweight_Ontology`: 22.8 KB, 3 files
- **Total**: 838.05 GB, 2,559 files

## Dataset Statistics

### Data Volume Across Pipeline Stages

<p align="center">
  <img src="./figs/Table8.png" alt="Data volume at each CORTEX pipeline stage" width="58%">
</p>

### Quality-Tier and Temporal Distribution

<p align="center">
  <img src="./figs/Table3.png" alt="Quality-tier and temporal distribution of the preprocessed corpus" width="58%">
</p>

### Domain Distribution of High-Quality Documents

<p align="center">
  <img src="./figs/Table13.png" alt="Domain distribution of high-quality documents across 12 top-level domains" width="100%">
</p>

### Cross-Domain Associations in the OCG

<p align="center">
  <img src="./figs/Table14.png" alt="Top-10 OCG neighbor chains for the finance concept chain" width="58%">
</p>

## Data Files

### `CORTEXBench`

- `CORTEXBench_QA.jsonl` contains 917 questions with reference answers, task categories, source documents, golden evidence, and candidate-pool metadata.
- `candidate_pool/*.jsonl` contains question-specific retrieval candidates. Each pool is designed for approximately 6,000 documents; the exact count is recorded in `candidate_pool_records`.

### `High_Quality_Content`

The refined corpus is separated into `time_NOT_Related` and `time_Related`, with both subsets organized by the same 403 leaf-level concept chains.

- Full document files contain document metadata, main text, quality and temporal scores, sentence-level refinement results, concept-chain associations, and extracted keywords.
- `Ranked_id` files provide `warc_id`, `target_confidence`, and `ConceptChainsRelated_top_3` for lightweight ranking and selection.

### `Hight_Quality_Content_to_Ontology`

The `all`, `time_NOT_Related`, and `time_Related` directories contain:

- raw document–chain–keyword mountings;
- per-chain and per-keyword statistics;
- weighted chain–keyword edges and chain profiles;
- inter-chain neighborhood associations;
- summary statistics.

The included scripts construct the file-based text graph, import it into Neo4j, and query neighboring concept chains.

### `Lightweight_Ontology`

- `Concept_Chain_Set.txt`: hierarchical lightweight ontology.
- `Concept_Chain_Set_Flatten.txt`: hierarchical lightweight ontology (flattened version).

## Download and Read

The repository contains heterogeneous file schemas. Download the required component or path explicitly.

```bash
# Benchmark QA file
hf download zjukg/CORTEX CORTEXBench/CORTEXBench_QA.jsonl \
  --repo-type dataset --local-dir ./CORTEX

# Lightweight ontology
hf download zjukg/CORTEX \
  --repo-type dataset \
  --include "Lightweight_Ontology/**" \
  --local-dir ./CORTEX

# Example corpus branch
hf download zjukg/CORTEX \
  --repo-type dataset \
  --include "High_Quality_Content/time_NOT_Related/自然科学/**" \
  --local-dir ./CORTEX
```

```python
import json

with open("CORTEX/CORTEXBench/CORTEXBench_QA.jsonl", encoding="utf-8") as f:
    for line in f:
        record = json.loads(line)
```

## License

The source data was obtained from Common Crawl and is subject to the [Common Crawl Terms of Use](https://commoncrawl.org/terms-of-use) and applicable rights of the original content owners.


## Citation

```bibtex
@article{DBLP:journals/corr/abs-2606-30175,
  author       = {Chengtao Gan and
                  Xiaoke Guo and
                  Yushan Zhu and
                  Zhaoyan Gong and
                  Zhiqiang Liu and
                  Songze Li and
                  Huajun Chen and
                  Wen Zhang},
  title        = {{CORTEX:} High-Quality Cross-Domain Organization of Web-Scale Corpora
                  through Ontological Corpus Graph},
  journal      = {CoRR},
  volume       = {abs/2606.30175},
  year         = {2026},
  url          = {https://doi.org/10.48550/arXiv.2606.30175},
  doi          = {10.48550/ARXIV.2606.30175},
  eprinttype   = {arXiv},
  eprint       = {2606.30175},
  timestamp    = {Fri, 10 Jul 2026 14:26:04 +0200},
  biburl       = {https://dblp.org/rec/journals/corr/abs-2606-30175.bib},
  bibsource    = {dblp computer science bibliography, https://dblp.org}
}
```
