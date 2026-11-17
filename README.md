# atlas-healthcare: Atlas for a clinic

A finished Atlas: an internal assistant that answers from company documents, cites its sources, never shows a user a document they can't read, and opens tickets only after a person approves them. This build serves staff at a fictional community clinic. The company, people and policies are fictional.

What it shows: role-based policies, refusal of any patient health information.

| Read | |
|---|---|
| [Design doc](examples/healthcare/docs/design.md) | problem, users, architecture, trade-offs, eval results, risks |
| [Demo run](examples/healthcare/demo.md) | a transcript of real CLI and HTTP runs, copied verbatim |
| [Golden set](examples/healthcare/golden.json) | the eval cases the merge gate scores |

It is the same code as [atlas-starter](https://github.com/sivamatic/atlas-starter) with every week's solution applied. Only the data changes: `ATLAS_DATA_DIR=examples/healthcare`.

## Run it

You need Python 3.11 or later. After the install, everything runs offline: a deterministic stand-in model (`LLM_MODE=stub`) and hash embeddings, so no key and no network.

```
git clone https://github.com/sivamatic/atlas-healthcare.git
cd atlas-healthcare
python -m venv .venv
.venv\Scripts\activate          # macOS/Linux: source .venv/bin/activate
pip install -r requirements.txt

$env:ATLAS_DATA_DIR="examples/healthcare"; $env:LLM_MODE="stub"     # macOS/Linux: export ATLAS_DATA_DIR=examples/healthcare LLM_MODE=stub
python -m atlas.cli ask --user devon "Within how many business days must insurance claims be submitted?"
python tools/eval_gate.py --data-dir examples/healthcare --compare
python -m pytest -q
```

`eval_gate.py` prints recall, citation precision, refusal accuracy and the permission-leak count, and exits non-zero if any falls below `evals.thresholds.json`. CI runs the tests and the gate on every push (`.github/workflows/ci.yml`).

To use a real model, set `LLM_MODE=live` and `LLM_BASE_URL` to any OpenAI-compatible endpoint (see `.env.example`). The stub model tests retrieval, permissions and refusals, not a real model's writing.

## Make your own

Copy `examples/healthcare` to a new folder, write your documents with an `acl:` line each, write the users and at least ten golden cases, point `ATLAS_DATA_DIR` at it and run the gate.

---

Part of [AI for Engineers](https://ai4e.phenome.app). The course explains why each piece is built the way it is.
