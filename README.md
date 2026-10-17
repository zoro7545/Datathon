# Datathon
# ProofChain AI

**Team:** 404! Founders | **IBM Z Datathon 2026** | **Event code:** SSF2026
**Challenge:** AI Secured: Innovation Without Exposure

> A trust check for files: ProofChain AI tells a reviewer whether a report is safe to use, and why, without exposing its contents.

## Problem
Organizations make decisions from spreadsheets and reports, but a value can be silently changed, a source can go missing, or two versions can conflict, and nobody notices until the decision is wrong. A plain "the file differs" check can't tell a legitimate, documented edit from a silent one.

## Solution
1. **Hash chain:** each approved file is SHA-256 hashed into a chain signed with HMAC. Any change to a file or block is detected.
2. **Features:** 10 signals comparing a received file with the registered master (values changed with no update trail, missing provenance, date-order violations, duplicate IDs, row-count change).
3. **ML:** a Random Forest trained on 200 labelled versions (100 documented edits, 100 silent tampering) plus an Isolation Forest for anomalies.
4. **Trust score:** 0-100 and a verdict: Ready for review, Needs human verification, or Reject.
5. **Explanation:** a small open-source LLM (flan-t5-base) writes a plain-English reason from the findings only. It never sees the data rows.

## IBM Z / LinuxONE
- **Used:** the whole notebook was built and run in Jupyter Lab on a LinuxONE Community Cloud instance (s390x), with scikit-learn for the models and hashlib/hmac for the chain.
- **Planned (not in the prototype):** HMAC signing key held in IBM Z hardware crypto (ICSF), and on-chip crypto acceleration (CPACF) for hashing at scale.

## Results
Tested on Superstore sales data with simulated tampering.

| Edit size | Silent value change | Missing provenance | Date shift | Row drop / duplicate |
|---|---|---|---|---|
| 0.1% of rows | missed by ML | missed by ML | caught | partly caught |
| 0.5% or more | caught | caught | caught | caught |

- The hash chain flags **every** modified file, including the 0.1% edits the ML misses. ML plus hash together caught 100% in our tests.
- Documented (registered) edits caused **0% false alarms**.
- The data is synthetic tampering, so these numbers show the approach works, not real-world accuracy.

## How to run
1. Open the notebook in Jupyter Lab (we used LinuxONE).
2. Install: pandas, numpy, scikit-learn, transformers, torch, ipywidgets.
3. Run the cells from top to bottom.
4. In the loader cell, choose a CSV or Excel file and click **Load dataset**.
5. Run the cells below it to see three verdict cards: original, documented edit, silent edit.

## Data
- Superstore Sales (public, Kaggle): [paste link]
- The provenance columns (Source_System, Last_Modified_By, Last_Updated) and all tampered versions are simulated by our team.

## Built with
pandas, NumPy, scikit-learn, Hugging Face Transformers (flan-t5-base), ipywidgets, Python hashlib and hmac.

## Limitations and next steps
- Works on tables with a date column and a numeric amount column.
- The demo HMAC key is in code. In production it belongs in hardware crypto (ICSF).
- The small LLM only rephrases the findings. A larger model such as one on watsonx.ai could give richer explanations.
- Next: read file metadata (author, modified time), PDFs and Excel formulas.

  
IBM Z/Linuxone 
