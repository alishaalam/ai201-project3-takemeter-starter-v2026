# Milestone 1 — Community and reading notes

## Community

**r/smallbusiness** (reddit.com/r/smallbusiness) — public, no login, high volume, real spread in post quality.
Not built around one show or release, so no spoiler problem.

- **What I read:** 40 text posts (self-posts over 150 characters), sampled evenly from the
  most recent 400 submissions (13–16 Oct 2026).
- **How I got them:** Arctic Shift public archive API. Reddit's own JSON endpoint returns 403 to scripts.
- **Yield:** of 400 submissions, 134 (34%) are `[removed]`/`[deleted]` and 246 (62%) are usable
  text posts over 150 characters. Median length 685 characters. 200 usable posts means
  pulling roughly 325 submissions at minimum; see "Things I noticed" for why to pull more.

## What the 40 looked like

| Kind of post | Count | Share | Example |
|---|---|---|---|
| Asks for help with own situation, concrete details | 19 | 48% | 1x7ery0, 1x6ohnl, 1x5xd27 |
| Asks, little or no context of their own | 8 | 20% | 1x69lbx, 1x6szkk, 1x5mj0u |
| Prompt or poll for the community, no own situation | 6 | 15% | 1x6fcqg, 1x6ecvu, 1x5be7c |
| Promotion or solicitation (overt or disguised) | 5 | 13% | 1x5s4wd, 1x60oy9, 1x55xgm |
| Opinion or advice stated as fact | 2 | 5% | 1x7jh3k, 1x6d70q |

One reader, my own judgment on the boundaries. 34 of 40 contain a `?`, so this is a
question-asking community. Posts that assert are the exception.

## Candidate distinctions

Each one is something I can point at in the text.

### 1. Names concrete specifics from the poster's own situation, or stays generic

- **Does:** 1x7ery0 gives the fine ($18,565, £14,280), the processor and the sequence of events.
  1x6ohnl gives the ad cost ($400), monthly revenue ($12–15K) and the break-even logic (3–4 jobs).
  1x5vwyf gives $1.2k in 4–5 months and a 40/60 equity split. 1x742pr gives a lease end date and a city.
- **Doesn't:** 1x69lbx ("What is your first step?"), 1x6szkk (a list of free security tools, no
  business detail), 1x5mj0u ("I don't know what I'm doing wrong"), 1x7g41u.
- **Caveat:** a digit in the body is not enough. 21 of 40 bodies contain one, including the pure
  ad 1x5s4wd (street address, phone) and the solicitation 1x5yuxm (₹1,500, a phone number).

### 2. Shows own reasoning or work already done, or just asks

- **Does:** 1x6ohnl weighs the cost against the likely return. 1x5xd27 lists four channels already
  tried and says the results were weak. 1x64j1u reviewed the footage and lays out the timeline.
  1x66gng considers a grooming salon and worries about local saturation.
- **Doesn't:** 1x69lbx, 1x7g41u, 1x5be7c ("I imagine it can also be a headache" is a guess, not
  an experience), 1x7aobp ("what would you do to grow the business").
- **Why it's separate from #1:** 1x7aobp has facts (6 sales staff, two warehouses, Kerala) and no
  reasoning. 1x6d70q has reasoning (the Nike argument) and nothing from the poster's own business.

### 3. Genuine post, or promotion

- **Overt promotion:** 1x5s4wd (address, email, phone, no question at all), 1x60oy9 ("[For Hire]"),
  1x5yuxm (WhatsApp number), 1x54by6 (offers to take on owners' books).
- **Disguised:** 1x55xgm is a success story with figures (102 emails, a $3k site) and ends by naming
  the tool used for all outreach. It passes distinction #1, so absence of numbers can't be the promo test.
- **Genuine:** the other 35, though 1x5yuxm blends a real request with a contact solicitation.
- **Caveat:** the boundary is the contact details or the product plug, not the topic.

### 4. About the poster's own situation, or a prompt for the community to discuss

- **Own situation:** 1x66gng (dog groomer choosing a business), 1x6ohnl, 1x5ekhm (three bakeries,
  a task backlog).
- **Community prompt:** 1x6fcqg ("What's the manual process in your business that simply refuses to
  die?"), 1x6ecvu, 1x5qm45, 1x5be7c. These pose a question about the audience, not the author.
- **Caveat:** 1x5qm45 includes the author's own starter list, and 1x66gng and 1x5ekhm end with a
  general question to everyone. The distinction is where most of the post's content comes from.

### Considered and dropped

| Idea | Why not |
|---|---|
| Asks vs. asserts | 34 of 40 ask. Only 1x7jh3k and 1x6d70q assert. Too lopsided to be a label axis |
| Long vs. short | 1x500a7 (3,844 chars) is an employee's rant about a boss. 1x55xgm (1,392) is a plug. Length is not quality here |
| High score/comments | See below. The snapshot makes it unusable |

| Distinction | Easy to apply? | Main risk |
|---|---|---|
| 1. Specifics | Yes, mostly visible on a skim | Numbers in promo posts; "specific" is a spectrum |
| 2. Own reasoning | Medium, needs a full read | Overlaps #1 in the middle; two labellers may split |
| 3. Genuine vs. promo | Yes for overt, hard for disguised | Likely a small class (see below) |
| 4. Own situation vs. prompt | Medium | Hybrids (1x5qm45) |

## Things I noticed that affect later milestones

- **Score and comment counts are not usable.** 22 of 40 have score 1 and 16 have 1 comment or fewer,
  which is an ingestion-time snapshot, not engagement. The outlier 1x64j1u (188 / 169) is detailed,
  but 1x6ohnl is just as detailed and sits at 1 / 1.
- **Promo is probably a thin class.** 5 of 40 (13%). At 200 posts that is about 25, near the
  point where test-split F1 jumps between seeds. The 134 removed posts may hold much of the spam,
  but that is my guess, and their text is gone, so I can't recover it.
- **Pull more than 325.** Hitting 200 usable posts needs ~325 submissions, but choosing a balanced
  set needs headroom. ~800 (about 6 days) is a safer pull.
- **The question is often in the title.** 1x5knht, 1x6s1g4 and 1x7mwr9 pose it there and use the body
  for context. Concatenating title + body into `text` is probably right. Decide in Milestone 4.
- **Non-native English could leak into labels.** Many posters are in India, Egypt, Brazil, China,
  Morocco, Singapore (1x7jh3k, 1x703l0, 1x75yd0). A model could learn "rough grammar = low
  substance." I should label on content, not polish.
- **Some prompt posts read as templated** (1x6fcqg, 1x6ecvu, 1x5be7c). I can't verify authorship
  and won't treat it as a feature.
- **Timing.** Three days in mid-October (Q4). Topics skew to e-commerce, services and tools.
  A longer window would broaden the mix.

## Assumptions

- I did not read the r/smallbusiness rules, so I'm assuming moderators shape what survives.
- Post kinds are my judgment from one reading pass, not a second reader.
- 40 posts from a 3-day window is a small, recent sample.

## Commit

Community: **r/smallbusiness**.
Candidate distinctions: **(1) concrete specifics vs. generic, (2) own reasoning vs. bare ask, (3) genuine vs. promotion, (4) own situation vs. community prompt.**

## Open Questions

- Which of these four become your labels in Milestone 2? #1 and #2 overlap in the middle, so
  using both needs a rule for where they split.
- Is a ~13% promo class enough, or should Milestone 4 oversample it deliberately?
- Should hybrids (1x5qm45, 1x5yuxm) get a tiebreak rule, or be left out of the 200?
- Does the 34% `[removed]` rate hold on a different week, or was this a spam-heavy window?

---

## Milestone 2 addendum — how the four labels were derived

The posts were first coded by intent, bottom-up from a larger pull. The four intent groups were then re-cut by topic into the hybrid labels used in the README. Polls and market-research posts were removed.

| Step | Count |
|---|---|
| Submissions pulled (9–16 Oct 2026, Arctic Shift) | 800 |
| `[removed]` / `[deleted]` | 268 |
| Self-posts of 150 characters or fewer | 32 |
| Usable text posts | 500 |
| Dropped as promotion (49 by rule and manual check, 51 more caught while reading) | 100 |
| Unlabelable (1x2mmhv, asks permission to ask) | 1 |
| Coded by intent | 399 |
| Dropped as polls (`peers_poll`, 31) or market-research / lead-gen (7) | 38 |
| **Posts labelled** | **361** |

**Step 1, intent coding.** I read every remaining post (title, plus the start and end of long posts) and wrote a free-text note of what the poster wanted. Grouping the notes gave four intent groups, split by what the poster does with the replies: `wants_plan` 156, `wants_peer_input` 110, `wants_verdict` 68, `wants_answer` 65. A first keyword grouping gave 39% / 28% / 18% / 16%. Reading each group showed that "how did you get your first clients?" is asked of peers but is really a request for a method, so those moved to `wants_plan`.

**Step 2, why re-cut by topic.** Intent labels shared vocabulary. A verdict about an LLC and a verdict about an ad budget have little in common, and a classifier learns words, not intent. Sorting by what the poster is asking *for* gave four labels with their own vocabulary.

| Label | Posts | Share | How it was built |
|---|---|---|---|
| `GROWTH_ADVICE` | 148 | 41.0% | `plan_start`, `plan_grow`, customer- or idea-related verdicts, and a few peer posts about getting customers |
| `OPERATIONS_PEERS` | 103 | 28.5% | `plan_fix`, vents, peer discussion and shared-experience posts about running the business |
| `LEGAL_MONEY_QUESTION` | 62 | 17.2% | `answer_fact`, entity / loan / buy-sell verdicts, tax and insurance questions |
| `TOOLS_RECOMMENDATION` | 48 | 13.3% | `answer_pick`, plus vendor and supplier questions from other groups |

The first pass was a rule on the old child label (`answer_pick` → tools, `answer_fact` → legal, `plan_start` / `plan_grow` → growth, `plan_fix` → operations, other verdicts → growth). I then overrode 74 of the 361 posts by hand, mostly verdicts and `plan_fix` posts whose topic was legal, tools or growth.

**Step 3, what was removed.** The user's rule is that polls and market-research posts are gaming the subreddit, not asking for help. That covers 31 `peers_poll` posts and 7 flagged as promo-ish, market-research or templated lead-gen (1x5r1zv, 1x4uszl, 1x4mgbc, 1x4lynu, 1x480oh, 1x1uv0c, 1x6l0ik). Six posts flagged only "promo-light" stayed (1x78tus, 1x6zwfo, 1x6cita, 1x5jsuh, 1x4u8j0, 1x43dqw).

**Trade-offs and flags**
- `GROWTH_ADVICE` is 41.0%, just over the 40% ceiling. The smallest label, `TOOLS_RECOMMENDATION`, is 13.3%, about 27 of 200 posts.
- Intent is a harder distinction to label consistently than topic, but topic labels here still depend on judgment (is "how do I price a service" growth or money?). I labelled alone, so the unit 6 agreement check is the first test.
- Verdict posts are spread over all four labels (41 growth, 18 legal, 5 operations, 4 tools). Intent is no longer its own label.
- The poll removal was done as a group, so some genuine polls went with the market-research ones. "Promo-light" posts remain and may still hide some lead-gen.
- Some intents are inferred. 1x6c16w and 1x3nag0 state no ask, and I inferred one. A second reader could infer a different ask.
- The pulled pool and the label file are in my scratchpad, not the repo. Milestone 4 needs a fresh pull of the same shape.

## Open Questions

- Should the six repost pairs be collapsed to one post each in Milestone 4, or kept out of the same split?
- Do you want the two French posts (1x3a612, 1x39qys) kept? The model is English-trained.
- Should `GROWTH_ADVICE` be undersampled in Milestone 4, or split (for example, start-a-business vs get-customers)?
