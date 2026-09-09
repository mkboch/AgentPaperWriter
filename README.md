# AgentPaperWriter

Dataset in, verifiable paper out — running entirely on local models.

Two local Qwen models on one server: a **writer** proposes research directions, runs its own
experiments, and drafts the manuscript; an independent **critic** with different weights
reviews the ideas and the paper. Neither can accept a paper on its own — a deterministic
validator holds the veto, because two models left to negotiate will agree with each other
rather than with the evidence.

<!--LINK-->
**Live demo:** https://intend-frog-facilities-scroll.trycloudflare.com  
status `online` · updated 2026-09-09 18:50 UTC

The link needs an access key appended as `?k=...`; it is not published here.
<!--/LINK-->

## What it does

    dataset + description
        ↓  profile it            measured facts only; the model never reads raw rows
        ↓  read the data card    README / HuggingFace card / source page
        ↓  search live           OpenAlex, arXiv, Crossref, PubMed
        ↓  find open gaps        grounded in the retrieved abstracts
        ↓  propose 5 directions
        ↓  SCREEN them           live novelty search + a fixed rubric; dead ideas dropped
        ↓  [ user picks one ]
        ↓  design review         confounds, leakage, construct validity — before the run
        ↓  run the experiment    real code, executed, repaired from its own errors
        ↓  replicate             5 seeds; sign-unstable metrics are flagged
        ↓  write the paper       sections in dependency order
        ↓  validate + review     deterministic checks + an independent critic model
        ↓  compile               PDF built from paper.tex

## Why the output is checkable

| mechanism | what it makes impossible |
|---|---|
| citation keys bound to a JSON-Schema `enum` | emitting a reference that was never retrieved |
| every number checked against `experiment_results.json` | inventing a result |
| the model sees a measured profile, not raw rows | inventing a dataset property |
| 5-seed replication gate | reporting an unstable finding as an effect |
| implementation record + independent cross-check | claiming a method that was never run |
| the critic's own numbers verified against results | abandoning good science on a hallucinated figure |

`provenance.json` records every claim with the evidence that licenses it — the JSON path of
the measurement, or the DOI of the citation — plus sha256 of the dataset and the script.
Typical runs land at **90–98% of claims traced to a measured source**.

## Layout

    agents/       profile · literature · gaps · idea_critic · experiment · critic · write_paper
    pipeline/     ideate_loop · autopaper · refine_loop · validate_paper · provenance · compile_paper
    prompts/      constitution, per-section prompts, rubrics/, preamble.tex
    web/          console + run history + export API
    scripts/      web.sh (start/restart) · serve_pair.sh (both models) · publish_link.sh

## Running it

    bash scripts/serve_pair.sh          # writer :8097, critic :8098
    bash scripts/web.sh up              # console + public tunnel
    python pipeline/run_all.py --data <dir> --run <dir> --query "<search terms>"

## Status

Honest summary: five small datasets produce validated, provenance-traced papers. On a real
101k-row clinical dataset the system has so far **refused** to produce one — the critic
rejected every direction for confounded designs, invalid constructs, or methods that did not
match their claims. Those refusals look correct. Turning that into an accepted paper is the
open problem.
