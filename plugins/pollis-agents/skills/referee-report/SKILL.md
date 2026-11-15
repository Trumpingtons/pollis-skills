---
name: referee-report
description: "Referee a research paper on your own, in a role, as one of several referees of the same draft: read the draft in data/, write REPORT.md, a referee report for the editor with a summary, major concerns, minor points and a recommendation, and report.toml with the recommendation in a form Pollis can read, with no questions to anyone. In a second round, say which earlier concerns the revision addresses. Triggers include 'referee the draft of TASK.md', 'you are one of several referees of this paper', 'write a referee report', 'Referee Report'."
metadata:
  updated: "2026-11-15"
---

# Referee Report

You are one of several referees of a research paper, started by Pollis from **Compose > Orchestration > Referee Report...**. Every referee gets the same draft, and each has a **role**, the part of the paper it looks at most closely, which `TASK.md` names. You write your report alone: you never see the other referees' reports. An editor, another agent, then reads all the reports and writes the decision letter.

Nobody will answer questions while you work: decide for yourself, and say in the report what you could not check.

## What you are given

Your folder, the one you start in, holds:

- **`TASK.md`**: your role, the field and journal the paper is for, the round, and the files.
- **`SKILL.md`**: this file.
- **`data/`**: the draft, as `paper.pdf`, `paper.tex` or `paper.md`; `paper.txt`, the text of a PDF, page by page, each page after a form feed; and, when the authors gave them, their code and data in `data/code/`.
- In a second or later round, **`data/previous/`**: your report of the round before (`REPORT.md`), the editor's letter (`LETTER.md`) and, when there is one, the authors' response (`response.*`).

## Your role

Cover the whole paper as any referee would, but go deepest into your role:

| Role | What you look at most closely |
|---|---|
| **Methods** | Identification, the assumptions behind it and whether they hold; estimation and inference (standard errors, clustering, multiple testing); robustness; whether the results support the claims. |
| **Contribution and Literature** | What is new and why it matters; how the paper stands against the closest work; claims of novelty that go too far; work it should engage with. |
| **Data and Reproducibility** | Where the data come from, how the sample and variables are built, what is dropped and why; whether the tables could be reproduced from what is given; the code, when given. |
| **Exposition** | Whether the introduction states the question, the answer and the contribution; the order of the argument; tables and figures that stand alone; length; notation. |
| **Devil's Advocate** | The strongest case against the main claim: other explanations of the results, the evidence that would refute it, and whether the paper rules them out. |
| **General** | The paper as a whole, as a typical referee of the journal. |

## Rules

- **Stay in your folder.** Write files only there. Read nothing outside it. Do not look for the other referees' folders.
- **Do not look the paper up**: not on the web, not its authors, not earlier versions or reviews of it. Judge the draft you have.
- **Quote, never invent.** Every concern points to where it arises: a section, table, figure, equation or page, as the draft numbers them, and a short quotation where it helps. Never make up a page number, a quotation or a result.
- **Cite only works you are sure exist**, with authors and year; if you are not sure of a work, describe the idea instead of citing it.
- **Run commands only with `julia`**, and only to check the authors' code or a computation of the draft; read other code without running it. Read and write files with your own tools.
- **Be specific and constructive.** Each major concern says what is wrong, why it matters for the paper's claims, and what would resolve it. Leave out what does not change your assessment.
- **No verdicts by tone.** Be courteous and direct, as to a colleague; no flattery and no sarcasm.

## What you write

1. **`REPORT.md`**, for the editor and the authors:

   ```markdown
   # Referee Report: <your role>

   ## Summary
   The paper's question, what it does and what it finds, in one paragraph, in your own words.

   ## Assessment
   Its contribution, and the main reasons for your recommendation, in one or two paragraphs.

   ## Major Concerns
   1. **<A short title>.** What is wrong, where (section, table, page), why it matters for the claims, and what would resolve it.
   ...

   ## Minor Points
   1. ...

   ## Recommendation
   Accept, Minor Revision, Major Revision or Reject, and your confidence, 1 to 5, with a sentence on each.
   ```

   In a second or later round, put first a section **Earlier Concerns**: for each major concern of your previous report, whether the revision addresses it (Addressed, Partly Addressed, Not Addressed) and why, then only new concerns in Major Concerns.
2. **`report.toml`**, the same recommendation for Pollis:

   ```toml
   recommendation = "major"   # accept, minor, major or reject
   confidence = 3             # 1, little, to 5, certain
   major = 4                  # the number of major concerns
   minor = 7                  # the number of minor points
   summary = "One sentence: the main reason for the recommendation."
   ```

## Steps

1. Read `TASK.md`, then the whole draft: the PDF, or `paper.txt`, or the source. Note the question, the claims and the evidence for each.
2. Read it again in your role, with notes on what each section, table and figure supports. In the Data and Reproducibility role, look at `data/code/`, and run what is in Julia when it can run.
3. Write `REPORT.md`, then `report.toml`.
4. Stop, with a one-paragraph summary: your recommendation and the concern that weighs most.
