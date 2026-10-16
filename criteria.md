# Acceptance criteria — TakeMeter

Five criteria that say what "working" means for this classifier, written in
unit 5 **before** anything was trained.

**All five are yours this time.** None are given. You've had two projects of
practice.

An acceptance criterion names a number. *"The model is accurate"* is an
opinion. *"Every label has an F1 of at least 0.60 on the held-out set"* is a
criterion.

Under each, write a sentence or two on **why that number**. A reason that says
something about your data or your taxonomy earns credit — *"I picked 0.60 F1
for `reaction` because it's my smallest label and I only have about 50
examples of it"*. A reason that could be attached to any project does not.

> Missing your own targets next unit costs you nothing. Setting a target so
> easy you can't miss it does.

---

## Pick numbers you can defend

Not numbers that sound impressive. Three labels means a coin-flip guesser gets
about 33%, so a target of 0.40 is barely a target. Your number should sit
somewhere you'd honestly call useful.

**Cover at least three of these five areas.** They're here as prompts, not as a
form to fill in — a criterion that fits none of them is fine if it names a
number.

| Area | A question it could answer |
|---|---|
| Overall accuracy | How often does it need to be right to be worth using? |
| Per-label performance | Is one label allowed to be much worse than the others? |
| Balance | How lopsided can your label counts get before it's a problem? |
| Consistency | If someone else labelled the same posts, how often should you agree? |
| Confidence | Should a confident prediction be right more often than an unsure one? |

Two things worth knowing before you pick numbers, because both will affect
whether you hit them:

- **Your smallest label will have the jumpiest score.** If a label has 50
  examples, about 8 land in the test split. An F1 computed on 8 examples moves
  a lot between seeds. A target for that label should be looser than one for
  your biggest label, and saying so is a good reason.
- **Unit 6 tests across three seeds, and the target has to hold across all
  three.** A target of 0.65 against results of 0.71, 0.62, 0.68 is a **miss**.
  Pick with that in mind — it is stricter than it first sounds.

---

## 1. Overall accuracy

Accuracy on the held-out test split is **at least 0.60 on each of the three seeds** (42, 7, 2024). A seed below 0.60 is a miss, even if the average is above it.

**Why this target:** Four labels means a coin-flip guesser gets 25%, and always guessing `GROWTH_ADVICE` (my largest label, about 40% of posts) gets about 40%. 0.60 is 20 points above that guess, which I'd call useful for sorting posts but not yet reliable. I'm not setting it higher because a test split of about 30 posts moves roughly nine points between seeds on noise alone.

---

## 2. Per-label performance

F1 on the held-out test split is **at least 0.55 for `GROWTH_ADVICE`, `OPERATIONS_PEERS` and `LEGAL_MONEY_QUESTION`, and at least 0.45 for `TOOLS_RECOMMENDATION`**, on each of the three seeds.

**Why this target:** An accuracy target can hide one label the model never learns. `TOOLS_RECOMMENDATION` is my smallest label (about 13% of posts), so only about 4 of its posts land in a 30-post test split and one miss moves its F1 a lot. That is why it gets the looser 0.45. The other three have more examples and distinct vocabulary (LLC, tax, invoice, leads), so I expect them to hold 0.55.

---

## 3. Balance

In `labels.csv`, **no label is more than 40% of rows and no label is less than 12%.** Checked by counting rows per label in the final file.

**Why this target:** My current 361 labelled posts are 41% `GROWTH_ADVICE` and 13% `TOOLS_RECOMMENDATION`. The 40% cap means I have to undersample `GROWTH_ADVICE` before training, so the model can't score well by leaning on it. The 12% floor keeps `TOOLS_RECOMMENDATION` at about 24 of 200 posts, enough that its test split isn't empty.

---

## 4. Consistency

I agree with the staff label on **at least 24 of the 30 staff posts (80%)** in `data/staff_posts.csv`, labelled under the definitions in `data/staff_taxonomy.md`, as reported by `python agreement.py`.

**Why this target:** My own labels were coded by one person, so the staff set is the only test of whether I can apply a written rule the same way someone else did. Staff picked some of the 30 posts because they're ambiguous, so 100% isn't realistic. 80% allows six disagreements, enough to argue for my reading on a few and still show I'm applying the rules consistently.

---

## 5. Confidence

Across the three seeds combined, test predictions with **confidence of at least 0.80 are correct at least 85% of the time.** If fewer than 10 test predictions reach 0.80 across the three seeds, this criterion is unmeasurable, which counts as a miss, not a pass.

**Why this target:** If I'd trust a confident prediction without reading the post, it has to be right more often than the model is overall (0.60 in criterion 1). I pooled the seeds because a single 30-post test split may have only a handful of predictions above 0.80. The minimum of 10 is there so a few lucky confident predictions can't pass it.

---

<!-- ─────────────────────────────────────────────────────────────────────────
     UNIT 6 — read this before you change anything above.

     If a criterion turns out to be BROKEN rather than merely unmet, you can
     revise it, and that earns credit. But never delete or edit the original
     line. Add the revision underneath, like this:

         ## 2. Every label performs acceptably

         The model performs well on all labels.

         **Why this target:** ...

         > **Revised in unit 6:** Every label has an F1 of at least 0.60 on
         > the held-out set.
         >
         > **Why revised:** "performs well" gave me nothing to check. I
         > couldn't produce a verdict from it at all.

     That's a revision because the criterion couldn't be MEASURED.

     Lowering a target because you missed it is not a revision, and it costs
     you the point:

         ✗ "Overall accuracy of at least 0.65" → "at least 0.55", because
            0.65 turned out to be optimistic for 200 examples.

     A number you missed stays where it is, gets diagnosed, and gets a fix
     attempted. That's where the points are.
     ───────────────────────────────────────────────────────────────────────── -->
