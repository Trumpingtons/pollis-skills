---
name: references
description: "Cite and list references in Pollis documents: wikis and notebooks (Markdown) and LaTeX documents (reports, write-ups, the Tour). Every citation is Author (year) with all authors written out, linked to its entry in the References, and every entry comes from the Pollis library, pollis.bib, with its DOI link. Triggers include 'add references', 'cite this paper', 'fix the citations', 'write the References section', 'link the citations', 'add a DOI', 'check the references of this wiki/notebook/document'."
---

# References

How Pollis documents cite their sources. One set of rules for every format; only the markup differs.

## The rules

1. **Author (year).** Cite as `Breiman (2001)` or, inside a sentence that already has brackets, `(Breiman 2001)`. Write out **every** author, never "et al.": `Breiman, Friedman, Olshen and Stone (1984)`, `Dickson, Grambsch, Fleming, Fisher and Langworthy (1989)`. Two authors: `Ljung and Box (1978)`. No comma before the last "and".
2. **Linked.** Each citation links to its entry in the References section at the end of the document.
3. **A DOI link** on every entry that has a DOI (`https://doi.org/<doi>`); otherwise the publisher's or the author's link, when there is one.
4. **Cited ⇔ listed.** Every work cited is in the References, and every entry in the References is cited at least once. A document's References lists only what it cites, never the whole library.
5. **Methods keep their names.** "Breslow's ties", "Efron's rule", "Baltagi's Table 2.5" stay as they are; the work behind them is cited with its year where it is first used: "the rule of Breslow (1974)".
6. **Never invent a reference.** Every entry comes from the Pollis library (below) or from the publisher's page or DOI record you have actually read. If you cannot verify a work (title, year, journal, volume, pages), say so to the user instead of guessing.

## The Pollis library: pollis.bib

Pollis keeps one BibTeX file with every work referenced anywhere in Pollis, cited or not, one entry each: `pollis.bib`. It is **generated** by Pollis' build from each panel's `[[references]]` (in its `.toml`, the list shown in Learn More › Explore References) and from the articles of the Compose notebooks (workflows, benchmarks, methods). Each entry has a readable key, first author and year (`breiman2001`; `munnell1990a`, `munnell1990b` when one author has two works in a year), and a `pollis-used-in` field naming the panels and notebooks that use it.

Where to find it:
- in the Pollis source repository: `src/vs/workbench/contrib/model/browser/media/references/pollis.bib`;
- otherwise, download it: `https://raw.githubusercontent.com/saragga/pollis/main/src/vs/workbench/contrib/model/browser/media/references/pollis.bib`.

**Never edit pollis.bib**: the next build overwrites it. To add or correct a reference:
- **for a panel** (its wiki, notebook tutorials or Explore References): add or correct the entry in the panel's `.toml`, `[[references]]`, with `title`, `authors` (`Last, First; Last, First`), `year`, `journal` (or the publisher, for a book), `doi` or `url`, and `openAccess`. The library follows at the next build, and the work also appears in Explore References. A wiki or notebook of a panel cites only works in its panel's `[[references]]`: add a missing one there first.
- **for a Compose notebook**: its article, in the notebook's `pollis.workflow.article` metadata (`citation`, `doi`, `bibtex`).
- **for any other document** (a report, a write-up): its own References, and its own `.bib` if it uses BibTeX. Look the work up in pollis.bib first and copy that entry, so the same work has the same key everywhere.

The build lists, in `.build/pollis-bib-conflicts.txt`, the works whose panels disagree (year, journal or authors) and the DOIs given to two different works. When you touch a reference that is listed there, correct it at its source.

Leave the user's own references alone: their additions and corrections in Explore References live in `~/.pollis/references/<panel>.bib` and are theirs.

## Markdown: wikis and notebooks

In the text, link the citation to the entry's anchor:

```markdown
Random forests grow each tree on a bootstrap sample ([Breiman (2001)](#ref-breiman2001)) ...
As [Breiman, Friedman, Olshen and Stone (1984)](#ref-breiman1984) show, ...
```

At the end, a `## References` section (before `## See Also` in a wiki), one paragraph per entry, sorted by first author and year, each starting with its anchor (`ref-` and the pollis.bib key):

```markdown
## References

<a id="ref-breiman1984"></a>Breiman, L., Friedman, J. H., Olshen, R. A. and Stone, C. J. (1984). *Classification and Regression Trees*. Wadsworth.

<a id="ref-breiman2001"></a>Breiman, L. (2001). Random forests. *Machine Learning* 45(1), 5–32. [doi:10.1023/A:1010933404324](https://doi.org/10.1023/A:1010933404324)
```

The entry: authors (last name, initials), (year), title, *journal or book in italics*, volume(issue), pages, then the DOI link (or the URL).

**In a notebook**, the References are the last Markdown cell. A link to an anchor in another cell may not jump in every notebook viewer, so in notebooks the citation links straight to the work's DOI (or URL) instead: `[Breiman (2001)](https://doi.org/10.1023/A:1010933404324)`; the References cell still lists every work cited.

Wikis are general reference: cite the literature, never the panel's example data (see the wiki rules of create-panel).

## LaTeX

With BibTeX (preferred: reports, longer documents), natbib and hyperref give linked Author (year) citations and DOI links:

```latex
\usepackage[round]{natbib}
\usepackage{hyperref}
\providecommand{\doi}[1]{\href{https://doi.org/#1}{doi:#1}}
...
\citet*{breiman1984} show ...        % Breiman, Friedman, Olshen, and Stone (1984): every author
... (\citealt*{breiman2001}) ...
\bibliographystyle{plainnat}
\bibliography{pollis}                 % only the cited entries are printed
```

Use the starred forms (`\citet*`, `\citep*`, `\citealt*`) so every author is written out. In Pollis, Compile LaTeX and the reports find pollis.bib without a copy: PollisTeX passes its folder to Tectonic (`compile("report.tex"; searchpath = [folder])`). Elsewhere, copy `pollis.bib` next to the document, or write a `.bib` with just the entries it cites, taken from pollis.bib.

Without BibTeX (short standalone documents, such as the New Methods write-ups), define a citation and an entry macro, and write the References by hand in the format above:

```latex
\newcommand{\refcite}[2]{\hyperlink{ref:#1}{#2}}
\newcommand{\refentry}[2]{\raisebox{1.1em}[0pt][0pt]{\hypertarget{ref:#1}{}}#2\par}
\newcommand{\refdoi}[1]{\href{https://doi.org/#1}{doi:#1}}
...
\refcite{breiman2001}{Breiman (2001)} ...
\section*{References}
\refentry{breiman2001}{Breiman, L. (2001). Random forests. \emph{Machine Learning} 45(1), 5--32. \refdoi{10.1023/A:1010933404324}}
```

The `[0pt][0pt]` keeps the anchor from adding space to each line. Keys are the pollis.bib keys.

## Check before you finish

- Every citation is Author (year), with all authors, and links to an entry (Markdown: `#ref-<key>` exists; LaTeX: no undefined citations or references in the log).
- Every entry in the References is cited; none is listed twice.
- Every entry with a DOI has its DOI link, and each link opens (`https://doi.org/...`).
- The years, titles and authors match pollis.bib, or, for a work not in it, the source you read.
- For a panel's wiki or notebook: every work cited is in the panel's `[[references]]`.

Tell the user what you cited, what you added to the panel's `[[references]]` or the notebook's metadata, and any reference you could not verify.
