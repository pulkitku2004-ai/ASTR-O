<div align="center">

# ASTR-O
## Aerosapce Systems Traceability & Observability
**Know exactly why your RAG pipeline failed — and prove it.**

Causal attribution and cryptographically signed audit reports for LLM pipelines in safety-critical systems.

<br/>

![Deterministic](https://img.shields.io/badge/evaluation-deterministic-blue)
![No LLM](https://img.shields.io/badge/eval%20path-no%20LLM-green)
![HMAC Signed](https://img.shields.io/badge/reports-HMAC--SHA256%20signed-orange)
![Safety Critical](https://img.shields.io/badge/ready-safety--critical-red)

</div>

---

## The Gap No One Talks About

Most RAG evaluation tools tell you *that* a response was wrong.  
None of them tell you *why* — and none of them can prove it.

> GPT-as-judge, embedding similarity, and RAGAS scores are non-deterministic.  
> Run the same query twice and you may get a different verdict.  
> That is not a proof. That is not an audit trail. That cannot satisfy a regulator.

When an LLM hallucinates in a general application, it is a bad user experience.  
When it hallucinates in an aerospace system, a legal review, or a safety-critical workflow — **it is a liability, and you have nothing to show for it.**

ASTR-O closes that gap.

---

## Why Not Just Use LangSmith, RAGAS, or TruLens?

Existing tools were built for general-purpose LLM observability. They log what happened. They score outputs. They do not produce proof.

| | LangSmith · Langfuse | RAGAS · TruLens · DeepEval | **ASTR-O** |
|---|---|---|---|
| **What it tells you** | That a call was made | That a score was low | Exactly which retrieval decision caused the failure |
| **Evaluation method** | Logging + traces | LLM-as-judge | Deterministic — no LLM in the eval path |
| **Same input, same verdict?** | Yes (logs don't change) | No — LLM evaluators vary | Yes — always |
| **Root cause attribution** | No | No | Yes — chunk, rank, tier, evidence trail |
| **Signed audit artifact** | No | No | Yes — HMAC-SHA256 per report |
| **Certification-ready** | No | No | Yes — DO-178C, AS9100D, IN-SPACe |
| **Actionable fix** | No | No | Yes — specific retrieval recommendation per flag |

> **The core difference:** observability tools are mirrors — they show you what the system did.  
> ASTR-O is an investigator — it tells you why it happened and what to change.

A 0.73 faithfulness score tells you something was wrong.  
A signed evidence trail tells you *Flight_Manual_v3.txt was ranked #3 instead of #1* — and gives you a fix.

---

## What Changes When You Use ASTR-O

**Before:**
- LLM produces a wrong answer → you know it was wrong, you don't know why
- You suspect retrieval but cannot pinpoint the exact failure
- Your evaluation gives different results each run — no repeatability
- You have no artifact to show a regulator, an auditor, or a client

**After:**
- Every query produces a signed verdict: `SAFE` or `FLAGGED`
- Every flagged answer includes the exact retrieval decision that caused it
- Every report is deterministic — same input, same verdict, always
- Every report carries a cryptographic signature — tamper-evident, verifiable proof

---

## What You Can Prove

ASTR-O does not just detect hallucinations. It produces a traceable evidence chain:

```
┌─────────────────────────────────────────────────────────┐
│  STATUS          FLAGGED                                 │
│  FAILURE         RETRIEVAL_FAILURE                       │
│  ROOT CAUSE      chunk_003                               │
├─────────────────────────────────────────────────────────┤
│  EVIDENCE TRAIL                                          │
│                                                          │
│  Step 1 → Query issued                                   │
│           "what is the nominal bus voltage?"             │
│                                                          │
│  Step 2 → Retrieval ranking                              │
│           chunk_003  (Test_Report_2024.txt, REFERENCE)   │
│           ranked #1  ·  rrf_score 0.91                   │
│                                                          │
│           Flight_Manual_v3.txt  (CRITICAL)               │
│           ranked #3 — never reached the context window   │
│                                                          │
│  Step 3 → Verdict                                        │
│           CRITICAL-tier source was outranked             │
│           LLM received compromised context               │
├─────────────────────────────────────────────────────────┤
│  RECOMMENDATION  Promote CRITICAL-tier sources in your   │
│                  retrieval scorer or increase top_k      │
├─────────────────────────────────────────────────────────┤
│  SIGNATURE       a3f8c2d1...  (HMAC-SHA256, 64-char)     │
│                  verifiable, tamper-evident proof the    │
│                  report originated from ASTR-O           │
└─────────────────────────────────────────────────────────┘
```

This is the artifact. Not a score. Not a probability. A signed, deterministic evidence chain that tells you exactly what went wrong, which document caused it, and what to fix.

---

## Who This Is Built For

**Aerospace & Defense**

DO-178C and AS9100D require you to prove *which document version* grounded each AI-generated claim — not that the system was "usually right." ASTR-O produces the signed audit artifact that satisfies a traceability review. No LLM in the evaluation path means the evaluator never introduces its own non-determinism.

**Legal & Compliance**

Document review, contract analysis, due diligence workflows where a wrong answer has real legal consequences. ASTR-O tells you not just that the answer was wrong, but which source document was missing or misranked — actionable, not just observational.

**Space Systems**

IN-SPACe and emerging AI governance frameworks for space operations require traceable evidence chains for any AI output that informs an operational decision. ASTR-O produces that chain, cryptographically signed, per query.

**Any Team Running RAG in Production**

If you are deploying a RAG pipeline and need to understand when to trust its output — and explain that trust to someone else — ASTR-O gives you the infrastructure to do that.

---

## What You Get Per Query

| Output | Description |
|---|---|
| `status` | `SAFE` · `FLAGGED` · `PARTIAL_EVALUATION` · `ERROR` |
| `signed_report` | HMAC-SHA256 signed audit report — tamper-evident |
| `causal_chain` | Root cause chunk + full evidence trail (FLAGGED only) |
| `hallucination_score` | 0–1 · likelihood the answer is ungrounded |
| `retrieval_quality` | Ranking health of the retrieved context |
| `faithfulness` | Answer-to-source alignment |
| `token_confidence` | LLM certainty on critical claim tokens |
| `overall_safety_score` | Composite verdict score |

---

## How to Connect

ASTR-O sits at the output of your existing RAG pipeline.
You send three things: the query, the documents your retriever returned, and your LLM's response.
ASTR-O returns a signed verdict and — when something is wrong — the exact retrieval decision that caused it.

```
  Your RAG pipeline output
          ↓
    process_span()
          ↓
  {status, signed_report, causal_chain, metrics}
```

One call. One result. Integration typically takes less than a day.

No changes to your retriever. No changes to your LLM. ASTR-O wraps around what you already have.

Full integration documentation is shared after access is granted.

---

## Compliance

| Standard | What ASTR-O provides |
|---|---|
| **DO-178C** | Source document → retrieval rank → context window → token confidence → verdict, per query |
| **AS9100D** | Document version traceability for every AI-generated output |
| **IN-SPACe** | Audit-ready evidence chain for space system AI outputs |

The evaluation path contains no LLM — the same input always produces the same verdict. This is a prerequisite for any certification process that requires repeatable, auditable results.

---

<div align="center">

## Access

ASTR-O is a private SDK.  
For access, integration support, or licensing inquiries:

📧 **pulkitku2004@gmail.com**

