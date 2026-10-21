---
name: journal-metrics
description: "Add journal metrics to the user's PollisJournals database: SJR (SCImago), SNIP (CWTS), CiteScore (Scopus), the Journal Impact Factor (Journal Citation Reports) and the ABDC Journal Quality List's ratings. The user downloads each file for their own use, Pollis imports it on their computer (the Journal Metrics page, or Databases view > PollisJournals > Import Journal Metrics...), and the agent then answers questions about journals with SQL. Triggers include 'add impact factors to Pollis', 'import SJR', 'get the CiteScore of my journals', 'which quartile is this journal in', 'is this journal A* on the ABDC list', 'compare these journals', 'is this a good journal', 'update my journal metrics'."
---

# Journal Metrics

**PollisJournals**, a built-in database of Pollis, has about 250,000 journals, conferences and book series from OpenAlex, with OpenAlex's own indicators (works, citations, 2-year mean citedness, h-index), and the levels of the Norwegian register (0, 1, 2), which are open data. The metrics most used to judge journals (SJR, SNIP, CiteScore, the Impact Factor, the ABDC rating) are not free to share, so Pollis does not ship them: **each user downloads them from their source, for their own use, and Pollis imports that copy on their computer.** Nothing is sent anywhere.

You help the user (1) choose the metrics that fit their field and their access, (2) download each file, (3) import it in Pollis, and (4) use the metrics: check the import, and answer questions about journals with SQL. The user does every login and every click in Pollis; never ask for a password, and never enter one.

## Never redistribute the files

The files and Pollis's copies of them (`~/.pollis/journals/metrics/*.jsonl`) are for the user's own use. Never commit them, upload them, attach them to an issue or a message, copy them into a toolbox, method, workflow or shared folder, or put their rows in anything the user will publish beyond what a citation needs (a journal's quartile in a CV or a report is fine; a table of a whole source is not). Download only with the source's own download button or link, never by scraping its pages.

## 1. Which metrics

Ask the user's field and what they need the metrics for (choosing where to submit, a CV, a promotion or grant application, a literature review). Then suggest, from [reference.md](reference.md):

| Source | Metrics | Access |
|---|---|---|
| SCImago Journal Rank | SJR, SJR quartile, h-index | Free, no account |
| CWTS Journal Indicators | SNIP, IPP | Free, no account |
| ABDC Journal Quality List | A*, A, B, C | Free, no account |
| Scopus | CiteScore, its quartile | A free Elsevier account |
| Journal Citation Reports | JIF, 5-year JIF, JIF quartile, JCI | A Clarivate subscription, usually the user's library |

- **SCImago and CWTS** are the place to start: free, broad (all fields), and the two together give a prestige measure (SJR) and a field-normalised one (SNIP).
- **ABDC** in business, economics, accounting, finance and management, where many schools (in Australia and beyond) judge journals by its rating.
- **JCR** when the user's institution or funder asks for the Impact Factor, which is common in medicine, the natural sciences and some business schools.
- **CiteScore** when the user's institution uses Scopus.
- In the **humanities**, citation metrics cover journals poorly: say so, and do not judge a humanities journal by a missing or low metric.

## 2. Download

Follow the source's steps in reference.md, with the user. The user downloads in their browser: the sources accept their own download buttons, not programs (SCImago turns programs away), and Scopus and JCR need the user's login. The sites change their pages: if a button is not where the steps say, ask the user what they see and find it with them.

Keep the file name the source gives: Pollis takes the year from it when the file has no year column (SCImago's `scimagojr 2025.csv`).

## 3. Import

The user does this in Pollis, on the **Journal Metrics** page (Databases view > right-click **PollisJournals** > **Journal Metrics**, or the info button next to **Journals** on the RSS Feeds page). It has a card per source, with what it measures, its **Download Page**, and what is imported; **Import File...** imports, and **Remove** deletes an import. Or:

1. In the **Databases** view (secondary side bar, on the right), right-click **PollisJournals** itself, not one of its tables, and choose **Import Journal Metrics...**. Or, in the Command Palette, **Databases: Import Journal Metrics...**.
2. Choose the downloaded file (CSV or Excel). Pollis tells the source by its columns; when the file says nothing of the year, it asks for it.
3. A notification says how many rows were imported. Then **Refresh** PollisJournals.

Importing the same file again replaces its rows, so a corrected download can simply be imported again. Each year of SCImago is a separate file, a separate import; CWTS has every year in one file; the ABDC list's file has every edition, and Pollis reads its first sheet, the latest.

Once imported, the metrics also show on the **RSS Feeds** page (Explore > RSS > RSS Feeds) and the welcome page: each item of a journal has a tag with the journal's Norwegian level, OpenAlex's 2-year mean citedness and, with SCImago or JCR imported, the SJR or JIF quartile and its year; the pointer on the tag shows every value with its source and year. The page's **Journals** filter offers Academic (level 1 or 2), Leading (level 2) and Open Access from the start, and SJR Q1, SJR Q1–Q2 (with SCImago) and SNIP Above 1 (with CWTS) once imported; items not from a journal always stay. Point the user there when they want to follow only the stronger journals of their feeds and email alerts.

If Pollis says the file is not one it can import, check that it is the export described in reference.md (not a page saved from the browser, nor a file opened and re-saved with other columns). If it is, the source has changed its columns: ask the user to report it at github.com/saragga/pollis/issues with the source, the edition and the file's header row (the first row only, not the data).

## 4. Use the metrics

The views of PollisJournals:

- `metrics`: the rows imported, a row per journal, year, source and, for some sources, subject category: `title, category, issn, eissn, year, source`, then `jif, jif_5year, jif_quartile, jci, citescore, citescore_quartile, sjr, sjr_quartile, h_index, snip, ipp, abdc_rating, imported`. For ABDC, `category` is the list's Field of Research code (`FoR 3502`).
- `journal_metrics`: the same, with `journal_id`, the journal's `id` in `journals`, found by ISSN. It is empty for a row whose ISSN no journal in OpenAlex has (many book series and conference proceedings).
- `journals`, `journal_years`, `journal_issns`: OpenAlex's journals, their works and citations by year, and every ISSN.
- `norwegian_register`, `norwegian_levels`: the journals and series of the Norwegian register (with `journal_id`), and their level each year since 2004: 0 not academic, 1 academic, 2 leading, X under review. Built in, no import: a quick answer to "is this journal academic, and is it among the leading ones?" in any field, the humanities included.
- `articles`: the items of the user's email alerts, with their DOI, journal and citations.

Query read-only. In Pollis, the user can run SQL in the `duckdb>` terminal (Databases view > **New DuckDB Terminal**), where PollisJournals is attached; or you run it yourself with DuckDB on `~/.pollis/databases/PollisJournals.duckdb`, opened read-only. Examples:

```sql
-- Check an import: rows and years by source
SELECT source, min(year), max(year), count(*) FROM metrics GROUP BY source;

-- A journal across sources, newest first (match the title loosely, then by ISSN)
SELECT year, source, category, sjr, sjr_quartile, snip, citescore, jif, jif_quartile
FROM journal_metrics WHERE title ILIKE '%econometrica%' ORDER BY year DESC, source;

-- The Norwegian level of a journal, by year
SELECT r.title, l.year, l.level FROM norwegian_register r JOIN norwegian_levels l USING (id)
WHERE r.title ILIKE '%econometrica%' ORDER BY l.year DESC;

-- The quartiles of the journals the user's email alerts came from
SELECT a.journal, m.year, m.sjr_quartile, m.snip
FROM articles a JOIN journal_metrics m ON m.journal_id = a.journal_id
WHERE m.source = 'SCImago' ORDER BY m.year DESC;
```

When you report a metric, always say its source and year, and the subject category of a quartile: a journal can be Q1 in one category and Q2 in another, and SCImago lists every category in one cell. Show the values; do not round a journal into "good" or "bad". Compare journals only within the same field, and prefer SNIP when the fields differ.
