# ELFI — the method, on the synthetic data

What the seven notebooks in this folder do, what comes out of them, and what each
result is checked against.

All notebooks run with `SOURCE = "synthetic"`. The same chain also runs on the real
employee survey, in a parallel copy that is not part of this package.

---

## Why the chain exists twice

The real comments are employee feedback and must not leave the company. To be able to
show the method, share it and make it traceable, there is a synthetic twin: 5,000
generated questionnaires with the same structure — the same 54 items, the same eight
dimensions, the same export format.

**What this folder achieves.** It shows that the chain runs from end to end, and it is
the basis for the shareable dashboard.

**What it cannot achieve.** The synthetic comments were assembled from building blocks
selected by a drawn emotion label and a valence level. Emotional content is an **input**
to the generator here, not a property of observed feedback. Result magnitudes therefore
do not transfer to real data — the ordering of methods does, the effect sizes do not.

The most important deviation comes first: here **half** of the respondents write a
comment. On real data the comment rate is considerably lower, which makes the selection
effect weigh far more heavily there.

---

## The chain

```
synthetic_dataset/synthetic/synthetic_data_transformed.csv
      │
01    The data           cleaning, quality signals, dimensions
02    Valence            how pleasant is the comment?
03    Arousal            how activating is it?
04    Circumplex         both axes together
05    Themes             what is written about?
06    Pipeline           the export for Power BI
      │
07    A data-driven axis     side branch, checks the method itself
```

One step is missing here: the synthetic data arrives ready-made in the package. The
real chain has a notebook `00` in front of this, which merges the survey's Excel files.

---

## Three things worth knowing before running it

**The data folder is called `synthetic_dataset`.** Every notebook sets
`DATA = Path("../synthetic_dataset")` at the top and derives its input, its output
folder and `config/` from it. The folder has to sit next to `notebooks/`.

**Every notebook has a second branch that is not used here.** The cell with the paths
contains an `if SOURCE == "synthetic": … elif SOURCE == "real": …`. The real branch
comes from the parallel chain on the actual survey and points nowhere in this package —
the files it looks for are not included and are not distributed. Since `SOURCE` is
fixed to `"synthetic"`, it is never entered. It was deliberately left in place so that
it stays visible that both chains share the same code and differ only in the data
source.

**Notebook 03 needs two files that are not in the package.** The word lists BAWL-R and
the IMS Stuttgart norms are published research data with their own terms of use and are
not redistributed. Where to obtain them is in `REFERENCES.md`; a note at the start of
03 says where they belong.

---

## Step 01 — The data

**What happens.** Text answers become numbers (1 to 5), the 54 items are condensed into
eight thematic dimensions, quality signals are computed.

**Result.**

```
5,000 questionnaires, 84 columns
of those with a comment: 2,500  (50 %)
8 dimensions + overall score
2,268 "cannot assess" answers → counted as missing, not as neutral
12 business units, 5 waves / 14 runs
median completion time: 8.9 minutes
```

**Quality signals.** They are flagged, not removed.

```
fast responses (bottom 5 % of durations)      238   4.8 %
near-uniform answering (spread < 0.3)         104   2.1 %
both at the same time                           2   0.0 %
```

*What "uniform" means:* someone who ticks almost the same value on all 54 items has a
standard deviation near zero. The threshold of 0.3 falls inside a gap in the
distribution, so it separates a distinct group rather than slicing off an arbitrary
percentage.

---

## Step 02 — Valence

**What happens.** Valence means: how pleasant or unpleasant is the experience the
comment reports. Two methods compete.

*The baseline:* `oliverguhr/german-sentiment-bert`, a model trained for German
sentiment.

*The challenger:* `gemma2:9b`, a language model, running locally via Ollama, with an
explicit scale from −1 to +1 and no knowledge of the survey answers — hence "blind".

**What it is validated against.** Six survey values given by the same person:
`arbeitsfreude`, `arbeitgeber_empfehlung`, `teamstimmung`, `work_life_balance`,
`engagement_score`, `overall_score`.

**Result.**

```
method                       agreement   values at the edge   distinct values
BERT, simple difference         +0.304          65 %                2,497
BERT, neutrality-adjusted       +0.306          68 %                2,497
Gemma 2 · 9B, blind             +0.393           0 %                   18

Winner: Gemma 2 · 9B
```

*"Values at the edge"* means: the share of comments beyond ±0.9. For BERT that is two
thirds — the model pushes everything to the extremes, which is useless for an ordering.

**The winner's limitation.** Gemma uses only **18 distinct values** and puts 21 % of all
comments on the same one (+0.50).

**Comparison with the real survey.** The figures below are properties of the *methods*,
not statements about the respondents.

```
method                       here      real
BERT, simple difference     +0.304    +0.243
BERT, neutrality-adjusted   +0.306    +0.259
Gemma 2 · 9B, blind         +0.393    +0.389
distinct values                 18        27
```

The **ordering** of the methods is identical — Gemma first, then BERT. The **gaps** are
not: on synthetic text BERT reaches considerably more, because generated comments carry
a clearer sentiment signal than real feedback does. That is precisely the limit of
synthetic text: orderings transfer, effect sizes do not.

---

## Step 03 — Arousal

**What happens.** Arousal means activation: "I am furious" and "I have given up" are
both unpleasant, but one is agitated and the other is flat.

**The problem, stated first.** The survey does not ask about activation, so there is no
direct criterion. The workaround: following an idea of Warr (1990), a substitute axis is
rotated out of the survey answers, and an independent word list serves as referee.

```
criterion from the survey     overlap with the valence criterion: −0.001
referee BAWL-R                covers 568 of 5,940 word forms
                              scores 77 % of the comments
IMS Stuttgart norms           cover 5,561 of 5,940 word forms
```

**Result.**

```
method                                 ~ BAWL-R   ~ valence
hand-made emotion mapping                +0.030     +0.184
IMS lexicon, mean over all words         +0.165     +0.179
IMS lexicon, mean without stopwords      +0.221     +0.012   ← winner
IMS lexicon, peak word                   +0.167     −0.081
IMS lexicon, three most extreme          +0.146     −0.101
language model gemma2                    +0.094     −0.161   ← rejected
```

**Why the last column decides it.** A method that correlates strongly with valence is
measuring the first axis again instead of the second. The winner sits at +0.012, so it
is practically independent. Dropping the stopwords is the single largest gain:
`+0.165 → +0.221`.

**The same winner as on the real data** — the IMS lexicon, mean without stopwords,
there as well. On real data the language model fails far more clearly, however: there it
correlates at **−0.661** against valence, here only at −0.161. That too is a figure
about the method rather than the respondents — it says the model confused two axes. The
generator never knew activation as a parameter, which is why this dataset has the least
to say about this axis in any case.

---

## Step 04 — The circumplex

**What happens.** Both axes are brought together, so that every comment becomes a point
in a plane — the circumplex model after Russell (1980), with valence and arousal as the
two core properties of any felt state (Lisa Feldman Barrett).

**Two corrections that are not cosmetic.**

*Valence is quantised.* Only 18 distinct values, so the circumplex draws itself as a
comb of vertical stripes. Before drawing, each value is spread across the interval it
stands for.

*Arousal is rank-normalised.* This is why **0 on the arousal axis does not mean "no
activation" but "median activation"**.

**Result.**

```
2,500 comments with both axes
correlation between the axes: +0.012   (near zero, they measure different things)

tense / nervous              unpleasant   high     649   26.0 %
exhausted / resigned         unpleasant   low      647   25.9 %
enthusiastic / energised     pleasant     high     602   24.1 %
calm / relaxed               pleasant     low      602   24.1 %

unpleasant in total: 51.9 %
```

**This is where the character of the dataset shows.** The four quadrants are almost
equal in size, because the generator drew the emotion labels evenly. Real feedback is
not evenly distributed — which imbalance shows up there is a finding and does not belong
in a shareable document. What remains to be recorded: a distribution taken from this
dataset says nothing about a workforce.

---

## Step 05 — Themes and engagement

**What happens.** Two questions: what is written about, and can engagement be predicted
from the language.

**Themes.** BERTopic embeds every comment into a 384-dimensional space
(`paraphrase-multilingual-MiniLM-L12-v2`), reduces it with UMAP, groups it with HDBSCAN.
`gemma2:9b` supplies the names. This run fits **its own model** and never sees the one
trained on real comments.

```
84 themes found, reduced to 35  →  34 themes plus an outlier group
no theme: 329 comments (13 %)

Informationsfluss im Unternehmen   212    8 %
Strategiekommunikation             207    8 %
Arbeitsumfeld und Wohlbefinden     205    8 %
Arbeitsbelastung                   196    8 %
Flexible Arbeitszeit               191    8 %
```

*Outliers.* HDBSCAN leaves comments it cannot place with confidence in no group at all.
"No theme" is therefore a real category, not a missing value.

*For comparison:* real feedback is thematically far broader. Grown over several years, a
few thousand comments produce more than two hundred distinguishable themes, and no
reduction is applied there. Reducing to 35 suits 2,500 uniform generated comments;
transferred to a real corpus it presses a large part into a single catch-all theme.

**Engagement.** Can the language of a comment predict the engagement score the same
person gave in the closed questions? Tested with cross-validation.

```
model                            R²      spread
baseline (mean)              −0.0017     0.0015
1 · valence only              0.1858     0.0247
2 · + arousal and length      0.1851     0.0234
3 · + TF-IDF                  0.1844     0.0216
    random forest             0.0989     0.0310
```

*What R² means:* the share of the variation in engagement the model explains. 0 means
"no better than the mean", 1 means "fully explained". 0.19 means a single sentiment
value taken from the text explains almost a fifth of the variation in a questionnaire
score it never saw.

*The yardstick for "real".* Cross-validation splits the data into blocks and computes
several times over. How much the result wobbles in doing so — here 0.025 — is the
yardstick: an improvement smaller than that wobble is noise.

**The result is unambiguous: only valence counts.** Arousal, comment length and
thousands of TF-IDF features improve nothing beyond the fold-to-fold spread. The random
forest is even worse than the linear models.

*On real data this can come out differently, and then a check is needed.* If vocabulary
improves the prediction, that does not necessarily mean it says something about the
person's attitude. Confounders of that kind are to be expected on real data and belong
checked: a model that knows only the structural features shows how much of the apparent
text gain actually comes from the text. This notebook has the test built in, so that it
runs on any corpus.

---

## Step 06 — The pipeline

**What happens.** The export for Power BI: a star schema of fact tables and dimensions,
plus a smoothed density field for the heatmap.

**Data protection in the model.** Measures carrying the suffix *(geschuetzt)* return
`BLANK()` instead of a value below five responses. With a single comment the value would
otherwise be attributable to one person. The density field has a second threshold at 50
comments — below that, a smoothed surface shows structure the data does not support.

On this dataset these rules are uncritical, because nobody is identifiable. They are
built in here because the same pipeline runs on the real data.

**Documentation included.** `power_query.m` with typed loading blocks, `measures.dax`,
`relationships.md`.

---

## Step 07 — A data-driven axis (side branch)

**The question.** Both axes were set by this project: valence was asked for, arousal was
assembled from word norms that are called "arousal". Can the axes be **found** in the
data instead?

**What happens.** Following the procedure of Semo et al.: a PCA over the 27 emotion
probabilities from step 03, the first two components rotated with varimax, and only then
a look at what came out.

**Result.**

```
component   variance   cumulative        relation to the project's axes
PC1           9.0 %       9.0 %      valence −0.767   arousal +0.043
PC2           6.3 %      15.3 %              +0.414           +0.124
PC3           5.7 %      21.0 %              +0.515           +0.057

rotation angle: 24.9 degrees
varimax criterion: 0.5966 before, 0.7824 after

best agreement reachable over all rotations:
  the valence measure    0.799
  the arousal measure    0.161
  the arousal criterion  0.088
```

**The valence axis is confirmed, and clearly.** A procedure that is never told what
valence is finds an axis that agrees with the valence measure at 0.80.

**The arousal axis is not confirmed.** The best value over all rotations is 0.161. It is
not a question of rotation — the dimension is absent from the plane, not merely turned
away. Both rotated axes are made of pleasantness: one is anchored in negative emotions,
one in positive.

**What this dataset can contribute here, and what it cannot.**

*The valence result is weaker than it looks.* An emotion classifier recovering valence
from text whose valence was fixed before the text existed is a shorter inference than it
appears. The 0.80 shows that the machinery works — the join, the PCA, the rotation — not
that it says anything about real feedback.

*The arousal result, by contrast, does not stand alone.* Activation was never a
parameter of the generator, so its absence could be the generator's doing. The same
analysis on real data reaches the same conclusion, however, and there the generator
excuse falls away.

---

## The quality measures at a glance

**Spearman correlation (ρ).** Compares orderings, not values. +1 means both measurements
sort the comments identically. 0 means no relationship. Used here because what matters
is the ranking and not whether one scale sits linearly against the other.

**R² (coefficient of determination).** The share of variation explained, 0 to 1. It
always needs a comparison: on its own 0.19 cannot be interpreted, against a baseline of
−0.002 and a fold spread of 0.025 it can.

**Cross-validation and the fold spread.** The data is split into blocks, the model
trained on part of it several times over and tested on the rest. The variation between
runs is the yardstick for whether an improvement is real.

**95 % confidence interval.** For the themes: the range in which the mean of that many
comments would move. A theme with 11 comments has a wide interval — without that range,
every ranking looks meaningful.

**Coverage.** How many comments get a value at all. For the arousal lexicon 100 %, for
the BAWL-R referee 77 %. A high agreement measured on few comments is worth little.

**Independence from the secondary criterion.** For arousal the most important figure of
all: a method that correlates with valence is measuring the wrong axis.

---

## What this dataset shows and what it does not

**It shows.** That the chain runs in full — reading in, two measurement axes, validation
against questionnaire values, a topic model, the export, the dashboard. That the
**ordering** of the methods is stable: Gemma before BERT for valence, the IMS lexicon
without stopwords for arousal, valence alone for engagement.

**It does not show.** Effect sizes. They come out more flattering throughout here,
because generated text carries a clearer signal. And the distribution of emotions: the
four equally sized quadrants are a property of the generator, not of working life.

**The most important difference.** Here half of the respondents write; on real data a far
smaller share. The selection effect — those who speak up are on average less satisfied
than those who stay silent — is not measurable on this dataset, because it was not built
in. It is well known from other contexts, such as product reviews or social networks:
the dissatisfied write, the satisfied stay silent.
