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

## 1.

The trained classifier will reach at least **0.65 overall accuracy** on the
held-out test set for each of the three seeds.



**Why this target:** With three labels, a roughly even random guess is about
0.33 accuracy. A 0.65 target asks the model to learn useful distinctions from
the short, informal `r/soccer` posts without pretending that every reaction
and hot take has an obvious boundary.



---

## 2.

Every label will have an **F1 score of at least 0.60** on the held-out test set
for all three seeds.



**Why this target:** `reaction` overlaps with unsupported `hot_take` because
both can be emotional and brief. The 0.60 floor allows that harder boundary
to be noisier than the overall score, while still requiring the model to
recognize all three labels instead of succeeding mainly on `analysis`.



---

## 3.

The labelled dataset will be balanced enough that **each label makes up at
least 20% and at most 50% of the 200 posts**.



**Why this target:** A 20% minimum gives each label about 40 examples and
roughly six test examples before seed variation, so `reaction` or `hot_take`
cannot disappear into a tiny test count. A 50% maximum prevents the model
from getting a deceptively good score by mostly guessing the dominant kind of
football comment.



---

## 4.

On a blind second labelling of **30 posts** by another person using the same
definitions, the label agreement will be at least **80%**.



**Why this target:** The most important risk in this taxonomy is inconsistent
judgment at the `analysis`/`hot_take` boundary. An 80% agreement target leaves
room for genuinely ambiguous banter while requiring the written decision rule
to produce a stable signal for most posts.



---

## 5.

For each seed, the model's most-confident third of predictions will have at
least **0.70 accuracy and will outperform its least-confident third by at least
0.10**.



**Why this target:** The short posts often omit context, so confidence should
help identify the examples that are safer to use automatically. A 0.10 gap is
large enough to be useful, while 0.70 for the most-confident third is less
fragile than demanding near-perfect confidence from a small held-out set.



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
