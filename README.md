# TakeMeter

> ### 👋 Start here
>
> **New to this repo? Read [RUNNING.md](RUNNING.md) first** — setup, the
> notebook, the baseline, and what to do when something breaks.
>
> Once `python test.py` passes:
>
> ```bash
> head -5 data/practice_labels.csv     # the shape your labels.csv needs
> ```
>
> Then open `takemeter.ipynb` **in this folder** — in VS Code, or with
> `jupyter notebook` if you prefer. Pick the kernel: the `.venv` inside this
> project. Run section 1, which reports the hardware you'll be training on.
> Everything else waits until you have data.
>
> Nothing to upload, nothing to connect, no accounts and no keys. The notebook
> runs on your machine and writes next to your code.
>
> **The rest of this file is your submission.** Fill it in as you go.

---

<!-- ─────────────────────────────────────────────────────────────────────────
     HOW TO USE THIS FILE

     Unit 5 asks for the first five sections. Unit 6 adds the five below them.

     Everything is pasted as TEXT. No screenshots, no images.

     ⚠️ The confusion matrix especially. The notebook prints one as a markdown
     table, ready to copy. A screenshot of a matrix earns nothing. Paste the
     table.
     ───────────────────────────────────────────────────────────────────────── -->

<!-- ═══════════════════════ UNIT 5 — THE BUILD ═══════════════════════ -->

## What This Does

<!-- Your community, and what your classifier sorts posts into. Three or four
     sentences. -->

I am building a text classifier for public discussion posts from `r/soccer`, a
large football community with match discussion, transfer talk, analysis,
questions, jokes, and emotional reactions. I read 36 public comments across
several Daily Discussion and transfer-discussion threads, rather than taking
examples from one match or one release. The final classifier will sort posts
into a small set of distinctions that I will define after collecting the full
dataset.

### Milestone 1 reading notes

The posts that felt more substantial usually supplied a checkable detail or
explained a cause: a statistic, fee, date, lineup choice, comparison, or a
chain of reasoning. The least substantial posts were often one-line jokes,
immediate emotional responses, or confident verdicts with no support.

Candidate distinctions I saw in the reading:

1. **Evidence-backed argument vs. unsupported assertion:** some posts use a
   number, date, quote, comparison, or concrete match detail to carry the
   point; others make a strong claim without offering support.
2. **Immediate reaction vs. general football claim:** some posts describe the
   writer's feeling about a match or transfer that just happened; others make
   a broader claim about a player, club, manager, or the sport.
3. **Explanation vs. verdict:** some posts connect an outcome to tactics,
   squad construction, or another identifiable cause; others stop at a
   judgment such as calling a signing excellent, useless, or disastrous.
4. **Substantive discussion vs. banter/one-liner:** some posts develop a
   comparison over several sentences, while others are mainly a joke, insult,
   short agreement, or quick question.



---

## Label Taxonomy

<!-- Each label: a one-sentence definition and two real examples from your
     reading. Then your decision rule for the hardest boundary.

     The decision rule is worth a point on its own and it's the thing most
     people leave out. Every taxonomy has a hardest boundary. Name yours. -->

### `analysis`

**Definition:** Makes an argument about football using at least one specific,
checkable detail that does real work in the argument, such as a statistic,
fee, date, named comparison, lineup choice, or tactical sequence.

**Example 1:**
> “Chelsea were absolutely diabolical but ... a midfield that ran, tackled and
> passed more and better than them.” The post then explains how space behind
> the advanced wide players helped create the goal. ([source](https://www.reddit.com/r/soccer/comments/x1iik3/daily_discussion/))

**Example 2:**
> “Milan are having a great mercato. They've kept most of their good players
> ... and bought [a] combination of young [and] experienced players.” It names
> several signings and explains the basis for the evaluation. ([source](https://www.reddit.com/r/soccer/comments/1mw4swx/daily_discussion/))

### `hot_take`

**Definition:**
Makes a confident general claim or verdict about a player, club, manager, or
football decision without offering support that can be checked. A strong tone
alone does not make a post analysis.

**Example 1:**
> “Genuinely think Spurs are about to complete the worst transfer window in
> football history.” ([source](https://www.reddit.com/r/soccer/comments/1w3kd8b/daily_discussion_august_31_2026/))

**Example 2:**
> “people finally realizing that de zerbi's talent id STINKS.” ([source](https://www.reddit.com/r/soccer/comments/1w3kd8b/daily_discussion_august_31_2026/))

### `reaction`

**Definition:** Gives an immediate response to a match, transfer, or football
event that just happened, centered on the writer's feeling or personal
experience rather than a general argument.

**Example 1:**
> “I'm outraged that we haven't signed all players. Sack the board.” ([source](https://www.reddit.com/r/soccer/comments/1w5de0k/daily_discussion_september_2_2026/))

**Example 2:**
> “For once I would like to go to Old Trafford with a fully fit squad.” ([source](https://www.reddit.com/r/soccer/comments/x1iik3/daily_discussion/))

### The hardest boundary

**Which two labels:** `analysis` vs. `hot_take`

**The decision rule I used every time:**
If a specific detail is used to explain or support the conclusion, it is
`analysis`, even if the tone is heated. If the detail is only decorative, or
the post gives no support for its verdict, it is `hot_take`. A post about what
the writer is feeling right now is `reaction` unless it also develops a
supported general argument.

**Borderline post from the reading:**

> “Overpaying for mid players ... extending a goalkeeper that hasn't shown
> anything beyond 6 games ...”

This could be `analysis` or `hot_take`. I chose `analysis` because the writer
uses concrete signings, a six-game sample, and a captaincy decision as reasons
for calling the window risky; those details carry the judgment.

### Boundary stress-test

I asked an AI tool to generate eight short posts designed to sit on these
boundaries, then applied the rules above:

| Synthetic post | Label | Why |
|---|---|---|
| “They had 61% possession and still created only one shot on target.” | `analysis` | The statistic supports the criticism. |
| “That midfield is finished; it will never win anything.” | `hot_take` | It is a broad unsupported verdict. |
| “The final whistle just went and I’m still shaking.” | `reaction` | It reports an immediate feeling. |
| “The £50m fee matters because the club sold its top scorer for less last summer.” | `analysis` | The comparison explains the conclusion. |
| “£50m for him is a disaster.” | `hot_take` | The fee is asserted, but no reason is supplied. |
| “I watched the last ten minutes through my hands; I can’t believe we held on.” | `reaction` | It is a personal response to a recent event. |
| “The red card changed everything.” | `hot_take` | It is a general claim without a supporting detail. |
| “Both fullbacks pushed high, the winger lost the ball, and the counter came through that gap.” | `analysis` | The post gives a checkable causal sequence. |



---

## The Dataset

<!-- Where you collected from, how you labelled, your counts, and three hard
     cases. -->

**Where the posts came from:** Public comments from several `r/soccer` Daily
Discussion threads, including the [August 31, 2026 thread](https://www.reddit.com/r/soccer/comments/1w3kd8b/daily_discussion_august_31_2026/)
and an earlier [Daily Discussion thread](https://www.reddit.com/r/soccer/comments/1mw4swx/daily_discussion/).
I collected 205 unique comments and kept the full text of each selected
comment.

**How I labelled them:** I applied the definitions directly to the first 20
rows and marked them `cold`. I then used AI to pre-label the remaining 185
rows, read every row, and corrected labels when the decision rule required it.
The `note` column records this workflow; no row was accepted without review.

**Counts per label:**

| Label | Count | Share |
|---|---|---|
| `analysis` | 73 | 35.6% |
| `hot_take` | 85 | 41.5% |
| `reaction` | 47 | 22.9% |
| **Total** | **205** | **100%** |

**Three hard cases**

<!-- Any post that made you pause: what it was, which two labels it could have
     been, and what you chose. These are worth more than the easy 190. -->

**1.**
> *The post:* “What happens when both teams are good at counter for attacks and
> so both are not keeping the ball, generally one team tries more than others”
>
> *Could have been:* `analysis` or `hot_take`
>
> *I chose, because:* I chose `analysis` because it is asking about a specific
> tactical situation and whether one side creates more of the action, rather
> than making a purely general insult or verdict.

**2.**
> *The post:* “He can complete passes, that's a massive upgrade over
> Mamardashvili”
>
> *Could have been:* `analysis` or `hot_take`
>
> *I chose, because:* I chose `analysis` because the named comparison is the
> reason offered for the positive judgment.

**3.**
> *The post:* “I stop paying attention for 12 hours and now there's no Eze,
> Savinho will cost nearly 100m, and Chelsea have Enciso fml”
>
> *Could have been:* `reaction` or `hot_take`
>
> *I chose, because:* I chose `reaction` because it reports the writer's
> immediate shock at several transfer updates rather than arguing a general
> claim about the players.

---

## The Training Run

<!-- Your starting model, your settings, and anything you changed and why. -->

**Base model:**

`distilbert-base-uncased`

**Settings:** 3 epochs, learning rate `2e-5`, batch size 16, seed 42, and
maximum sequence length 128.

**Anything I changed from the defaults, and why:**

Nothing. I used the notebook defaults.

**Device:** CPU (`torch 2.14.1+cu130`).

**Split sizes:** 143 train / 31 validation / 31 test. The test split contains
11 `analysis`, 13 `hot_take`, and 7 `reaction` posts. `reaction` is below the
roughly-eight-example guideline, so its next-unit metric may be especially
jumpy between seeds.



---

## How I Used AI

<!-- Two specific moments — what you asked, what came back, what you changed.

     ⚠️ Plus disclosure of any pre-labelling. If you had a model pre-label a
     batch and then read and corrected every one, say that. It's an allowed
     workflow and disclosing it costs you nothing. Not disclosing it is the
     problem. -->

**Moment 1**

- *What I asked for:* I gave the AI tool my three definitions and asked it to
  generate eight short posts designed to sit on the label boundaries.
- *What came back:* It produced examples involving unsupported verdicts,
  immediate feelings, statistics, transfer fees, and tactical sequences.
- *What I changed:* I classified every example myself and tightened the
  analysis rule: a concrete detail must actually support or explain the
  conclusion; merely mentioning a number or event does not make a post
  analysis.

**Moment 2**

- *What I asked for:* I supplied the definitions and decision rule to AI and
  asked it to propose labels for a batch of collected comments.
- *What came back:* It returned preliminary labels for the 185 non-cold rows,
  including short reactions, broad transfer verdicts, and posts with named
  comparisons.
- *What I changed:* I read every proposed label against the full post and
  corrected cases where a detail did not support the claim or where the post
  was primarily an immediate personal response.

**Pre-labelling disclosure:**

I used AI to propose labels for the 185 non-cold rows after supplying the three
definitions and decision rule. I read and checked every proposed label against
the post itself, especially the `analysis`/`hot_take` and
`hot_take`/`reaction` boundaries, and corrected the rows where the evidence or
recency rule did not fit.

<!-- ═══════════════════════ UNIT 6 — THE TEST ═══════════════════════

     Don't fill these in during unit 5.
     ═══════════════════════════════════════════════════════════════════ -->

---

## Baseline vs. Trained

<!-- Both models on the same posts. `python baseline.py --trained results.json`
     prints this table for you. -->

**Prediction, written before checking the baseline:** I predict the trained
model will beat the zero-shot baseline most clearly on `analysis` and
`hot_take`, while `reaction` will remain the hardest label.

The baseline used `facebook/bart-large-mnli`, the definitions in
`label_definitions.txt`, and the same 31 held-out posts in `test_split.csv` as
the trained model. It did not fall back to a random sample.

| Measure | Baseline | Trained | Difference |
|---|---|---|---|
| Overall accuracy | 0.419 | 0.677 | +0.258 |
| Macro F1 | 0.427 | 0.514 | +0.087 |
| F1 — `analysis` | 0.421 | 0.833 | +0.412 |
| F1 — `hot_take` | 0.333 | 0.710 | +0.376 |
| F1 — `reaction` | 0.526 | 0.000 | -0.526 |

The trained model beat the baseline on `analysis` and `hot_take`, as
predicted, but `reaction` was still its hardest label and its F1 fell below
the baseline. The baseline results are in `baseline_results.json`.

### Definition ablation

On the same 31 posts, removing the definitions and using bare label names
reduced accuracy from 0.419 to 0.355 (-0.065) and macro F1 from 0.427 to 0.255
(-0.172). The bare-label control is recorded in
`baseline_bare_names_results.json`; the reported baseline above uses the
definitions-based run.

**What I predicted before I looked:**
<!-- Milestone 1 asks you to write this BEFORE seeing the trained numbers. A
     prediction made afterwards isn't one. -->
The trained model would beat the zero-shot baseline most clearly on `analysis`
and `hot_take`, while `reaction` would remain the hardest label.

**What the gap actually means:**
<!-- If the baseline matched your trained model, your fine-tuning added
     nothing — and that is a real finding, not a failure. Say it plainly. -->
Fine-tuning added a substantial overall accuracy gain over the definitions
baseline (+0.258), especially for `analysis` and `hot_take`, but it did not
solve `reaction` and was worse than the baseline on that label.



---

## Run Log — Before

<!-- Five criteria across three seeds. The notebook's section 6 prints the
     spread table; the Target and Verdict columns are yours. -->

**Device:** CPU (`torch 2.14.1+cu130`).

| Measure | Seed 42 | Seed 7 | Seed 2024 | Spread |
|---|---|---|---|---|
| Overall accuracy | 0.677 | 0.484 | 0.484 | 0.194 |
| Macro F1 | 0.514 | 0.333 | 0.361 | 0.181 |
| F1 — `analysis` | 0.833 | 0.400 | 0.522 | 0.433 |
| F1 — `hot_take` | 0.710 | 0.600 | 0.562 | 0.147 |
| F1 — `reaction` | 0.000 | 0.000 | 0.000 | 0.000 |

Accuracy moved 0.194 across seeds, more than ten points. This dataset is too
small or lopsided for a stable accuracy measure; the `reaction` test split
also has only seven posts, so its per-label F1 is especially fragile.

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1.  |  |  |  |  |  |
| 2.  |  |  |  |  |  |
| 3.  |  |  |  |  |  |
| 4.  |  |  |  |  |  |
| 5.  |  |  |  |  |  |

### Confusion matrix

<!-- ⚠️ TYPED AS A MARKDOWN TABLE. The notebook prints one ready to paste.
     An image of a matrix earns nothing. -->

| true \ predicted | analysis | hot_take | reaction |
|---|---|---|---|
| **analysis** | 10 | 1 | 0 |
| **hot_take** | 2 | 11 | 0 |
| **reaction** | 1 | 6 | 0 |

**My biggest off-diagonal number, and what it means:**
<!-- Not "the model made mistakes" — WHICH boundary it didn't learn, and which
     direction. "7 real analysis posts were called hot_take and only 3 went the
     other way" is a direction, not just an error rate. -->
Six true `reaction` posts were predicted as `hot_take`, while zero true
`hot_take` posts were predicted as `reaction`; the model is strongly biased
toward the `hot_take` side of that boundary.


---

## Verdicts and Diagnoses

<!-- MET or MISSED against LAST UNIT's target. The target has to hold across
     all three seeds, not turn up sometimes. -->

| # | Criterion | Target | Verdict | How I decided |
|---|---|---|---|---|
| 1 |  |  |  |  |
| 2 |  |  |  |  |
| 3 |  |  |  |  |
| 4 |  |  |  |  |
| 5 |  |  |  |  |

**Diagnoses**

<!-- For each miss: the cause, and how you know. The four common causes are:
     too few examples for a label, a boundary you applied inconsistently, a
     genuinely hard label pair, and a task the model can't reach from this
     much data.

     ⚠️ Use your agreement report as evidence. It is the only instrument you
     have that can tell a LABELLING problem from a MODEL problem, and this
     section is graded on whether you used it that way. -->



---

## Agreement Report

<!-- Your rate against the staff set, and every disagreement adjudicated.

     Remember you labelled these 30 under the STAFF taxonomy in
     data/staff_taxonomy.md, not your own — so every argument below is made
     from those definitions and those decision rules. -->

**Agreement rate:** ___ / 30 = ___%

<!-- Nobody grades this number. A 60% who argues every disagreement from the
     stated rules beats a 95% who wrote "staff was right" nine times. Several
     of the 30 were chosen because they're genuinely ambiguous — you should be
     winning some of these. -->

**Disagreements**

<!-- Three lines each: the post, both labels, and who you think is right and
     why — grounded in the staff definitions you were both applying.

     Then sort each into one of three piles:
       (a) the rule covered it and I applied it loosely → a consistency problem
       (b) the rule genuinely doesn't say               → a gap in the definitions
       (c) the rule is ambiguous here and my reading is defensible → argue it.
           This is a legitimate win.

     Pile (a) is the one that matters most for your diagnosis: if you applied a
     written rule two different ways on 30 posts, that is direct evidence about
     what you did across your own 200. -->

**1.**
> *The post:*
>
> *Staff said / I said:*
>
> *My call, and why:*
>
> *Which pile:*

**2.**
> *The post:*
>
> *Staff said / I said:*
>
> *My call, and why:*
>
> *Which pile:*

**What the pattern in my disagreements tells me:**



---

## The Improvement

**What I changed:**

**Which diagnosis pointed at it:**

### Run Log — After

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1.  |  |  |  |  |  |
| 2.  |  |  |  |  |  |
| 3.  |  |  |  |  |  |
| 4.  |  |  |  |  |  |
| 5.  |  |  |  |  |  |

**Did it help, and how do I know:**

<!-- If it didn't, say so. Relabelling that didn't help is a genuinely
     interesting result and earns full credit. -->



---

## What's Still Broken

<!-- For each criterion still missed: what you'd do, and why you stopped. -->



**The gap between what I meant my labels to capture and what the model
learned:**
<!-- Two sentences. Your confusion matrix is the evidence. -->



<!-- ═════════════════════════════════════════════════════════════════════

     SUBMISSION CHECKLIST — unit 5

       [ ] criteria.md has five numbered criteria, each naming a NUMBER
       [ ] Each has a reason underneath tied to your data or taxonomy
       [ ] labels.csv: at least 150 rows, text/label/note, ONE file not split
       [ ] No label above 70%
       [ ] All five unit 5 sections have real content
       [ ] Label Taxonomy includes the decision rule for your hardest boundary
       [ ] The Dataset includes three hard cases
       [ ] results.json and test_split.csv committed (the notebook does this)
       [ ] At least four new commits
       [ ] Repository URL submitted — WRITE IT DOWN

     SUBMISSION CHECKLIST — unit 6

       [ ] Baseline vs. Trained table, with your prediction written beforehand
       [ ] Run Log — Before, five criteria across three seeds
       [ ] Confusion matrix TYPED AS A MARKDOWN TABLE
       [ ] A verdict on every criterion
       [ ] A diagnosis for every miss, using the agreement report as evidence
       [ ] Agreement Report with every disagreement adjudicated
       [ ] One improvement, with Run Log — After
       [ ] What's Still Broken
       [ ] results_three_seeds_before.json, results_three_seeds_after.json,
           baseline_results.json and
           agreement_results.json committed
       [ ] At least four new commits
       [ ] The SAME repository URL as last unit

     Do not delete and recreate this repository.
     ═════════════════════════════════════════════════════════════════════ -->

---

📖 **How to run this project: [RUNNING.md](RUNNING.md)**
