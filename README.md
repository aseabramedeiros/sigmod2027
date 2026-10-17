# From Implicit Semantics to Verifiable Design Expectations: Neuro-Symbolic Relational Schema Auditing

This repository contains experimental artifacts accompanying the research paper **“From Implicit Semantics to Verifiable Design Expectations: Neuro-Symbolic Relational Schema Auditing.”** It documents two experimental configurations for auditing relational schema designs: a direct **LLM-only** baseline and a **neuro-symbolic** approach that reconstructs design expectations from evidence and checks their consequences through explicit reasoning and deterministic procedures.

The repository is intended to support inspection of the implementation, inputs, experimental procedures, and reported results. It is **not** presented as a standalone named benchmark or a production-ready schema auditing tool.

## Repository layout

```text
.
├── README.md
├── LLM-Only.ipynb
├── Neuro-Symbolic.ipynb
└── Spider2/
    └── ...                 # Input artifacts used by the experiments
```

- **`LLM-Only.ipynb`** — Direct LLM-based schema auditing (the V2 experimental configuration), including the evaluation of generated findings.
- **`Neuro-Symbolic.ipynb`** — The V3 experimental configuration, combining neural interpretation with symbolic reasoning and deterministic checks.
- **`Spider2/`** — Relational artifacts and experimental inputs derived from the Spider2-DBT material. This directory is intended to hold the input files referenced by the notebooks. Its exact internal layout and contents depend on the files deposited with the release.

The public notebook filenames describe their experimental roles. Internal identifiers such as `V2` and `V3` may still appear in notebook code, generated outputs, and experiment metadata to preserve traceability.

## Research objective

Formal database verification can test explicit constraints and dependencies, but important design expectations may be implicit in identifiers, relationships, SQL artifacts, documentation, and domain context. The paper distinguishes:

- **Defect recognition:** identifying evidence suggesting a possible schema design problem.
- **Defect establishment:** reconstructing the relevant design expectation and providing a justified basis for concluding that it is violated.

The proposed approach investigates whether neural interpretation can help reconstruct these expectations while symbolic reasoning and deterministic checks make resulting findings more explicit and inspectable.

## Experimental design

The evaluation uses **1,561 controlled mutation instances** constructed from realistic relational artifacts associated with Spider2-DBT. The mutations are grouped by the evidence required to establish the violated expectation:

| Category | Mutation operator | Instances |
| --- | --- | ---: |
| L1 — Explicit structural defects | Remove `NOT NULL` | 861 |
| L1 — Explicit structural defects | Remove `UNIQUE` | 300 |
| L2 — Relational design flaws | Introduce a transitive dependency | 349 |
| L3 — Semantic schema defects | Introduce a semantic relationship mismatch | 51 |
| **Total** | | **1,561** |

These counts reflect the applicability of each mutation operator to the source artifacts, not the prevalence of the corresponding defects in operational databases.

### LLM-only experiment

The LLM-only baseline directly examines mutated relational artifacts and generates audit findings. The execution notebooks use **Python**, **Amazon Bedrock** (via **Boto3**), and **Claude 3 Sonnet** for auditing, with `temperature=0` and an output limit of 1,200 tokens per call. A complementary evaluation uses **Claude 3 Haiku** to assess generated findings against injected mutations.

The baseline evaluation distinguishes `MATCH`, `PARTIAL`, and `NO_MATCH` judgments. These judgments should not be treated as equivalent to the target-detection and exact-case-match criteria used in the neuro-symbolic evaluation.

### Neuro-symbolic experiment

The neuro-symbolic configuration combines neural interpretation of available relational evidence with explicit reasoning and deterministic checks. Its goal is to reconstruct relevant design expectations, retain the evidential basis of reconstructed premises, and assess whether the schema satisfies those expectations.

The experiment distinguishes **target detection** (whether the injected target is identified) from **exact case match** (whether the target is identified and only one finding is emitted). Exact case match is a restrictive output-specificity criterion: additional findings do not automatically imply false positives.

The two experimental configurations use **different assessment protocols**. Their headline percentages should therefore **not** be interpreted as a direct numerical comparison of model performance.

## Reported neuro-symbolic results

| Mutation operator | Target detection | Exact case match |
| --- | ---: | ---: |
| Remove `NOT NULL` | 14.87% | 12.78% |
| Remove `UNIQUE` | 42.00% | 27.00% |
| Introduce transitive dependency | 100.00% | 0.57% |
| Introduce semantic relationship mismatch | 92.16% | 0.00% |
| **Overall** | **41.64%** | **12.36%** |

Results vary substantially across operators. In particular, successful target detection does not necessarily imply that the auditor produced a single, specific finding. Consult the paper and the notebook outputs for interpretation of these metrics.

## Token usage and inference cost

For the LLM-only **auditing stage** (all 1,561 instances), recorded API usage totals **10,899,351 input tokens** and **374,082 output tokens** (11,273,433 tokens combined). The confirmed Haiku evaluation records for **541 L1/L2 cases** add **474,520 input tokens** and **54,462 output tokens**. These Haiku figures are a **partial subtotal**, not the full evaluation-stage usage.

At illustrative standard on-demand rates of **US$3 / US$15 per million input/output tokens** for Claude 3 Sonnet and **US$0.25 / US$1.25** for Claude 3 Haiku, the corresponding estimated inference costs are approximately **US$38.31** for the Sonnet auditing stage and **US$0.19** for the confirmed Haiku subset. The **US$38.50 combined amount is not a complete experiment cost**; it excludes other evaluation calls, local processing, and any account-specific pricing or charges. These are price-based estimates, not verified AWS invoice totals.

The neuro-symbolic notebook does not preserve a complete measured token-usage aggregate; no V3 cost total is claimed here.

## Inspecting and reproducing the experiments

1. Obtain the notebooks and populate `Spider2/` with the experimental input artifacts distributed for this paper.
2. Inspect each notebook's initial configuration cells for local paths, required Python packages, model identifiers, and AWS region settings. Update these to match your environment.
3. Configure AWS credentials **outside the repository**, using a supported AWS credential mechanism, and ensure access to the required Amazon Bedrock models.
4. Execute the notebook cells in their documented order, checking intermediate files and generated results before proceeding to subsequent stages.
5. Compare generated outputs with the reported metrics, taking account of differences between the LLM-only and neuro-symbolic evaluation protocols.

**Reproducibility scope.** The supplied execution notebooks consume experimental artifacts prepared in earlier steps. Full end-to-end regeneration of mutation inputs is possible only if the corresponding source artifacts and generation procedures are also included in the release. The `Spider2/` folder is reserved for these inputs; its presence alone does not establish that every dependency is available. Bedrock model access, pricing, and model availability may vary by account and region.

## Responsible release of artifacts

Before publication, review notebooks **including saved cell outputs** for credentials, access tokens, account identifiers, internal endpoints, absolute paths, and other sensitive environment details. Avoid committing AWS credential files or proprietary datasets. Confirm the redistribution terms and attribution requirements applicable to any Spider2-DBT-derived materials placed in `Spider2/`.

## Citation

If you use these artifacts, please cite the accompanying paper:

*From Implicit Semantics to Verifiable Design Expectations: Neuro-Symbolic Relational Schema Auditing.*

Full bibliographic information will be added after publication.
