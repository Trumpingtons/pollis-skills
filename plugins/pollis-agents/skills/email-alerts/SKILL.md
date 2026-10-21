---
name: email-alerts
description: "Set up email alerts at Google Scholar, JSTOR, HeinOnline, Nexis Uni, SSRN, Semantic Scholar, Web of Science, Scopus, Westlaw and Factiva, sent to the user's own alerts mailbox, which Pollis reads and turns into one RSS feed per alert (Explore > RSS > Add Feed > Email Alerts...). Writes precise searches in each source's syntax, so the alerts bring few false hits. Triggers include 'set up a Google Scholar alert', 'get alerts from JSTOR in Pollis', 'turn my search into a feed', 'my alerts bring too much noise', 'set up my alerts mailbox', 'follow new papers on a topic by email'."
---

# Email Alerts

Many sources of articles, cases and news send the results of a saved search only by email. Pollis turns those emails into feeds: the user sends the alerts to an address of their own, the **alerts mailbox**, and Pollis reads it on their computer over IMAP, read only, making one feed per alert under the topic **Email Alerts** of **Explore > RSS > RSS Feeds**. Nothing passes through Pollis or any other service.

You help the user (1) set up the alerts mailbox, (2) write a precise search for each topic, in the syntax of the source, and (3) create the alert at the source, sent to the alerts mailbox. The user does every click and every login: never ask for a password, and never enter one.

The sources Pollis reads: Google Scholar, JSTOR, HeinOnline, Nexis Uni, SSRN, Semantic Scholar, Web of Science, Scopus, Westlaw and Factiva. The details of each are in [reference.md](reference.md). If a source offers an RSS feed for a search (arXiv, PubMed, many journals), use that instead, with **Add Feed** in Pollis: it needs no mailbox.

## 1. The alerts mailbox

Ask whether the user already has an address for alerts. If not, recommend a new one used for nothing else, as Pollis makes a feed of every alert that arrives there and the alerts stay out of their own inbox. A free Gmail account is the simplest:

1. Create the account. Turn on **2-Step Verification** (myaccount.google.com > Security), then make an **app password** at myaccount.google.com/apppasswords: Pollis uses it instead of the account's password. IMAP is on in Gmail by default.
2. iCloud Mail and Yahoo Mail also need an app password; Fastmail and most university or work mail servers take the usual password, or an app password if 2-step login is on. Outlook.com and Hotmail accounts do **not** work: Microsoft no longer lets programs open them with a password.
3. In Pollis: **Explore > RSS > Add Feed > Alerts Mailbox...**: the address, the IMAP server (Pollis suggests it for Gmail, iCloud, Yahoo, Fastmail and GMX) and the app password, which Pollis keeps in the system's keychain.

Pollis reads the folder INBOX. If the user files the alerts into another folder or Gmail label, set `pollis.rss.alertsMailbox` > `folder` in the settings to its name.

### When a source sends alerts only to the account's address

Several sources (JSTOR, SSRN, Semantic Scholar, Web of Science, Scopus) send alerts to the email of the account the user is signed in with. Two ways:

- **Sign up at the source with the alerts address** (best for free accounts: SSRN, Semantic Scholar, JSTOR personal accounts).
- **Forward automatically**: a rule in the user's own mail that forwards the source's emails to the alerts address (in Gmail: Settings > Forwarding and POP/IMAP > Add a forwarding address, then a filter on the sender with "Forward it to"). Automatic forwarding keeps the source as the sender, which is how Pollis tells the source. Forwarding one email by hand also works when its articles link to the source's own site.

## 2. A precise search

The user's first complaint about alerts is noise: a few words in Google Scholar match thousands of loosely related papers. Before creating an alert, write the search with the user:

1. Ask what they want to follow, and for two or three papers that are exactly on topic and one that is not.
2. Write the search in the source's syntax (reference.md): phrases in quotes, words in the title rather than anywhere, the field's own vocabulary, the words that cause false hits excluded, and where the source allows, limits to journals, authors or subjects.
3. Run it at the source with the user and look at the first results. Tighten it until most of the first page is on topic; an alert sends what is new for that search, so its first page is a fair sample.
4. One alert per topic: each becomes its own feed in Pollis, named after the email's subject, so a clear search makes a clear feed name.

## 3. Create the alert

Follow the source's steps in reference.md. The pages change: if a button is not where the steps say, ask the user what they see and find it with them. Once the alert exists:

- Some sources send a confirmation email first (Google Scholar, for an address that is not the Google account's). The user opens it in the alerts mailbox and confirms.
- The first alert email arrives when there is something new: from a day to a week. Pollis finds it the next time it reads the feeds (every hour, or **Refresh**), and the alert appears in **Add Feed > Email Alerts...**, under its source, to tick.

## When an alert shows no articles

Pollis reads Google Scholar's emails from their own layout, and the other sources' from the links to their articles. The readers of sources other than Google Scholar were written before their emails were seen, so a source's emails may be missed. Then ask the user to save one of the emails (as `.eml`), and report it at github.com/saragga/pollis/issues with the source's name, after removing anything personal from it.
