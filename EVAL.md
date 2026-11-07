# Evaluation test set

Fill this in after every refresh, before you publish. The agent is the system under test.
You are the grader. Nothing here is automated on purpose: the point is to decide, with a
source you trust, whether the agent told you the truth.

**Ground truth sources.** Pick one and use the same one every time, so your scores are
comparable week to week:

- Prices: Google Finance (google.com/finance), Yahoo Finance, or your brokerage app.
- News: the article itself. Open the link. Check the date on the page.
- Calendar: the company's investor relations page (search "TICKER investor relations
  events"), or the Fed's own meeting calendar for FOMC dates.

Refresh date: 2026-11-06    Grader: number-checker agent

## A. Prices (one row per ticker)

Pass if the dashboard's last price is within $0.10 of the ground truth for the same day
and the same price type (close vs intraday). If the agent recorded an intraday price,
compare against the intraday price at about the same time; do not compare it to a close.

| Ticker | Date shown | Price type shown | Dashboard price | Ground truth price | Source you used | Pass / Fail |
|---|---|---|---|---|---|---|
| NVDA | 2026-11-06 | close | 186.64 | 186.64 (all 5 closes Nov 2-6 match: 202.06, 206.69, 200.13, 181.60, 186.64) | Yahoo Finance https://finance.yahoo.com/quote/NVDA/history/ and https://stockanalysis.com/stocks/nvda/history/ | Pass |
| JPM | 2026-11-06 | close | 334.49 | 334.49 (all 5 match: 340.58, 337.98, 352.11, 337.34, 334.49) | Yahoo Finance https://finance.yahoo.com/quote/JPM/history/ and https://stockanalysis.com/stocks/jpm/history/ | Pass |
| ACN | 2026-11-06 | close | 260.50 | 260.50 (all 5 match: 260.94, 254.56, 264.27, 265.16, 260.50) | Yahoo Finance https://finance.yahoo.com/quote/ACN/history/ and https://stockanalysis.com/stocks/acn/history/ | Pass |
| C | 2026-11-06 | close | 119.05 | 119.05 (all 5 match: 123.10, 122.71, 124.74, 118.45, 119.05) | Yahoo Finance https://finance.yahoo.com/quote/C/history/ and https://stockanalysis.com/stocks/c/history/ | Pass |
| LLY | 2026-11-06 | close | 1354.00 | 1354.00 (all 5 match: 1410.99, 1409.38, 1417.99, 1364.73, 1354.00) | Yahoo Finance https://finance.yahoo.com/quote/LLY/history/ and https://stockanalysis.com/stocks/lly/history/ | Pass |

Note: Google Finance could not confirm any date. The fetch of https://www.google.com/finance/quote/NVDA:NASDAQ returned "Google Finance isn't supported on this device" with no prices, so I did not try the other four. Yahoo Finance was the other named source for every ticker; StockAnalysis (the brief's own source) agreed. `latest` is null for all tickers, so no intraday check. The page text was read through a summarizing fetch tool, not a browser.

## B. Weekly change (one row per ticker)

The page computes this from the five closes. Pass if it is within 0.3 percentage points of
your own calculation: (last price minus the close five trading days earlier) divided by
that earlier close.

| Ticker | Dashboard weekly change | Your calculation | Pass / Fail |
|---|---|---|---|
| NVDA | -7.63% (186.64 vs 202.06; CHANGELOG says -7.6%) | -7.63% from verified closes | Pass |
| JPM | -1.79% (334.49 vs 340.58; CHANGELOG -1.8%) | -1.79% | Pass |
| ACN | -0.17% (260.50 vs 260.94; CHANGELOG -0.2%) | -0.17% | Pass |
| C | -3.29% (119.05 vs 123.10; CHANGELOG -3.3%) | -3.29% | Pass |
| LLY | -4.04% (1354.00 vs 1410.99; CHANGELOG -4.0%) | -4.04% | Pass |
| NVDA (typed percentage check) | BRIEF.companies.NVDA.news[0].why contains "Nvidia fell 9.3% on Thursday" | n/a (CLAUDE.md rule 6 says never type a percentage; the 9.3% is a one-day move quoted from the CNBC article and matches 200.13 to 181.60 = -9.26%, not the weekly change) | Fail |

| NVDA (typed percentage check, REGRADE after fix) | BRIEF.companies.NVDA.news[0].why now reads "Nvidia fell sharply on Thursday as AI and chip stocks sold off on doubts about the pace of the data center buildout after Democrats won Congress." No percentage anywhere in BRIEF (lines 183-273 of index.html re-read). CHANGELOG percentages unchanged and still match. | n/a (original Fail above kept) | Pass |

Note: the typed-percentage row is a strict reading of the instruction. It is a quoted daily move, not a weekly change, so the owner may decide the rule does not cover it. No other percentage appears in BRIEF. CHANGELOG percentages all match.

## C. News grounding (one row per news item, three per ticker)

Open every link. Pass only if all four are true: the link opens, the headline matches the
article, the article date is within the last seven days, and the "why it matters" line is
supported by the article (not just plausible).

| Ticker | Headline (first few words) | Link opens | Headline matches | Dated within 7 days | "Why" is supported | Pass / Fail | Source link |
|---|---|---|---|---|---|---|---|
| NVDA | AI stocks hammered by midterm shock... | Pass | Pass | Pass (Nov 6, 2026) | Pass (9.3% drop, Democrats won Congress, data center doubts) | Pass | https://www.cnbc.com/2026/11/06/ai-data-center-midterms-selloff.html |
| NVDA | Nvidia's Huang is asked to brief Congress... | Pass | Pass | Pass (Nov 5, 2026) | Pass (House Republicans asked for closed-door briefing, China concerns) | Pass | https://www.cnbc.com/2026/11/05/nvidia-jensen-huang-china-spy-congress.html |
| JPM | Democrats to probe JPMorgan's relationship with Epstein... | Pass | Pass | Pass (Nov 5, 2026) | Pass (probe "has not been announced") | Pass | https://www.cnbc.com/2026/11/05/jpmorgan-epstein-democrats-erdoes-midterm-election.html |
| ACN | Accenture accused of publishing AI paper... | Pass | Pass | Pass (Nov 4, 2026) | Pass (denies AI, says human error, will correct) | Pass | https://www.tipranks.com/news/the-fly/accenture-accused-of-publishing-ai-paper-rife-with-false-citations-ft-reports-thefly-news |
| NVDA (REGRADE, reworded why) | AI stocks hammered by midterm shock... | Pass | Pass | Pass (Nov 6, 2026, 7:00 AM ET) | Pass (article: Nvidia fell 9.3% on Thursday Nov 5 in AI sell-off; Democrats' rout and Senate control; data center buildout doubts. New "fell sharply on Thursday" wording is supported and no longer carries the number) | Pass | https://www.cnbc.com/2026/11/06/ai-data-center-midterms-selloff.html |
| C | Citi price target raised to $148.50... | Pass | Pass | Fail (Oct 30, 2026 is 7 days before asOf; a window of seven days up to and including Nov 6 starts Oct 31) | Pass (Overweight kept, analyst opinion) | Fail | https://www.tipranks.com/news/the-fly/citi-price-target-raised-to-148-50-from-147-at-jpmorgan-thefly-news |
| LLY | Lilly and Hims & Hers expand access... | Pass | Pass | Pass (Nov 6, 2026) | Pass (Zepbound and Foundayo, self-pay prices) | Pass | https://www.prnewswire.com/news-releases/lilly-and-hims--hers-expand-access-to-fda-approved-lilly-medicines-302931979.html |

Note: the C item fails only on the date edge. The brief's CHANGELOG already flagged it as the edge of the window. The NVDA Nov 6 CNBC article says Nvidia fell 9.3% "Thursday" though it was published Friday; Nov 5 was the Thursday and the close data agrees.

(Add rows as needed. Fifteen items is normal for five tickers.)

## D. Calendar (one row per event)

Pass if the event and its date appear on the source linked, and the date is in the future
relative to the refresh date.

| Ticker | Event | Date shown | Confirmed on source | Pass / Fail | Source link |
|---|---|---|---|---|---|
| NVDA | Q3 fiscal 2027 earnings report | 2026-11-17 | Not confirmed on NVIDIA's IR pages (events page and home page showed no Q3 FY27 date in what I could read). CNBC article says Nvidia is "scheduled to report" Nov 17. Brief already labels it "not confirmed on NVIDIA's IR page". Not an estimated-date problem, but not confirmed by company IR page. | Fail | https://investor.nvidia.com/events-and-presentations/events-and-presentations/default.aspx ; CNBC https://www.cnbc.com/2026/11/05/nvidia-jensen-huang-china-spy-congress.html |
| NVDA | Proposed closed-door House briefing with Jensen Huang | 2026-11-18 | CNBC confirms lawmakers invited Huang for Nov 18; Nvidia has not confirmed attendance. Brief says "Proposed" and "not confirmed". Not an IR event, so no IR page applies. | Pass | https://www.cnbc.com/2026/11/05/nvidia-jensen-huang-china-spy-congress.html |
| JPM | US CPI report for October, 8:30 AM ET (market-wide) | 2026-11-10 | BLS own schedule shows Nov 10, 2026, 8:30 AM. Brief cites a third-party calendar but the date is correct. Third-party page does not label it estimated. | Pass | https://www.bls.gov/schedule/news_release/cpi.htm ; https://thriveinmarkets.com/calendar/dates/cpi-2026/ |
| LLY | Ex-dividend date, $1.73 (record Nov 13, payable Dec 10) | 2026-11-13 | Only found on aggregators: StockAnalysis shows Nov 13 ex/record, Dec 10 pay, not labeled estimated. A web search snippet says dividendhistory.org labels the Nov 13 ex-date as unconfirmed/estimated. Lilly's IR page (investor.lilly.com) returned 403, so I could not open it. Not confirmed by company IR page. | Fail | https://stockanalysis.com/stocks/lly/dividend/ |

Note: the LLY Fail is partly "could not verify" because the Lilly IR site blocked me (403). A search snippet attributed the $1.73, Nov 13 record and Dec 10 payable dates to Lilly's own Oct 26 release, but I did not open that page, so it is not counted.

## E. Omissions

Did the agent miss something a human analyst would have included? List anything you found
in the ground-truth sources that the brief does not mention (a big headline, an earnings
date inside the window). Each one counts as a Fail in the score below.

- 
- 

## F. Your own test cases

Write two tests of your own that none of the sections above cover. Examples students have
used: "the as-of date on the page matches the changelog date", "no ticker shows a flat
sparkline when the real stock moved", "the page still renders if I delete one ticker's
news list in the data". Write the test, run it, record the result.

| Test | How you checked | Pass / Fail |
|---|---|---|
| | | |
| | | |

## Score

| Section | Passes | Total | Rate |
|---|---|---|---|
| A. Prices | 5 | 5 | 100% |
| B. Weekly change (original grade, Fail kept) | 5 | 6 | 83% |
| B. Weekly change (after regrade, 7 rows incl. original Fail) | 6 | 7 | 86% (all 6 current checks pass) |
| C. News (original) | 5 | 6 | 83% |
| C. News (after regrade, 7 rows incl. original C Fail) | 6 | 7 | 86% (C date-edge Fail still stands) |
| D. Calendar | 2 | 4 | 50% |
| E. Omissions (fails only) | 0 | | |
| F. Your own | | | |
| **Overall** | | | |

Publish only if A and B are 100% and C is at least 80% after fixes. If a row failed, tell
Claude exactly which row and what the correct value is, let it fix only that, rerun the
`qa-reviewer` agent, and regrade that row. Keep the original Fail in this file: a record of
what the agent got wrong is more useful than a clean sheet.
