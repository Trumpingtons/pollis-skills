# The sources

For each source: who can use it, what it measures, how to download the file Pollis imports, and what to watch for. The sites change their pages; when a step does not match what the user sees, find the button with them.

## SCImago Journal Rank (SJR)

- **Access**: free, no account. Data from Scopus.
- **Measures**: **SJR**, citations weighted by the prestige of the citing journal, over three years; the **SJR quartile**, the journal's best quartile among its subject categories; the journal's **h-index**. The `category` column lists every subject category with its quartile, such as `Economics and Econometrics (Q1); Statistics and Probability (Q2)`.
- **Download**: at scimagojr.com, **Journal Rankings**; choose the **year** in the selectors above the table (leave the subject area and country at **All**, to get every journal); then **Download data**, at the top right of the table. The file is `scimagojr YYYY.csv`, about 30,000 journals, with `;` between columns and decimal commas: Pollis reads it as it is.
- **Several years**: one file per year; download and import each. Pollis takes the year from the file name, so keep it.
- **Watch for**: SCImago lists a few journals twice, under an old and a new title.

## CWTS Journal Indicators (SNIP)

- **Access**: free, no account. Data from Scopus, computed by the Centre for Science and Technology Studies (CWTS), Leiden University.
- **Measures**: **SNIP** (source normalized impact per paper), citations per paper adjusted for how much the journal's field cites, so a value of 1 is about the average of any field, and journals of different fields can be compared; **IPP**, citations per paper, not adjusted.
- **Download**: at journalindicators.com, **Downloads**, then the Excel file of all the indicators. One file has every year, from 1999, with a `Year` column: about 600,000 rows, 30 to 50 MB. It is released once a year (`CWTS Journal Indicators March 2026.xlsx`).
- **Import**: takes a minute or two; the notification counts the rows as it goes.

## ABDC Journal Quality List

- **Access**: free, no account. From the Australian Business Deans Council.
- **Measures**: a rating of about 2,700 journals of business, economics, accounting, finance, management and related fields: **A\*** (the best), **A**, **B** or **C**, by expert panels, reviewed every three years (the 2025 list is the latest). Not a citation metric: journals outside the list are not rated, which says nothing of them.
- **Download**: at abdc.edu.au, **ABDC Journal Quality List**, the Excel file of the latest list (`ABDC-JQL-2025-....xlsx`). Its first sheet is the latest edition, with a `2025 rating` column; the other sheets are the earlier editions, which Pollis does not read.
- **Watch for**: `category` is the Field of Research (FoR) code of the journal (`FoR 3502`, banking, finance and investment), not a name.

## Scopus (CiteScore)

- **Access**: a free Elsevier account is enough for the source list with its metrics; the user's institution may give full Scopus.
- **Measures**: **CiteScore**, citations in four years to the documents of those four years, divided by their number; its **quartile** (or percentile) within each Scopus subject area.
- **Download**: at scopus.com/sources, signed in, the download of the source list with its CiteScore metrics (a link above the table, such as **Download Scopus Source List** or **Download CiteScore metrics**), an Excel file. When the page offers only an export of the rows shown, choose all the subject areas first.
- **Watch for**: the file has a row per journal and subject area; the `category` column says which. The year is in the header (`CiteScore 2024`), which Pollis reads.

## Journal Citation Reports (Impact Factor)

- **Access**: a Clarivate subscription, usually through the user's university library (sign in through the institution at jcr.clarivate.com). The Impact Factor is Clarivate's: it cannot be had elsewhere.
- **Measures**: the **Journal Impact Factor** (JIF), citations in a year to the items of the two previous years, divided by their number of citable items; the **5-year JIF**; the **JIF quartile** in each Web of Science category; the **Journal Citation Indicator** (JCI), normalised by field, where 1 is the world average.
- **Download**: at jcr.clarivate.com, **Journals**; filter by category or edition if the user wants only some; then **Export**, as CSV or Excel. JCR limits how many rows one export holds: for more, export the categories one at a time, and import each file.
- **Watch for**: the first lines of a JCR export are a title, not the columns; Pollis skips them. The year is in the header (`2024 JIF`).

## Other indicators

- **OpenAlex**, built into `journals`, without any import: works, citations, `mean_citedness_2yr` (close to a two-year impact factor, for every journal), `h_index`, `i10_index`, open access, DOAJ and article charges (`apc_usd`).
- **The Norwegian register**, also built in (`norwegian_register`, `norwegian_levels`): the level of about 40,000 journals and series each year, under NLOD 2.0.
- **Not importable** in Pollis today: Google Scholar Metrics (h5-index; no download), the Chartered ABS Academic Journal Guide, the Finnish levels (JUFO), ERIH PLUS. If the user needs one of these, look the journals up with them on the source's site.
