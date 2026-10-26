# Can You Study Your Way to Winning Jeopardy?

Dataquest guided project, *Winning Jeopardy*. Dataset: 19,999 clues from 336 episodes aired 1984-09-21 to 2012-01-19, scraped from J! Archive.

## The claims I checked

The analysis usually run on this dataset produces two findings, both pointing to "study old clues":

1. **About 70% of questions are recycled.**
2. **Certain words predict high-value clues**, per chi-squared tests.

Neither survives.

## Findings

### 1. The recycling metric measures nothing

It reproduces at 0.7239. Then:

| Version of the data | Mean overlap |
|---|---|
| Real clues, in air-date order | 0.7239 |
| **Every word reassigned to a random clue** | **0.7372** |
| Clues in reverse order | 0.7309 |
| Clues in random order | 0.7282 |

**Destroying all recycling makes the number go up.** The metric has no power to detect the thing it is named after.

What it measures is how fast an English vocabulary saturates. The first 500 clues score 0.213 and the last 500 score 0.810, purely because the running vocabulary grows from nothing to 20,249 words as the walk proceeds. The first clue scores zero by construction.

### 2. The chi-squared scan is mostly noise, and its top results are HTML

Across 386 terms appearing in 30 or more clues: **47 clear p < 0.05 where 19 are expected by chance, and 5 survive Bonferroni.**

The two strongest by a wide margin are **archive** (chi-squared 113.7) and **target** (111.1). Neither is a trivia word. The scrape kept anchor tags for audio and video clues, so `<a href="http://www.j-archive.com/media/...">` appears in 949 clues and `target=` in 752. Normalising turns the markup into words.

The signal is real but misidentified: those tags mark **multimedia clues**, which Jeopardy places disproportionately in high-value positions. The test found a genuine pattern and attributed it to vocabulary.

### 3. What the data actually shows

| Measure | Value |
|---|---|
| Identical questions repeated | 12 of 19,999 (**0.06%**) |
| Clues in any near-duplicate pair (Jaccard >= 0.6) | 368 (**1.84%**) |
| Clues whose answer appears elsewhere | 5,771 (**28.86%**) |
| Distinct answers | 14,228 for 19,999 clues |

**Questions are not recycled. Answers are.** Japan appears as the answer 22 times; India, Australia, China and Canada 19 each; then Texas, Italy, Chicago. Countries, US states, major cities.

But the tail defeats memorisation:

| Answers memorised | Clues covered |
|---|---|
| 100 | 5.6% |
| 500 | 15.5% |

## The practical answer

**Studying past clues hoping to meet them again is worthless: 0.06% come back.** Studying recurring *subjects* is better founded but still thin, since the 100 most repeated answers in 28 years cover one clue in eighteen.

What the data supports is unglamorous. Jeopardy draws on a core of common general knowledge and an enormous tail of everything else. Broad knowledge beats a study list, and no analysis of this dataset will find a shortcut.

## Method

- Normalise text by lowercasing and replacing non-alphanumerics with spaces.
- Reproduce the standard overlap metric, then run it against three null versions, the key one reassigning every word to a random clue while preserving word frequencies and clue lengths exactly.
- Measure real recycling at clue level: exact duplicates, plus Jaccard similarity on long-word sets with rare-word blocking to keep the pair count tractable.
- Run chi-squared on all 386 terms appearing in 30 or more valued clues, and report the Bonferroni threshold alongside the uncorrected count.

## What I would not claim

- **This is 19,999 of roughly 500,000 aired clues.** Recycling on a 20-year cycle would be largely invisible at this density, so 0.06% is a floor on the sample, not a measurement of the show.
- **The 336 episodes are not a random sample** of the period, and early years are thin.
- **Jaccard >= 0.6 is my threshold,** and rare-word blocking will miss a rephrased clue sharing no uncommon vocabulary with its twin. The 1.84% is a lower bound.
- **Answer matching is on exact normalised strings,** so "the US" and "United States" count separately. True answer recycling is higher than 28.86%.
- **The $800 high-value cut is arbitrary** and ignores that clue values doubled in 2001, so values are not comparable across the span without adjustment.

## Files

- `notebook.ipynb`: full analysis, executed, with two charts.

## Stack

pandas, NumPy, SciPy, Matplotlib (fivethirtyeight style). No seaborn.