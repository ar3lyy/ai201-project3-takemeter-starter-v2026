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

TakeMeter is a text classifier for public posts from r/TheBeatles on Reddit. It sorts posts into three categories: explained opinions, stated opinions, and other posts, such as questions or factual statements without the writer's own opinion. I chose this community because I wanted to work with music and noticed that fans express their appreciation in different ways. The classifier focuses on whether writers support their opinions, not whether their opinions are correct or whether I agree with them.

---

## Label Taxonomy

These labels describe how writers express their opinions, not whether their opinions are correct or whether I agree with them. I read the title and body together.

The quoted examples below are excerpts where a post is longer. Each source link leads to the full post.

### `explained_opinion`

**Definition:** The writer expresses an opinion about the Beatles or their music and supports it with a specific musical part, feature, example, or personal experience connected to that opinion.

**Example 1:**
> "Their early records may not be as adventurous and impressive as the studio work that followed, but there's much more happening than 'jingle rock and roll.' You can already hear sophisticated vocal arrangements, tight ensemble playing, and an impressive ability to transform American rock & roll, R&B, Motown, and pop standards into something distinctly their own."

Source: [Early Beatles... Overlooked and Under appreciated](https://www.reddit.com/r/TheBeatles/comments/1vcw3uk/early_beatles_overlooked_and_under_appreciated/)

This supports the writer's appreciation of their early recordings with details about the vocal arrangements, ensemble playing, and approach to covers.

**Example 2:**
> "I'll go first as always, Fixing a Hole is a song with incredible musical structure that paints what the lyrics are describing in such a perfect way, and was generally misunderstood."

Source: [What's the Most Underrated Beatles Song?](https://www.reddit.com/r/TheBeatles/comments/1ok1gdr/whats_the_most_underrated_beatles_song/)

The writer explains their praise by connecting the song's musical structure to what the lyrics describe.

### `stated_opinion`

**Definition:** The writer expresses an opinion about the Beatles or their music without giving a specific supporting detail or explanation.

**Example 1:**
> "I like the Beatles"

Source: [I like the Beatles](https://www.reddit.com/r/TheBeatles/comments/1vs8q5z/i_like_the_beatles/)

The writer expresses a preference without explaining why.

**Example 2:**
> Title: Would ‘Imagine’ have been good for The Beatles
>
> Body: I think yes

Source: [Would ‘Imagine’ have been good for The Beatles](https://www.reddit.com/r/TheBeatles/comments/1x63z8u/would_imagine_have_been_good_for_the_beatles/)

The writer answers their own question with an opinion but gives no supporting reason.

### `other`

**Definition:** The writer does not express their own opinion, such as a post that only asks a question or shares factual information.

**Example 1:**
> "Which album got you into The Beatles?"

Source: [Which album got you into The Beatles?](https://www.reddit.com/r/TheBeatles/comments/1ww3srf/which_album_got_you_into_the_beatles/)

This asks about other people's experiences without giving the writer's own judgment.

**Example 2:**
> "What song got you into the Beatles"

Source: [What song got you into the Beatles](https://www.reddit.com/r/TheBeatles/comments/1uzmbyi/what_song_got_you_into_the_beatles/)

This asks a question without stating the writer's own opinion.

### The hardest boundary

**Which two labels:** `explained_opinion` and `stated_opinion`.

**The decision rule I used every time:**

An explained opinion must include a specific detail connected to the writer's judgment. Words like "best," "amazing," "beautiful," and "terrible" express feelings but are not enough by themselves. Using "because" does not automatically make an opinion explained.

Naming an instrument alone is not enough. However, identifying a particular musical part or something the instrument does, such as an opening guitar riff, a drum fill, or a bubbling sound, counts as support when the writer connects it to their preference. A technical explanation of how the music works is not required.

A date or song title does not count as support just because it appears beside an opinion. A specific personal memory counts when the writer connects that experience to their feelings about the music.

I check for the writer's own opinion first. If there is none, I choose `other`. If there is an opinion, I check for a supporting detail. A question can still contain an opinion, and a post with several opinions counts as `explained_opinion` if at least one is specifically supported.

Criticism counts as an opinion, just like praise. For example, calling a song too long expresses a judgment even if the writer never says "I dislike it."

### Real borderline post

Source: [“The Hollies have a very Beatlesque sound.”](https://www.reddit.com/r/TheBeatles/comments/1w7lmgh/the_hollies_have_a_very_beatlesque_sound/)

This post was difficult to place between `explained_opinion` and `stated_opinion`. The writer finds a recording interesting and mentions the sound of a 12-string Rickenbacker, but does not identify a particular riff, passage, or action that makes the sound interesting.

Under my decision rule, I classify it as `stated_opinion`. Naming the instrument and generally mentioning its sound is different from pointing to a particular musical part the writer appreciates.

### AI Boundary Stress Test

ChatGPT generated eight fictional posts to test the boundaries of my labels. These practice posts are not part of my real dataset.

My choices matched the rules on five of the eight examples. The three differences helped me clarify that:

- Criticism counts as an opinion, just like praise.
- A question such as "Who else thinks..." can express the writer's own opinion.
- Adding a fact or question does not cancel an opinion elsewhere in the post.

I updated my decision rules to cover these cases.

During dataset review, I also clarified that identifying a particular musical part, such as a guitar riff, can support an opinion without requiring a technical explanation.

---

## The Dataset

<!-- Where you collected from, how you labelled, your counts, and three hard
     cases. -->

**Where the posts came from:**

My dataset contains 200 unique public posts from r/TheBeatles on Reddit. Each example includes the post title and any collected body text. The dataset includes discussions about songs, albums, performances, band history, and fans' experiences.

ChatGPT helped collect the posts through accessible public pages after Reddit blocked attempts to download posts in a batch. Source links are recorded in the `note` column. The examples are kept in one CSV rather than being manually split into training and test files.

My working file is named `beatles_labels_reviewed.csv`. The notebook will need to use that filename, or the file can be copied to `labels.csv` to match the starter's default.

**How I labelled them:**

<!-- Cold first? Pre-labelled with AI and corrected?
Say so plainly — the disclosure is required, not penalised. -->

I labeled the first 20 posts independently without AI help. ChatGPT pre-labeled the remaining 180 posts using the label definitions. I read each post, reviewed the suggested labels, and corrected the ones I disagreed with.

The notes identify the first 20 examples as `cold` and the remaining examples as `AI-prelabeled`, with `reviewed` recording my later review. I also revised one of my initial cold labels during review, and its original label is recorded in the notes.

**Counts per label:**

| Label | Count | Share |
|---|---|---|
| explained_opinion | 47 | 23.5% |
| stated_opinion | 72 | 36% |
| other | 81 | 40.5% |
| **Total** | **200** | **100%** |

No label accounts for more than 70% of the dataset. The largest category is `other` at 40.5%.

**Three hard cases**

<!-- Any post that made you pause: what it was, which two labels it could have
     been, and what you chose. These are worth more than the easy 190. -->

These three examples are cases where my reviewed label differed from the AI's suggested label.

**1.**
> **The post:** “Mono vs stereo?”
>
> The writer compared mono and stereo versions of several tracks, said they did not hear much difference, and asked why other people prefer mono.
>
> **Could have been:** `other` or `stated_opinion`.
>
> **I chose, because:** `stated_opinion`. Although the post mainly asks a question, the writer also gives their own assessment of the recordings. They do not identify particular similarities or differences in the sound, so I did not count it as an explained opinion.

Source: [Mono vs stereo?](https://www.reddit.com/r/TheBeatles/comments/1w57ijs/mono_vs_stereo/)

**2.**
> **The post:** “Does anyone know what that bubble sounding sound in It Is 'He' (Jai Sri Krishna)?”
>
> The writer says the song is probably their favorite because of its instruments, then asks about a particular instrument that makes a bubbling sound.
>
> **Could have been:** `stated_opinion` or `explained_opinion`.
>
> **I chose, because:** `explained_opinion`. The writer does more than name an instrument. They identify a distinctive sound and connect the instrumentation to their preference. The question at the end does not cancel the opinion.

Source: [The bubbling sound in “It Is 'He'”](https://www.reddit.com/r/TheBeatles/comments/1vthent/does_anyone_know_what_that_bubble_sounding_sound/)

**3.**
> **The post:** “What’s your favorite Beatles song that doesn’t get much love or recognition?”
>
> The writer praises “What You’re Doing” and points to its opening drum part and guitar riff.
>
> **Could have been:** `stated_opinion` or `explained_opinion`.
>
> **I chose, because:** `explained_opinion`. The opening drum part and guitar riff are particular musical parts, rather than just instrument names. The writer identifies those parts as things they appreciate, which is enough support under my clarified rule.

Source: [A favorite song that gets less recognition](https://www.reddit.com/r/TheBeatles/comments/1qly1fl/whats_your_favorite_beatles_song_that_doesnt_get/)

---

## The Training Run

<!-- Your starting model, your settings, and anything you changed and why. -->

**Base model:** `distilbert-base-uncased`

**Settings:**

- Epochs: 3
- Learning rate: 0.00002
- Batch size: 16
- Maximum input length: 128 tokens
- Seed: 42
- Device: `mps` — Apple Silicon GPU
- PyTorch version: 2.15.0

**Anything I changed from the defaults, and why:**

I kept the default training settings for this first run. I
changed the label map to match my three categories and set the
CSV path to `beatles_labels_reviewed.csv`.

**Split sizes:**

| Label | Train | Validation | Test |
|---|---|---|---|
| explained_opinion | 33 | 7 | 7 |
| stated_opinion | 50 | 11 | 11 |
| other | 56 | 13 | 12 |
| **Total** | **139** | **31** | **30** |

The notebook created a stratified split. The explained_opinion
label has only seven test examples, so its evaluation scores
may vary noticeably between seeds.

Training completed for all three epochs. Section 5 evaluated
the held-out test set and saved `results.json` and
`test_split.csv`.

---

## How I Used AI

<!-- Two specific moments — what you asked, what came back, what you changed.

     ⚠️ Plus disclosure of any pre-labelling. If you had a model pre-label a
     batch and then read and corrected every one, say that. It's an allowed
     workflow and disclosing it costs you nothing. Not disclosing it is the
     problem. -->


- **What I asked for:** Help collecting public posts and organizing them into a CSV, followed by pre-labeling the posts after my first 20 independent examples.
- **What came back:** ChatGPT collected posts, retained source links, and suggested labels for 180 examples.
- **What I changed:** I reviewed every example and changed labels I disagreed with. For example, I changed the post about the opening drum part and guitar riff in “What You’re Doing” to `explained_opinion`.

**Pre-labelling disclosure:**

I independently labeled the first 20 posts without AI assistance. ChatGPT pre-labeled the remaining 180 posts, and I read and reviewed every one. The CSV notes preserve that labeling history. I also revised one of my first 20 labels during the later review.

ChatGPT also helped draft README wording, explain evaluation measures, and draft acceptance criteria. I selected the 75% accuracy target and the 70% maximum label-share target; ChatGPT suggested the other three targets.

<!-- ═══════════════════════ UNIT 6 — THE TEST ═══════════════════════

     Don't fill these in during unit 5.
     ═══════════════════════════════════════════════════════════════════ -->

---

## Baseline vs. Trained

<!-- Both models on the same posts. `python baseline.py --trained results.json`
     prints this table for you. -->

| Measure | Baseline | Trained | Difference |
|---|---|---|---|
| Overall accuracy | 0.400 | 0.367 | -0.033 |
| Macro F1 | 0.399 | 0.231 | -0.168 |
| F1 — `explained_opinion` | 0.625 | 0.000 | -0.625 |
| F1 — `other` | 0.111 | 0.588 | +0.477 |
| F1 — `stated_opinion` | 0.462 | 0.105 | -0.356 |


**What I predicted before I looked:**
I predict the baseline will perform better than my trained
model because it can use my detailed label definitions directly,
including the rules I clarified during labeling. My trained
model had only 139 training examples to learn those distinctions.

**What the gap actually means:**
The baseline correctly classified 12 of the 30 test posts,
compared with 11 for my trained model. Its accuracy was
3.3 percentage points higher, and its macro F1 was also higher.

The baseline performed better on explained_opinion and
stated_opinion, while my trained model performed better on
other. Fine-tuning did not improve overall performance on
this split. The confusion matrix and three-seed runs will
help me investigate this pattern.
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
|  |  |  |  |
|  |  |  |  |
|  |  |  |  |

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
> **The post:**
>
> **Staff said / I said:**
>
> **My call, and why:**
>
> **Which pile:**

**2.**
> **The post:**
>
> **Staff said / I said:**
>
> **My call, and why:**
>
> **Which pile:**

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

## Milestone 1: Community Reading

### Community I Chose

I chose r/TheBeatles on Reddit as my public community. I wanted to work with music, and this community has discussions about the Beatles’ songs, albums, performances, and fans’ experiences. I read 30 posts to see what differences appeared in the way people discussed the band.

### Three Differences I Noticed

1. **Asking questions versus sharing opinions.**
   Some posts invite other fans to answer a question, such as which album introduced them to the Beatles. Other posts mainly share what the writer thinks about a song, album, or the band.

2. **Personal preferences versus factual claims.**
   Some writers describe their own tastes, such as their favorite song. Others make claims about the band’s history or recordings that someone could check. A preference does not have one correct answer, while a factual claim can be checked even if it turns out to be wrong.

3. **Explaining opinions versus simply stating them.**
   Some writers explain their opinions by discussing things like the instruments, vocals, lyrics, or how songs fit together. Others say they love something or call it the best without giving a specific reason.

### The Difference I Want to Explore

I am most interested in whether people explain their opinions or simply state them. While discussing this distinction, I noticed that words like “best” and “amazing” express appreciation but do not explain what makes the music good.

My starting idea is that an explained opinion needs a specific reason connected to the music. Just mentioning an instrument or saying the vocals are amazing would not automatically count as an explanation. I still need to test this distinction and decide how to handle posts that mainly ask questions or share facts.

**Later clarification:** After testing the labels and reviewing the dataset, I decided that identifying a particular musical part, such as a guitar riff or an opening drum part, can count as support when connected to the writer's opinion. Simply naming an instrument is still not enough.

---

📖 **How to run this project: [RUNNING.md](RUNNING.md)**