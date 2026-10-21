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

r/smallbusiness is a public community where owners and would-be owners ask each other for help. This classifier reads one text post and sorts it with a **hybrid label**: the topic the post is about *and* what the poster wants the replies to do. A post about getting customers and a post about taxes land in different labels because they use different words and ask for different things. Deleted posts, ads, "[For Hire]" posts, self-promotion and market-research polls are removed before labelling, because they ask nothing of the community and are gaming the subreddit for answers or leads. I worked out the four labels in this project: Claude proposed the hybrid taxonomy from an earlier pull of 399 posts (9–16 Oct 2026; 361 after removing polls and market research), and I replaced my earlier intent-only labels with it because intent alone gave labels with little vocabulary of their own. The final training set is 200 posts from a separate pull (25 Sep–8 Oct 2026), fine-tuned on `distilbert-base-uncased`.

---

## Label Taxonomy

Four labels in `TOPIC_INTENT` form. The classifier trains on these only.

**The sorting order for every post:**
1. What is the poster asking *for*? A **name** (a tool, vendor, supplier, professional) → `TOOLS_RECOMMENDATION`. A **rule, number, or legal/financial structure** → `LEGAL_MONEY_QUESTION`.
2. Otherwise, is it about **getting customers or starting up**? → `GROWTH_ADVICE`.
3. Otherwise → `OPERATIONS_PEERS`.

| Label | The poster is... | Share of 361 coded posts |
|---|---|---|
| `GROWTH_ADVICE` | asking how to get customers, launch, or pick what to start | 41% (148) |
| `OPERATIONS_PEERS` | venting or asking peers how they run the day-to-day | 29% (103) |
| `LEGAL_MONEY_QUESTION` | asking a legal, tax, financing or deal-structure question | 17% (62) |
| `TOOLS_RECOMMENDATION` | asking which tool, vendor or supplier to use | 13% (48) |

### `GROWTH_ADVICE`

**Definition:** The poster describes their own business or idea and asks for tactics, channels, a plan or a judgment about getting customers, launching, or choosing what to start, such as how to find first clients, whether a pricing, ad or business idea is worth it, or what business to begin.

**Vocabulary:** first customers, first clients, leads, get clients, promote, marketing, ads, Meta, Instagram, launch, validate, niche, side business, business idea, scale, "how do I get", "where do I start".

**Edge rule:** A request for a method wins over its phrasing. "How did you get your first clients?" and "is Etsy a terrible idea?" are both `GROWTH_ADVICE`.

**Example** (1x6sh4k, title): "How to generate more leads?"
**Example** (1x6ohnl, title): "Opinions wanted on restaurant placemat advertising"

### `TOOLS_RECOMMENDATION`

**Definition:** The poster asks for a named tool, software, vendor, supplier, manufacturer, platform or service professional to use, where the answer is a name or short list.

**Vocabulary:** recommend, recommendations, best, supplier, manufacturer, wholesale, bulk, software, app, platform, POS, CRM, scheduling, "looking for", "reliable".

**Edge rule:** If the answer is a name, it is here. If the answer is a rule or a number ("is workers' comp required?"), it is `LEGAL_MONEY_QUESTION`. A CPA or attorney recommendation is here.

**Example** (1x7kqyu, title): "Best scheduling app? (one location, up to 10-15 employees)"

### `LEGAL_MONEY_QUESTION`

**Definition:** The poster asks a question with a definite or checkable answer about legal structure, taxes, compliance, insurance, financing or a deal, such as LLC vs S Corp, whether a permit or filing is required, a loan choice, or whether to buy or sell a business.

**Vocabulary:** LLC, S Corp, sole proprietor, EIN, permit, license, trademark, liability, insurance, tax, loan, line of credit, SBA, lease, buyout, valuation, equity, "do I have to".

**Edge rule:** Money talk alone does not qualify. Pricing your own service is `GROWTH_ADVICE`. A client who won't pay is `OPERATIONS_PEERS`. This label needs a legal, tax, financing or deal-structure object.

**Example** (1x5a2cj, title): "Selling home canned goods online"
**Example** (1x5do6e, title): "SCorp vs LLC - Trying to decide if it's time to make a switch"

### `OPERATIONS_PEERS`

**Definition:** The poster vents about, or asks peers how they handle, running the business day to day, such as clients who don't pay, employees, workload, frozen payment accounts, burnout or automation, and wants shared experience rather than a named item, a rule or a growth plan.

**Vocabulary:** anyone else, how do you handle, invoice, won't pay, employee, hire, burnout, schedule, workflow, automate, processor, account frozen, Square, Stripe, Wise, "has anyone".

**Edge rule:** If a vent ends in a concrete fix (a legal clause, a named tool), use the ask: `LEGAL_MONEY_QUESTION` or `TOOLS_RECOMMENDATION`. If it ends in "anyone else?" or "how do you handle it?", it stays here.

**Example** (1x6fcqg, title): "What's the manual process in your business that simply refuses to die?"
**Example** (1x4pwa9, title): "What is your trick to collect from non-paying B2B customers?"

### The hardest boundary

**Which two labels:** `GROWTH_ADVICE` and `OPERATIONS_PEERS`.

They collide because "how do you…?" posts to peers can be about getting customers (growth) or running the business (operations), and the same words (price, client, time) appear in both.

**The decision rule:** Ask whether the answer would put a **new customer or new business** in front of the poster, or keep the **existing business running**. New customers or a new business → `GROWTH_ADVICE`. Existing clients, staff, payments or tools → `OPERATIONS_PEERS`. 1x4pwa9 (collecting from a client who already owes money) is operations. 1x6sh4k (more leads) is growth.

### Edge cases

| Post | Candidate labels | Chosen | Why |
|---|---|---|---|
| 1x7d1dx (henna artist) | `GROWTH_ADVICE` / `TOOLS_RECOMMENDATION` | `GROWTH_ADVICE` | First ask is boosting viewership and bookings. App recommendations are secondary |
| 1x7ijwd (home-watch business) | `TOOLS_RECOMMENDATION` / `GROWTH_ADVICE` | `TOOLS_RECOMMENDATION` | First ask is "what software do you use?" |
| 1x5do6e (S Corp vs LLC) | `LEGAL_MONEY_QUESTION` / `GROWTH_ADVICE` | `LEGAL_MONEY_QUESTION` | The choice is between legal structures |
| 1x5mvaq (freelancer vs agency) | `GROWTH_ADVICE` / `OPERATIONS_PEERS` | `GROWTH_ADVICE` | Two paths for getting clients and income |
| 1x5gi85 (left corporate, went back) | `GROWTH_ADVICE` / `OPERATIONS_PEERS` | `GROWTH_ADVICE` | Judges whether to start a business. Not about running one |
| 1x7ery0 (Shopify $18k fine) | `OPERATIONS_PEERS` / `LEGAL_MONEY_QUESTION` | `OPERATIONS_PEERS` | A vent with "has anyone challenged one?". No rule being asked for |
| 1x6c16w (stalking, new LLC) | no explicit ask | `OPERATIONS_PEERS` | Implicit ask: how to run a business safely |
| 1x3nag0 (growth ceiling, "feeling like a loser") | no explicit ask | `OPERATIONS_PEERS` | Implicit ask: did others feel this way |
| 1x6l0ik, 1x1uv0c | `GROWTH_ADVICE` / promo | removed | Same templated wording, likely disguised lead-gen |
| 1x3a612, 1x39qys | any | by the rules above | Written in French. Keep or drop in Milestone 4 |

**Removed before labelling:** 38 of the 399 coded posts. 31 were `peers_poll` ("what do you use for X?", surveys, "a few questions for beauty providers"). 7 more were flagged promo-ish, market-research or templated lead-gen (1x5r1zv, 1x4uszl, 1x4mgbc, 1x4lynu, 1x480oh, 1x1uv0c, 1x6l0ik). The poll posts were removed as a group, so some genuine ones went too. Six posts flagged only "promo-light" in my notes stayed in (1x78tus, 1x6zwfo, 1x6cita, 1x5jsuh, 1x4u8j0, 1x43dqw).

**Known weaknesses of this scheme**
- **`GROWTH_ADVICE` is now 41%.** Removing the polls took 27 posts out of `OPERATIONS_PEERS` and only 7 out of `GROWTH_ADVICE`, which pushed it just over the 40% ceiling. Undersample it in Milestone 4.
- **Intent is no longer a label.** A verdict ("should I…?") is sorted by topic, so verdict posts are spread across all four labels (41 `GROWTH_ADVICE`, 18 `LEGAL_MONEY_QUESTION`, 5 `OPERATIONS_PEERS`, 4 `TOOLS_RECOMMENDATION`).
- **`TOOLS_RECOMMENDATION` is the smallest label.** At 13%, a 200-post set gives about 27 posts, about 5 in a test split. Its F1 will vary a lot between seeds.
- **Re-cut by one person.** 74 of the 361 posts (21%) needed a hand override after the first rule pass. A second reader could split differently, so unit 6 agreement is the first real test.

Reposts by the same author also exist (for example 1x5km93 and 1x5knht, 1x4bet3 and 1x4bdt1, 1x3tzeh and 1x3twvd). They must be deduplicated in Milestone 4 so they don't land in different splits.

---

## The Dataset

**Where the posts came from:** r/smallbusiness, via the Arctic Shift public archive API. I pulled 1,777 submissions from 25 Sep to 8 Oct 2026, a window before the 9 to 16 Oct pull I used to build the taxonomy, so no post from the taxonomy pool is in `labels.csv`. I dropped removed/deleted and non-text posts, posts of 150 characters or fewer, repeat titles, and posts matching contact-detail or plug patterns. That left 890 candidates. I labelled a random 380 of them (20 cold, 360 pre-labelled). I dropped 99 of the 360 as promotion, polls or market research and sampled the remaining 261 to hit my balance targets (no label above 40%, none below 12%). Each `text` is the title followed by the whole post.

**How I labelled them:**
- **20 cold:** I labelled these myself, with no AI help, before seeing any pre-label. Four of the 20 were replaced because I removed them as promotion or market research (Cold 7, 9, 11, 16 were redrawn). Three cold labels were changed after I re-checked them for mis-clicks, and those rows are marked `cold (corrected after click check)` in the note column.
- **180 pre-labelled by Claude, then reviewed one by one:** I gave Claude my definitions and it assigned a label to each post. I then read every post and either agreed or changed it. My first pass changed 22 labels. A second audit of those 22 found that 15 were mis-clicks (the answer options appeared in a different order from question to question), so 7 corrections from that pass stand, plus 1 on a replacement post. The `note` column says `pre-labelled (Claude), checked` or `pre-labelled (Claude), corrected`.
- **Caveat 1, anchoring:** I agreed with Claude's pre-label on about 96% of posts (172 of 180), which could be real agreement or anchoring on its label.
- **Caveat 2, shortened text:** Claude pre-labelled each post from its title plus roughly the first 230 characters. When I reviewed, long posts were shortened in the review prompt (62 of the 200 posts, 53 of them pre-labelled). For long posts the ask is often at the end, so those labels are less reliable than the rest.
- **Blind re-label:** I re-labelled 20 random posts from the 172 I had agreed with, with no pre-label shown. I matched the committed label on 12 of 20 (60%). Part of that gap comes from shortened text on 3 of the 8 disagreements. The other 5 were short posts shown in full. Four of the 8 were `OPERATIONS_PEERS` vs `LEGAL_MONEY_QUESTION`, on situations with a legal angle but no explicit legal question. Short posts with no clear ask can't be sorted reliably, so on those the label is partly a guess.
- **Decision:** I kept the labels as they are and did not re-review them. The consequence is that part of the label noise is built into this dataset, and I expect `OPERATIONS_PEERS` vs `LEGAL_MONEY_QUESTION` to be the model's weakest boundary. I'll use this in the unit 6 diagnosis.

**Counts per label:**

| Label | Count | Share |
|---|---|---|
| `GROWTH_ADVICE` | 78 | 39.0% |
| `OPERATIONS_PEERS` | 48 | 24.0% |
| `LEGAL_MONEY_QUESTION` | 38 | 19.0% |
| `TOOLS_RECOMMENDATION` | 36 | 18.0% |
| **Total** | 200 | 100% |

The counts come from sampling for balance, so they do not match the natural mix of the subreddit (about 42% growth, 20% operations, 19% tools and 18% legal in my pre-labelled pool).

**Three hard cases**

**1.**
> *The post:* "EXAGGERATED MY COST. I work in a consultancy... I think I charged a large amount, which I knew that they couldn't pay easily... how do I retain them without making them feel I buckled under pressure and reduced their charges...? I asked 6k, finalised my offer to 5k, and can easily do it in 3.5k."
>
> *Could have been:* `LEGAL_MONEY_QUESTION` or `GROWTH_ADVICE` (or `OPERATIONS_PEERS`, which was the pre-label).
>
> *I chose* `LEGAL_MONEY_QUESTION`, because the ask is about a quote and a discount on a specific deal. I did not treat it as growth because the client is not a new customer to find, but the label is a judgment call. The rule "pricing your own service is growth" would have sent it to `GROWTH_ADVICE`.

**2.**
> *The post:* "Just did my first tax return. Did my own tax for my S-corp and LLC with all the bells and whistles... Some things I learn: 'Above-the-line' deduction, R&D credits... Will I do it myself next year? Probably not."
>
> *Could have been:* `LEGAL_MONEY_QUESTION` (tax topic) or `OPERATIONS_PEERS`.
>
> *I chose* `OPERATIONS_PEERS`, because the post asks nothing and shares an experience. My definition of `LEGAL_MONEY_QUESTION` needs a question with a definite answer. This is the case where topic and intent point to different labels.

**3.**
> *The post:* "Getting responses on bizbuysell. I understand that sellers or brokers might receive many replies to their ads, but what kind of details do buyers or brokers seek when someone answers the listing? I'm reaching out to brokers too but curious what initial message gets a response."
>
> *Could have been:* `LEGAL_MONEY_QUESTION` (buying a business) or `GROWTH_ADVICE` (what message gets a reply).
>
> *I chose* `GROWTH_ADVICE`, because the ask is about getting a response to outreach, not about deal structure or financing. The pre-label was `LEGAL_MONEY_QUESTION` on the topic alone.

---

## The Training Run

**Base model:** `distilbert-base-uncased`, fine-tuned for 4-way classification. I trained on Apple Silicon (`mps`), PyTorch 2.14.1. `results.json` records the device.

**Settings:** 3 epochs, learning rate 2e-5, batch size 16, 128 tokens per post, seed 42.

**Anything I changed from the defaults, and why:** Nothing. I only set `LABELS` to my four labels. I ran the notebook's section 1 to 5 cells as a script with the project `.venv` Python, so the notebook file has no saved outputs for this run.

**Split sizes:** train 139, val 31, test 30 (stratified 70/15/15).

| Label | Train | Val | Test |
|---|---|---|---|
| `GROWTH_ADVICE` | 54 | 12 | 12 |
| `TOOLS_RECOMMENDATION` | 25 | 6 | 5 |
| `LEGAL_MONEY_QUESTION` | 26 | 6 | 6 |
| `OPERATIONS_PEERS` | 34 | 7 | 7 |

**Notes for next unit:**
- Three labels have fewer than 8 test posts (`TOOLS_RECOMMENDATION` 5, `LEGAL_MONEY_QUESTION` 6, `OPERATIONS_PEERS` 7). Their per-label F1 will move a lot between seeds.
- The model reads only the first 128 tokens. The median post is 144 tokens, and 115 of the 200 posts are longer, so the end of those posts (where the ask often sits) is cut off.
- I have not read the results closely. A first look shows that the model predicted `GROWTH_ADVICE` for every one of the 30 test posts, so accuracy equals the share of `GROWTH_ADVICE` in the test split (0.40). I'll diagnose that in unit 6.

---

## How I Used AI

**Moment 1: proposing the taxonomy**

- *What I asked for:* I had Claude analyze my r/smallbusiness posts and propose a four-category hybrid taxonomy that combined topic and intent, with a name, strict definition, vocabulary and an edge-case rule for each category.
- *What came back:* Four labels (`GROWTH_ADVICE`, `TOOLS_RECOMMENDATION`, `LEGAL_MONEY_QUESTION`, `OPERATIONS_PEERS`) with vocabulary lists from a keyword analysis. It estimated `GROWTH_ADVICE` at about 45%, which was over my 40% ceiling.
- *What I changed:* I asked for the posts to be hand-labelled against the new definitions, and that gave `GROWTH_ADVICE` at 38.8%, so the keyword estimate was wrong. I replaced my intent labels with the hybrid labels. I also decided that polls and market-research posts were gaming the subreddit and had Claude remove them, along with promotion (38 posts from the taxonomy pool, then more from the final pull).

**Moment 2: the pre-label review and the blind re-label**

- *What I asked for:* I had Claude pre-label 180 posts under my definitions and show them to me one at a time, so I could confirm or correct each. Later I asked for a blind re-label of 20 of the posts I had agreed with, with no pre-label shown.
- *What came back:* Claude's labels agreed with mine on about 96% of the posts. My first pass changed 22 labels, and when Claude asked me to re-check them, 15 turned out to be mis-clicks because the answer options changed order from one question to the next. The blind re-label matched the committed label on only 12 of 20 (60%). Posts had been shortened in the review prompts, and some short posts had no clear ask.
- *What I changed:* I fixed the mis-clicks and kept the remaining 7 corrections. I kept the labels as they were instead of re-reviewing them, and I documented the 60% result and the truncation problem in the Dataset section. I told Claude to summarize long posts in future instead of cutting them off.

**Pre-labelling disclosure:**

- **The 180 pre-labelled rows in `labels.csv`** were labelled first by Claude, from each post's title plus roughly its first 230 characters, and then reviewed by me one at a time. They are marked `pre-labelled (Claude), checked` (172) or `pre-labelled (Claude), corrected` (8) in the `note` column. Part of my review was on shortened text, as described in the Dataset section.
- **The 20 cold rows** were labelled by me with no AI help and are marked `cold`. Three of them were corrected after a click check and are marked `cold (corrected after click check)`. Four were redrawn after I removed promotion or market-research posts.
- **Posts used to derive the taxonomy:** the 399-post pull from 9–16 Oct 2026 came from an earlier working session, described in `reading_notes.md`. Claude helped read and code those posts into intent groups, and I reviewed them. They are not in `labels.csv`. Claude then proposed the hybrid taxonomy from them and I chose and tested it (Moment 1).
- **Code and filtering:** Claude pulled the posts from the Arctic Shift API, wrote the filtering, and ran the notebook cells for the training run. The numbers in `results.json` come from that run.

<!-- ═══════════════════════ UNIT 6 — THE TEST ═══════════════════════

     Don't fill these in during unit 5.
     ═══════════════════════════════════════════════════════════════════ -->

---

## Baseline vs. Trained

Both models were scored on the same 30 posts in `test_split.csv`. The baseline is the zero-shot `facebook/bart-large-mnli`, which reads only my label definitions. It ran on Apple Silicon (`mps`). The trained model is the seed 42 run.

| Measure | Baseline | Trained | Difference |
|---|---|---|---|
| Overall accuracy | 0.567 | 0.400 | -0.167 |
| Macro F1 | 0.414 | 0.143 | -0.271 |
| F1 — `GROWTH_ADVICE` | 0.733 | 0.571 | -0.162 |
| F1 — `LEGAL_MONEY_QUESTION` | 0.588 | 0.000 | -0.588 |
| F1 — `OPERATIONS_PEERS` | 0.000 | 0.000 | +0.000 |
| F1 — `TOOLS_RECOMMENDATION` | 0.333 | 0.000 | -0.333 |

**What I predicted before I looked:** I predicted that the baseline would get at least 50% of the posts right. I wrote this after I had seen my trained model's numbers (accuracy 0.40, every post predicted as `GROWTH_ADVICE`) but before I saw any baseline number, so it is a prediction about the baseline and not about where training would win. The baseline got 0.567 (17 of 30), so the prediction held, by a margin of two posts.

**Baseline on all three seeds:** The table above is seed 42. Each seed holds out a different 30 posts, so I also scored the baseline on the seed 7 and seed 2024 test sets (`test_split_seed7.csv`, `test_split_seed2024.csv`, `baseline_results_seed7.json`, `baseline_results_seed2024.json`). I rebuilt those two sets with the notebook's own split function, without retraining. The trained model scored 0.400 on every seed.

| Seed | Baseline accuracy | Baseline macro F1 | Trained accuracy | Trained macro F1 |
|---|---|---|---|---|
| 42 | 0.567 | 0.414 | 0.400 | 0.143 |
| 7 | 0.500 | 0.328 | 0.400 | 0.143 |
| 2024 | 0.567 | 0.414 | 0.400 | 0.143 |

The baseline beats the trained model on accuracy and macro F1 on all three seeds, by 10 to 17 points of accuracy. Its own accuracy moves by 6.7 points across the seeds (0.500 to 0.567), under the ten points the notebook treats as unstable, so 0.567 on seed 42 was a slightly friendly draw but not a fluke. Seeds 42 and 2024 give identical baseline numbers on different posts (the two test sets share only 3 posts), which I take to be a coincidence of counts. On seed 7 the baseline got `OPERATIONS_PEERS` and `TOOLS_RECOMMENDATION` all wrong (F1 0.000), so its weakness on those two labels is not specific to one split.

**What my definitions were worth:** I also ran the baseline with bare label names and no definitions (`baseline_bare_results.json`). With the definitions it scored 0.567 accuracy and 0.414 macro F1. With bare names it scored 0.333 accuracy and 0.320 macro F1. So the definitions were worth about 23 points of accuracy. The table above is the run with definitions, and that is the baseline I am reporting. Bare names did better on `OPERATIONS_PEERS` (F1 0.308 against 0.000), but that is 7 posts, so I don't read much into it.

**What the gap actually means:** My fine-tuning added nothing over the baseline in this run. The trained model is worse on accuracy, on macro F1 and on three of four labels, and it ties at zero on `OPERATIONS_PEERS`. The baseline can spread its guesses across the labels using only my definitions, while the trained model collapsed to one label. With 30 test posts, one post is 3.3 points of accuracy, and three labels have fewer than 8 test posts, so these gaps are noisy. The baseline also scores 0.000 on `OPERATIONS_PEERS`, the label I found hardest to separate from `LEGAL_MONEY_QUESTION` when I re-labelled. I'll test whether the gap holds across three seeds before drawing conclusions.

---

## Run Log — Before

Three seeds (42, 7, 2024), 3 epochs, learning rate 2e-5, batch size 16, 128 tokens, on Apple Silicon (`mps`), PyTorch 2.14.1. Raw numbers are in `results_three_seeds_before.json`. Seed 42 reproduces the earlier `results.json`. The verdicts below are my reading of the numbers against the targets in `criteria.md`; the diagnoses come in the next section.

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1. Accuracy | at least 0.60 on every seed | 0.400 | 0.400 | 0.400 | MISSED |
| 2. Per-label F1 | at least 0.55 for `GROWTH_ADVICE`, `OPERATIONS_PEERS`, `LEGAL_MONEY_QUESTION`; at least 0.45 for `TOOLS_RECOMMENDATION` | `GROWTH_ADVICE` 0.571, other three 0.000 | `GROWTH_ADVICE` 0.571, other three 0.000 | `GROWTH_ADVICE` 0.571, other three 0.000 | MISSED |
| 3. Balance | no label above 40% or below 12% of `labels.csv` | 39.0% / 18.0% | same file | same file | MET |
| 4. Consistency | at least 24 of 30 agreement with staff | not run yet | not run yet | not run yet | open until the agreement report |
| 5. Confidence | at least 85% right for predictions with confidence of 0.80 or more, with at least 10 such predictions | none can reach 0.80 (mean confidence 0.34 to 0.38) | same | same | MISSED (unmeasurable) |

**Spread:** accuracy, macro F1 and every per-label F1 are identical across the three seeds (spread 0.000). The notebook calls that "stable enough to trust", but here it means the model made the same mistake on every seed: it predicted `GROWTH_ADVICE` for every test post. Stratified splits put 12 `GROWTH_ADVICE` posts in each 30-post test split, so predicting that label always gives 0.40 on every seed. A spread of zero here is not a stable measure of learning, only of the same collapse.

**Confidence check (criterion 5):** the saved results hold confidence by thirds, not every prediction. The model's mean confidence was 0.34 to 0.38, close to the 0.25 of a four-way guess, so no prediction can plausibly reach 0.80. Under my own rule that makes the criterion unmeasurable, which counts as a miss.

### Confusion matrix

This is the seed 42 matrix (the notebook builds it from the first seed). Rows are true labels, columns are the model's guesses.

| true \ predicted | GROWTH_ADVICE | TOOLS_RECOMMENDATION | LEGAL_MONEY_QUESTION | OPERATIONS_PEERS |
|---|---|---|---|---|
| **GROWTH_ADVICE** | 12 | 0 | 0 | 0 |
| **TOOLS_RECOMMENDATION** | 5 | 0 | 0 | 0 |
| **LEGAL_MONEY_QUESTION** | 6 | 0 | 0 | 0 |
| **OPERATIONS_PEERS** | 7 | 0 | 0 | 0 |

**My biggest off-diagonal number, and what it means:** The biggest cell is 7 real `OPERATIONS_PEERS` posts called `GROWTH_ADVICE`, with 0 going the other way. That direction is not evidence about the boundary between those two labels, because all 18 posts from the other three labels also went to `GROWTH_ADVICE`. The model did not learn any boundary. It learned to always answer the biggest label, and `OPERATIONS_PEERS` shows the largest number only because it is the largest of the three labels that were never predicted.

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
