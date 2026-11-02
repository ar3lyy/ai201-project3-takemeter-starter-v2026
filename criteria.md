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

The model must achieve at least 75% accuracy on the held-out test
set for each of the three evaluation seeds: 42, 7, and 2024.

**Why this target:**

I chose 75% because correctly sorting three out of four Beatles
posts would make the classifier useful to me. I expect some
mistakes because a post can mix questions, facts, and opinions.

---

## 2.

No label may account for more than 70% of the full labeled dataset.

**Why this target:**

Many posts I read ask questions without expressing an opinion,
which could make `other` dominate my dataset. This limit helps
keep the classifier from relying too heavily on one category.

---

## 3.

Each of the three labels must achieve an F1 score of at least
0.60 on the held-out test set for each evaluation seed:
42, 7, and 2024.

**Why this target:**

The classifier needs to recognize all three categories, not just
the most common one. I chose 0.60 because the boundary between
stated and explained opinions can be difficult, and a small test
set can make the scores vary between seeds.

---

## 4.

When I independently relabel the same 20 cold examples, I must
agree with at least 16 of my original labels, or 80%. During the
second pass, I will use my written definitions without viewing
the original labels or using AI. I will compare the labels only
after completing the pass.

**Why this target:**

Our practice exercise showed that questions and negative opinions
can be confusing. I want to check whether my clarified rules help
me label those cases consistently. This target allows up to four
disagreements before I would consider my consistency too low.

---

## 5.

Recall for `explained_opinion` must be at least 0.70 on the
held-out test set for each evaluation seed: 42, 7, and 2024.

**Why this target:**

The main distinction I chose is whether writers support their
opinions with specific reasons. I want the classifier to find
at least 70% of the genuinely explained opinions rather than
mistaking them for unsupported opinions or factual posts.

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
