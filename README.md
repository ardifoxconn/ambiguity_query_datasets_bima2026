# UNTAD-RAG-Eval: A Grounded and Adversarial Query Dataset for RAG Evaluation on Indonesian Institutional Documents

[![License: CC BY 4.0](https://img.shields.io/badge/License-CC%20BY%204.0-lightgrey.svg)](https://creativecommons.org/licenses/by/4.0/)
[![Queries](https://img.shields.io/badge/Queries-170-blue)]()
[![Language](https://img.shields.io/badge/Language-Indonesian-green)]()
[![Domain](https://img.shields.io/badge/Domain-Institutional%20Documents-orange)]()

---

## Overview

**UNTAD-RAG-Eval** is a bilingual-labeled, domain-specific evaluation dataset for Retrieval-Augmented Generation (RAG) systems operating on Indonesian institutional documents. The dataset consists of **170 queries** — 110 grounded (answerable) and 60 adversarial (unanswerable) — derived from five official documents of Universitas Tadulako (UNTAD), Palu, Indonesia.

The dataset is designed to support two evaluation objectives:

1. **Behavioral analysis** of RAG systems under grounded versus adversarial query conditions, with a focus on answer relevance and faithfulness
2. **Metric sufficiency analysis** — specifically, whether aggregate faithfulness scores are sufficient to detect RAG failure on adversarial queries

This dataset accompanies two research papers currently in preparation:

> *Ardiansyah, R. et al. (in prep.). Perilaku Sistem Fusion-RAG pada Query Grounded dan Adversarial: Analisis Empiris pada Dokumen Institusional Berbahasa Indonesia.* [Target: Sinta 2]

> *Ardiansyah, R. et al. (in prep.). Are Faithfulness Metrics Sufficient for RAG Evaluation? Evidence from Adversarial Query Analysis on Indonesian Institutional Documents.* [Target: Scopus Q3]

---

## Dataset Statistics

| Split | n | Proportion |
|---|---|---|
| **Total** | **170** | 100% |
| Grounded | 110 | 64.7% |
| Adversarial | 60 | 35.3% |
| **Grounded:Adversarial ratio** | **1.83:1** | |

### Grounded Queries — Category Distribution

| Category | n | Proportion |
|---|---|---|
| `factual_lookup` | 22 | 20% |
| `definitional` | 22 | 20% |
| `procedural` | 22 | 20% |
| `eligibility_criteria` | 22 | 20% |
| `multi_hop_reasoning` | 22 | 20% |
| **Total** | **110** | 100% |

### Grounded Queries — Difficulty Distribution

| Difficulty | n | Proportion |
|---|---|---|
| `easy` | 52 | 47.3% |
| `medium` | 37 | 33.6% |
| `hard` | 21 | 19.1% |

### Adversarial Queries — Plausibility Distribution

| Plausibility | n | Proportion |
|---|---|---|
| `high` | 30 | 50% |
| `low` | 30 | 50% |

---

## Source Corpus

Five official documents from Universitas Tadulako:

| Document ID | Full Title | Type |
|---|---|---|
| `PANDUAN_PPM_2025` | Panduan Penelitian dan Pengabdian kepada Masyarakat UNTAD 2025 | Research & Community Service Guidelines |
| `Peraturan_Rektor_No_01_Tahun_2019_Kode_Etik` | Peraturan Rektor No. 01 Tahun 2019 tentang Kode Etik Sivitas Akademika | Rector's Regulation |
| `Kurikulum_OBE_PS_Teknik_Informatika_UNTAD_2025` | Kurikulum OBE Program Studi Teknik Informatika UNTAD 2025 | OBE Curriculum Document |
| `BUKU-PEDOMAN-DAN-PERATURAN-AKADEMIK-UNIVERSITAS-TADULAKO-TA-2023-2024` | Buku Pedoman dan Peraturan Akademik Universitas Tadulako TA 2023–2024 | Academic Regulation Handbook |
| `PANDUAN-AKADEMIK-FATEK-2023-2024` | Panduan Akademik Fakultas Teknik UNTAD 2023–2024 | Faculty-Level Academic Guidelines |

---

## Dataset Construction

### Sampling Strategy

This dataset was constructed using a **three-layer purposive stratified sampling** design. Each layer operates at a different level of granularity and serves a distinct methodological function.

---

#### Layer 1 — Stratified Sampling (primary structure)

The primary structure of the dataset follows **proportional stratified sampling**, in which the population of possible queries is partitioned into non-overlapping strata prior to selection, and each stratum is sampled independently to ensure uniform representation.

For grounded queries, strata are defined by the cross-product of *query category* × *source document*. The target cell size of 22 queries per category was held constant across all five categories, producing a perfectly balanced marginal distribution over categories (22 × 5 = 110). For adversarial queries, strata are defined by *plausibility level* × *source document*, with a target of 30 queries per plausibility level, yielding a 50:50 split.

This design ensures that no single query type or document dominates the evaluation signal — a property that is necessary for the between-group comparisons (grounded vs. adversarial, high vs. low plausibility) that constitute the primary analyses in the associated papers. The approach follows established practice in evaluation benchmark construction [1, 2].

---

#### Layer 2 — Purposive Allocation across Documents (within-stratum allocation)

Within each stratum, the number of queries allocated per document was determined through **purposive sampling** based on an assessment of each document's *information richness* — defined here as the density of operationalizable regulatory content (explicit conditions, procedures, and eligibility criteria that can be unambiguously verified against the document).

This judgment-based allocation produced unequal per-document counts (PPM: 27, Pedoman Akademik: 25, OBE: 21, Panduan FT: 20, Kode Etik: 17 for grounded; analogous allocation for adversarial). Documents with denser regulatory content received higher allocation. Proportional-to-length allocation was rejected because document length is a poor proxy for query-generation potential in this domain — a long preamble section contributes fewer grounded queries than a short but dense eligibility clause.

Purposive sampling is appropriate when theoretical considerations about population substructure justify non-random selection [3]. The specific allocation decisions are documented in the dataset note and are available for peer review.

---

#### Layer 3 — Content-Driven Negative Sampling (adversarial query construction)

Adversarial queries were not drawn randomly from a space of possible questions. Instead, they were constructed through **systematic content gap analysis**: for each source document, sections were examined to identify topics that are *plausibly implied* by the document's scope but are factually absent from its content. Each adversarial query was then formulated to probe this gap.

Plausibility classification (`high` vs. `low`) operationalizes the *surface similarity* between query terms and document vocabulary. A `high`-plausibility adversarial query uses terminology that appears in the document and references entities or procedures that exist within it, but asks for information the document does not provide. A `low`-plausibility adversarial query addresses a topic with no terminological or structural overlap with the document.

This construction strategy follows the adversarial negative sampling approach in SQuAD 2.0 [4], where unanswerable questions were crafted by crowdworkers to be plausible given the passage, requiring systems to distinguish answerability rather than simply matching surface terms.

---

### Difficulty Annotation (Grounded Queries)

Difficulty labels reflect the *retrieval and reasoning complexity* required to produce a correct answer, operationalized as follows:

| Label | Definition | Decision Rule |
|---|---|---|
| `easy` | Answer is stated explicitly in a single sentence or clause | Validator identifies one sentence as a complete, standalone answer |
| `medium` | Answer requires synthesizing ≥2 points within the same section or article | Validator must read more than one sentence but does not need to cross section boundaries |
| `hard` | Answer requires cross-section or cross-document reasoning | Validator must consult at least two distinct sections or two different documents |

Boundary cases between `medium` and `hard` were resolved using the rule: *"Does answering this query require moving to a different pasal or bab?"* — if yes, the query is classified `hard`.

---

### Validation Protocol

All queries were validated by domain experts from UNTAD with direct familiarity with the source documents:

| Validator Role | Documents Assigned |
|---|---|
| Head of Informatics Study Program | Kurikulum OBE Informatika 2025 |
| PIPM Team (Research & Community Service Unit) | Panduan PPM UNTAD 2025 |
| Faculty Quality Assurance Team | Kode Etik, Pedoman Akademik UNTAD, Panduan Akademik FT |

Two independent validators per grounded query. Inter-annotator agreement is computed using Cohen's κ across four dimensions: factual correctness, completeness, difficulty label, and category label. All 60 adversarial queries are validated by all three validator groups independently, with three-way IAA computed for adversarial status confirmation and plausibility label.

---

## File Format

The dataset is released as a single JSON file: `queries_with_answer_v2_alldoc_standardized.json`

Each record contains the following fields:

| Field | Type | Description |
|---|---|---|
| `query_id` | `string` | Unique identifier. Format: `[DOC]-G-[NNN]` for grounded, `[DOC]-A-[NNN]` for adversarial |
| `query` | `string` | Query text in Bahasa Indonesia |
| `query_type` | `string` | `"grounded"` or `"adversarial"` |
| `category` | `string` \| `null` | Query category (grounded only): `factual_lookup`, `definitional`, `procedural`, `eligibility_criteria`, `multi_hop_reasoning`. `null` for adversarial |
| `category_label` | `string` \| `null` | Human-readable category label |
| `source_document` | `string` | Document ID from which the query was derived |
| `source_section` | `string` | Section or article reference (grounded: source of answer; adversarial: note on why answer is absent) |
| `expected_answer_exists` | `boolean` | `true` for all grounded, `false` for all adversarial |
| `difficulty` | `string` \| `null` | `"easy"`, `"medium"`, or `"hard"` for grounded. `null` for adversarial |
| `plausibility` | `string` \| `null` | `"high"` or `"low"` for adversarial. `null` for grounded |
| `expected_answer` | `string` \| `null` | Reference answer (paraphrase from source document) for grounded queries. `null` for adversarial |
| `expected_key_facts` | `array` \| `null` | List of key facts that must appear in a correct answer (grounded only). `null` for adversarial |

### Example Records

**Grounded query (procedural, medium):**
```json
{
  "query_id": "PPM-G-009",
  "query": "Apa saja tahapan pengelolaan penelitian dan pengabdian kepada masyarakat di Untad menurut panduan 2025?",
  "query_type": "grounded",
  "category": "procedural",
  "category_label": "Procedural",
  "source_document": "PANDUAN_PPM_2025",
  "source_section": "BAB II, 2.2 Tahapan Pengelolaan",
  "expected_answer_exists": true,
  "difficulty": "medium",
  "plausibility": null,
  "expected_answer": "Tahapan pengelolaan penelitian dan pengabdian kepada masyarakat di Untad terdiri dari 9 tahap, yaitu: (1) pengumuman, (2) pengusulan melalui Sipenaemas, (3) seleksi/review, (4) penetapan/pengumuman pemenang, (5) revisi proposal, (6) pelaksanaan, (7) pengawasan, (8) pelaporan, dan (9) penilaian hasil penelitian dan pengabdian kepada masyarakat.",
  "expected_key_facts": ["9 tahapan", "pengumuman", "pengusulan", "seleksi/review", "penetapan", "revisi proposal", "pelaksanaan", "pengawasan", "pelaporan", "penilaian hasil"]
}
```

**Adversarial query (high plausibility):**
```json
{
  "query_id": "PPM-A-004",
  "query": "Apakah panduan PPM 2025 mengatur secara detail mekanisme penelitian yang didanai dari sumber di luar DIPA Untad?",
  "query_type": "adversarial",
  "category": null,
  "category_label": null,
  "source_document": "PANDUAN_PPM_2025",
  "source_section": "N/A",
  "expected_answer_exists": false,
  "difficulty": null,
  "plausibility": "high",
  "expected_answer": null,
  "expected_key_facts": null
}
```

---

## Intended Use

This dataset is intended for:

- Evaluating RAG system behavior on domain-specific, low-resource Indonesian institutional documents
- Benchmarking refusal calibration — whether a system correctly declines to answer adversarial queries rather than hallucinating a response
- Studying the sensitivity (or insensitivity) of aggregate faithfulness metrics to query type
- Replication studies in the Indonesian NLP and RAG evaluation domain

### Out-of-Scope Uses

This dataset should not be used as a general-purpose Indonesian QA benchmark. The corpus is narrow (5 documents, one institution) and the query construction is evaluation-instrument-first, not representative-of-user-behavior-first. Generalization claims to other institutions or document types require separate validation.

---

## Limitations

1. **Single-institution corpus.** All five source documents are from Universitas Tadulako. Generalization to other Indonesian universities or institutional document types has not been validated.
2. **Indonesian only.** The dataset does not cover cross-lingual or multilingual retrieval scenarios.
3. **Unequal per-document query counts.** Allocation is based on information richness, not document length. PPM contributes disproportionately to the `eligibility_criteria` category (n=8 of 22) due to domain characteristics, not design inconsistency.
4. **Ground truth pending full IAA.** `expected_answer` and `expected_key_facts` fields were authored by the dataset creators and are currently undergoing formal inter-annotator validation. Reported Cohen's κ values will be updated post-validation.
5. **No cross-document hard queries across institutions.** All `hard` queries require cross-section reasoning within the same document or across documents from the same institution.

---

## Citation

If you use this dataset, please cite:

```bibtex
@dataset{ardiansyah2025untadrageval,
  author    = {Ardiansyah, Rizka and Angreni, Dwi Shinta and Saputra, Sabarudin and Hernita, Ayu and Mustari, Aidynal},
  title     = {{UNTAD-RAG-Eval}: A Grounded and Adversarial Query Dataset for RAG Evaluation on Indonesian Institutional Documents},
  year      = {2026},
  publisher = {GitHub},
  institution = {Universitas Tadulako},
  url       = {https://github.com/ardifoxconn/ambiguity_query_datasets_bima2026}
}
```

---

## License

This dataset is released under [Creative Commons Attribution 4.0 International (CC BY 4.0)](https://creativecommons.org/licenses/by/4.0/). You are free to share and adapt the dataset for any purpose, provided appropriate credit is given.

Source documents are official publications of Universitas Tadulako and are reproduced here in derivative form (query-answer pairs) under fair use for academic research purposes.

---

## References

[1] Thakur, N., Reimers, N., Rücklé, A., Srivastava, A., & Gurevych, I. (2021). BEIR: A Heterogeneous Benchmark for Zero-shot Evaluation of Information Retrieval Models. *NeurIPS 2021 Datasets and Benchmarks Track.*

[2] Chen, J., et al. (2024). Benchmarking Large Language Models in Retrieval-Augmented Generation. *Proceedings of AAAI 2024.*

[3] Patton, M. Q. (2002). *Qualitative Research and Evaluation Methods* (3rd ed.). SAGE Publications.

[4] Rajpurkar, P., Jia, R., & Liang, P. (2018). Know What You Don't Know: Unanswerable Questions for SQuAD. *Proceedings of ACL 2018.*

---

## Contact

**Rizka Ardiansyah, S.Kom., M.Kom.**
Lecturer, Department of Informatics Engineering & Information Systems
Universitas Tadulako, Palu, Indonesia
`[rizka@untad.ac.id]`
