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

r/smallbusiness is a public community where owners and would-be owners ask each other for help. This classifier reads one text post and sorts it by **what the poster wants the replies to do for them**: give a plan, give a specific answer, give a verdict, or share experience. It sorts by intent, not by topic, so a post about taxes and a post about marketing can land in the same label. Ads, "[For Hire]" posts and self-promotion are removed before labelling, because they ask nothing of the community. The labels came from coding about 400 posts from a 7-day pull (9–16 Oct 2026) one at a time, and were not designed first.

---

## Label Taxonomy

Four parent labels. The classifier trains on these only. Each parent has child sub-types, recorded in the `note` column of `labels.csv`, that keep the larger labels meaningful and settle edge cases. The classifier never sees them.

**The one question that sorts every post:** *what will the poster do with the replies?*

| Label | The poster will... | Share of 399 coded posts | Children |
|---|---|---|---|
| `wants_plan` | act on a method | 39% | `plan_start` (45), `plan_grow` (48), `plan_fix` (63) |
| `wants_peer_input` | read, compare, or commiserate | 28% | `peers_discuss` (44), `peers_shared` (35), `peers_poll` (31) |
| `wants_verdict` | decide, using a judgment | 17% | `verdict_choice` (52), `verdict_idea` (16) |
| `wants_answer` | use one specific item or fact | 16% | `answer_pick` (42), `answer_fact` (23) |

### `wants_plan`

**Definition:** The poster describes their own goal or problem and asks for steps, tactics or ideas to act on, such as how to get customers, how to start something, or how to handle a situation.

**Children:** `plan_start` (how to begin, or ideas for what to start), `plan_grow` (customers, leads, pricing, marketing), `plan_fix` (a live operational, money or people problem, including most vents).

**Example 1** (`plan_grow`, 1x6sh4k):
> "Does anyone here have a way to get more leads without paying for advertising or for leads?"

**Example 2** (`plan_fix`, 1x4pwa9):
> "What is your method/trick that worked with you to push them paying on time? or at least to pay the due amount?"

### `wants_answer`

**Definition:** The poster asks for one specific item or fact, such as a recommended tool, vendor, supplier or professional, or a definite legal, tax or compliance answer.

**Children:** `answer_pick` (a named item to choose), `answer_fact` (a yes/no or rule).

**Example 1** (`answer_pick`, 1x7kqyu):
> "Looking for scheduling app recommendations. Something that allows you to add up to 10-15 different employees, at one location where hours worked can be pulled to run payroll"

**Example 2** (`answer_fact`, 1x5a2cj):
> "Can I legally sell my canned goods on a website and deliver them to other states?"

### `wants_verdict`

**Definition:** The poster puts a specific idea, option or decision on the table and asks whether it is good, right or worth doing.

**Children:** `verdict_choice` (a decision, deal or situation to judge), `verdict_idea` (an idea, plan, price or offer to critique).

**Example 1** (`verdict_choice`, 1x6disu):
> "Sell and go to uni, or keep it running with a team lead?"

**Example 2** (`verdict_choice`, 1x6ohnl):
> "Opinions wanted on restaurant placemat advertising"

### `wants_peer_input`

**Definition:** The poster asks the community to share experiences, practices or opinions, or whether others are in the same situation, with no method, item or verdict they need to act on.

**Children:** `peers_discuss` (an opinion, story or lesson offered for discussion), `peers_shared` ("has anyone been through this?", often after a vent), `peers_poll` (a survey of how people do something).

**Example 1** (`peers_poll`, 1x6fcqg):
> "What’s the manual process in your business that simply refuses to die, and why’s it still there?"

**Example 2** (`peers_shared`, 1x71vce):
> "Anyone been through this?"

### The hardest boundary

**Which two labels:** `wants_plan` and `wants_peer_input`.

They collide because many posts ask peers a question about the past ("how did you...?") that is really a request for a method.

**The decision rule I used every time:** Ask what the poster will do with the replies.
- If the poster states a **live goal or problem of their own** and wants something to act on, it is `wants_plan`, even when phrased as "how did you...?". 1x23z1e asks "where did your first few bookings come from?" about the poster's own new business, so it is `wants_plan`.
- If the poster only **surveys, discusses, or asks whether others share the situation**, it is `wants_peer_input`. 1x4kafe asks "where did your last 5 new customers come from?" with no problem of their own, so it is `wants_peer_input`.

Two rules apply to every boundary:
1. **Venting always has an ask.** I find it from the closing question, or by asking what reply would actually help. A vent that asks for a method goes to `wants_plan`. A vent that asks "anyone else?" goes to `wants_peer_input`. 1x3phzx ("How did you successfully escalate the situation?") is `wants_plan`. 1x71vce ("Anyone been through this?") is `wants_peer_input`.
2. **Mixed asks:** the first explicit ask (the title, else the first question) decides, and the second ask is recorded as the runner-up.

### Edge cases

| Post | Candidate labels | Chosen | Why |
|---|---|---|---|
| 1x7d1dx (henna artist) | `wants_plan` / `wants_answer` | `wants_plan` | First ask is "boost my viewership and bookings." App recommendations are secondary |
| 1x7ijwd (home-watch invoicing) | `wants_answer` / `wants_plan` | `wants_answer` | First ask is "What software do you use?" Reminder wording comes second |
| 1x5do6e (S Corp vs LLC) | `wants_verdict` / `wants_plan` | `wants_verdict` | The title asks whether to switch. "Questions to ask" is secondary |
| 1x5mvaq (freelancer vs agency) | `wants_verdict` / `wants_peer_input` | `wants_verdict` | The poster states their own two options and asks "what would you do" |
| 1x5gi85 (left corporate, went back) | `wants_peer_input` / `wants_verdict` | `wants_peer_input` | Asks for stories ("can we hear some reality check stories too?"), not a judgment on one option |
| 1x7ery0 (Shopify fine) | `wants_plan` / `wants_peer_input` | `wants_plan` | Opens with "has anyone challenged one?" but ends on whether there is a formal appeal process |
| 1x6c16w (stalking, new LLC) | no explicit ask | `wants_plan` | Implicit ask: how to run a business privately and safely |
| 1x3nag0 (growth ceiling, "feeling like a loser") | no explicit ask | `wants_peer_input` | Implicit ask: "is there anyone who felt that way?" |
| 1x6l0ik, 1x1uv0c | `wants_plan` / promo | `wants_plan` | Same templated closing ("Maybe you've dealt with a similar..."). May be disguised lead-gen. Review in Milestone 4 |
| 1x3a612, 1x39qys | any | by the rules above | Written in French. Keep or drop in Milestone 4 |

Reposts by the same author also exist (for example 1x5km93 and 1x5knht, 1x4bet3 and 1x4bdt1, 1x3tzeh and 1x3twvd). They must be deduplicated in Milestone 4 so they don't land in different splits.

---

## The Dataset

<!-- Where you collected from, how you labelled, your counts, and three hard
     cases. -->

**Where the posts came from:**

**How I labelled them:** <!-- Cold first? Pre-labelled with AI and corrected?
Say so plainly — the disclosure is required, not penalised. -->

**Counts per label:**

| Label | Count | Share |
|---|---|---|
|  |  |  |
|  |  |  |
|  |  |  |
| **Total** |  | 100% |

**Three hard cases**

<!-- Any post that made you pause: what it was, which two labels it could have
     been, and what you chose. These are worth more than the easy 190. -->

**1.**
> *The post:*
>
> *Could have been:*
>
> *I chose, because:*

**2.**
> *The post:*
>
> *Could have been:*
>
> *I chose, because:*

**3.**
> *The post:*
>
> *Could have been:*
>
> *I chose, because:*

---

## The Training Run

<!-- Your starting model, your settings, and anything you changed and why. -->

**Base model:**

**Settings:** <!-- epochs, learning rate, batch size, seed -->

**Anything I changed from the defaults, and why:**

**Split sizes:** <!-- train / val / test, and per-label counts in the test
split. If a label had fewer than about 8 in test, say so — it explains a lot
of next unit's variance. -->



---

## How I Used AI

<!-- Two specific moments — what you asked, what came back, what you changed.

     ⚠️ Plus disclosure of any pre-labelling. If you had a model pre-label a
     batch and then read and corrected every one, say that. It's an allowed
     workflow and disclosing it costs you nothing. Not disclosing it is the
     problem. -->

**Moment 1**

- *What I asked for:*
- *What came back:*
- *What I changed:*

**Moment 2**

- *What I asked for:*
- *What came back:*
- *What I changed:*

**Pre-labelling disclosure:**

<!-- ═══════════════════════ UNIT 6 — THE TEST ═══════════════════════

     Don't fill these in during unit 5.
     ═══════════════════════════════════════════════════════════════════ -->

---

## Baseline vs. Trained

<!-- Both models on the same posts. `python baseline.py --trained results.json`
     prints this table for you. -->

| Measure | Baseline | Trained | Difference |
|---|---|---|---|
| Overall accuracy |  |  |  |
| Macro F1 |  |  |  |
| F1 — `label_one` |  |  |  |
| F1 — `label_two` |  |  |  |

**What I predicted before I looked:**
<!-- Milestone 1 asks you to write this BEFORE seeing the trained numbers. A
     prediction made afterwards isn't one. -->

**What the gap actually means:**
<!-- If the baseline matched your trained model, your fine-tuning added
     nothing — and that is a real finding, not a failure. Say it plainly. -->



---

## Run Log — Before

<!-- Five criteria across three seeds. The notebook's section 6 prints the
     spread table; the Target and Verdict columns are yours. -->

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

| true \ predicted |  |  |  |
|---|---|---|---|
| **** |  |  |  |
| **** |  |  |  |
| **** |  |  |  |

**My biggest off-diagonal number, and what it means:**
<!-- Not "the model made mistakes" — WHICH boundary it didn't learn, and which
     direction. "7 real analysis posts were called hot_take and only 3 went the
     other way" is a direction, not just an error rate. -->



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
