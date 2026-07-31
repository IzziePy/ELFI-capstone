# EDA Findings

Interpreted results of [eda.ipynb](eda.ipynb) — ELFI, first EDA on dummy data.
The notebook contains the analysis; this file contains the conclusions.

> ⚠️ **All numbers describe synthetic dummy data.** They validate the pipeline,
> not an organisation. See [Limitations](#limitations).

*English below, deutsche Fassung ab [Ergebnisse der explorativen Datenanalyse](#ergebnisse-der-explorativen-datenanalyse).*

---

## 1. Data set and completeness

| Metric | Value |
| :--- | :--- |
| Responses | 500 |
| Columns | 66 raw → 63 after dropping `E-Mail`, `Name`, `Language` |
| Likert items | 54, all on the same 5-point agreement scale |
| Free-text question | 1 (`freitext_kommentar`) |
| Business units (`bereich`) | 5, sizes 82–114 |
| Survey waves | 1 iteration, 4 runs (`run_id`) |

**Unit sizes:** IN-AL 114 · TR-DE 108 · ZU-BE 106 · KE-EP 90 · FO-GA 82 — balanced
enough for group comparisons without weighting.

**Completeness is unusually clean:** not a single Likert question was skipped
(0 true missings). Every `NaN` in the item data comes from the escape option
*"Kann ich nicht beurteilen"* — 950 answers, 3.5 % of all 27,000 item responses.
The free-text question was left blank by 18 respondents (3.6 %).

**Escape answers concentrate on items people cannot observe:**

| Item | Escape answers | Share |
| :--- | ---: | ---: |
| `prozessverbesserung_kontinuierlich` | 28 | 5.6 % |
| `gestaltungsspielraum` | 26 | 5.2 % |
| `rahmenbedingungen_change` | 25 | 5.0 % |
| `veraenderung_aktiv` | 25 | 5.0 % |
| `kundenfokus` | 24 | 4.8 % |

The pattern is consistent: these ask about organisation-wide processes and change,
not about the respondent's own experience. Per person the median is 2 escape
answers, the maximum 8 — nobody used it to opt out wholesale.

**Consequence for the pipeline:** the escape option must never be recoded as a
neutral "3". It is a *refusal to judge*, not a middle position. The notebook
records it in `knb_mask` **before** recoding and asserts that every resulting
`NaN` traces back to a known cause.

---

## 2. Item level: strengths and problem areas

### Lowest-rated items

| Item | Mean | Reading |
| :--- | ---: | :--- |
| `ueberlastung_r` | 2.84 | Reverse coded — a low value means **high** perceived workload |
| `anforderung_faehigkeit_match` | 2.88 | Requirements do not match skills |
| `strategieklarheit` | 3.10 | Strategic direction unclear |
| `klare_ziele` | 3.11 | Goals not transparent |
| `arbeitsinformationen` | 3.11 | Information needed for the work is missing |
| `zielbeitrag_strategie` | 3.12 | Own contribution to the strategy not visible |

Four of the six form one coherent cluster: **clarity of direction** — strategy,
goals, information flow, contribution. The other two are about **person-role fit**
and **workload**.

### Highest-rated items

| Item | Mean |
| :--- | ---: |
| `rahmenbedingungen_change` | 4.21 |
| `work_life_balance` | 4.20 |
| `arbeitsfreude` | 3.72 |
| `lob_anerkennung` | 3.56 |
| `zusammenarbeit_team` | 3.55 |
| `teamstimmung` | 3.54 |

### A contradiction worth naming

`work_life_balance` is the second-highest item (4.20) while `ueberlastung_r` is
the lowest (2.84). Respondents say their phases of strain and recovery are in
balance **and** that they feel overloaded. In real data this would be the single
most interesting finding — either the two items measure different time horizons
(current week vs. general arrangement), or people normalise chronic overload as
"balanced". In this synthetic set it is more likely an artefact of independent
item generation, but the check belongs in every future wave.

### Polarisation

| Highest spread (std) | | Lowest spread (std) | |
| :--- | ---: | :--- | ---: |
| `ueberlastung_r` | 1.17 | `arbeitgeber_empfehlung` | 0.75 |
| `anforderung_faehigkeit_match` | 1.14 | `beitrag_wettbewerbsfaehigkeit` | 0.79 |
| `veraenderung_aktiv` | 1.09 | `arbeitsfreude` | 0.81 |
| `erwartungsklarheit` | 1.09 | | |
| `klare_ziele` | 1.08 | | |

The polarising items are the useful ones for segmentation and clustering: there
are clear winners and losers. The consensus items separate nobody and will carry
little signal in a model.

### Reverse coding

`ueberlastung` ("Ich fühle mich aktuell durch die Arbeit überlastet") is the only
negatively worded item in the questionnaire. It was verified two ways:

- **By wording** — all 54 item texts were read.
- **Empirically** — item-total correlation (each item correlated against the mean
  of the remaining 53). `ueberlastung` was the only negative value at **−0.24**;
  the other 53 items range from +0.18 to +0.75.

It is recoded as `6 − x` and renamed to **`ueberlastung_r`**. The suffix is not
cosmetic: after recoding, a *high* value means *low* workload, and a chart
labelled "ueberlastung" would invite the exact opposite reading.

Three items look negative but must **not** be reversed — they are negatively
*worded* but positively *poled*: `psychologische_sicherheit` ("keine Angst",
r = +0.60), `fehlerkultur_keine_vorwuerfe` ("keine Vorwürfe", r = +0.58),
`meinungsfreiheit` ("ohne Risiken", r = +0.61).

### One ambiguous item

`strategie_einfluss` — *"Die Strategie von [Organization] beeinflusst meine
Arbeitsentscheidungen."* High agreement is not unambiguously good: the item
measures how **salient** the strategy is, not how satisfied someone is with it.
Statistically it behaves normally (r = +0.61), so it stays in `strategie_score` —
but any interpretation of that score should mention it, because a rise could mean
"the strategy is clearer" or merely "the strategy is more present".

---

## 3. Data quality

All four survey-specific quality checks come back clean:

| Check | Result |
| :--- | :--- |
| Exact duplicate rows | 0 |
| Identical answer patterns across all 54 items | 0 |
| Completion time | 3–24 min, median 13 min |
| Speeders (< 3 min) | 0 |
| Straightlining (within-person std < 0.25) | 0 |

This is exactly what generated data should look like. The value of these checks
is not the result but the fact that they now run automatically — on real survey
data, speeders and straightliners are the normal case, and both would look like
valid responses in every statistic in this notebook.

`start` and `ende` were previously unused; they now produce `dauer_minuten`.

---

## 4. Topic level and scale reliability

The 54 items are aggregated into 8 topic scores (mean per respondent, then mean
across respondents). All 54 items are assigned to exactly one group — the
notebook asserts this rather than assuming it.

| Rank | Topic | Mean | Std | Min | Max | Cronbach's α | n complete |
| ---: | :--- | ---: | ---: | ---: | ---: | ---: | ---: |
| 1 | Engagement | 3.53 | 0.65 | 1.40 | 5.00 | 0.70 | 420 |
| 2 | Teamarbeit | 3.51 | 0.92 | 1.00 | 5.00 | 0.95 | 422 |
| 3 | Kultur | 3.44 | 0.74 | 1.33 | 5.00 | 0.84 | 391 |
| 4 | Führung | 3.41 | 0.65 | 1.33 | 4.83 | 0.73 | 387 |
| 5 | Entwicklung | 3.32 | 0.71 | 1.33 | 4.86 | 0.84 | 387 |
| 6 | Wertschöpfung | 3.29 | 0.67 | 1.22 | 4.89 | 0.83 | 349 |
| 7 | Strategie | 3.23 | 0.72 | 1.17 | 4.83 | 0.80 | 403 |
| 8 | Arbeitsgestaltung | 3.15 | 0.70 | 1.25 | 4.86 | 0.83 | 378 |

**The whole range is 0.38 points wide** (3.15 to 3.53) on a 5-point scale. There
is no topic that is genuinely good and none that is genuinely broken — everything
sits between "Teils/Teils" and "Stimme eher zu". Ranking these eight topics is
therefore a much weaker statement than the ordering suggests.

**Reliability:** all eight scales are acceptable (α ≥ 0.70), six are good
(α ≥ 0.80). Two need comment:

- **Teamarbeit α = 0.95** is *too* high for 6 items. Alpha that high usually means
  the items are near-paraphrases rather than facets of one construct — plausible
  here, since the generator produced them from similar templates. In real data
  this would be a reason to shorten the scale.
- **Engagement α = 0.70** is the weakest, which fits its content: it bundles
  recognition, willingness to recommend, enjoyment, work-life balance and
  workload — related but not interchangeable.

**Watch the complete-case counts.** Alpha is computed listwise, so the escape
answers cost between 78 and 151 respondents per scale (Wertschöpfung with 9 items
retains only 349 of 500). The scores themselves use pairwise means and are not
affected, but any future model that needs complete rows will lose a third of the
sample unless the escape answers are imputed deliberately.

---

## 5. Differences between business units

| Topic | Spread (best − worst unit) |
| :--- | ---: |
| Teamarbeit | 0.20 |
| Strategie | 0.19 |
| Arbeitsgestaltung | 0.18 |
| Engagement | 0.18 |
| Kultur | 0.18 |
| Entwicklung | 0.14 |
| Führung | 0.08 |
| Wertschöpfung | 0.07 |

**There are effectively no unit-level differences.** The largest gap between the
strongest and the weakest of five units is 0.20 points — smaller than the
within-unit standard deviation of every single topic (0.65–0.92). No unit stands
out as a problem case, and no unit can serve as a best-practice example.

The one stable pattern: `arbeitsgestaltung_score` is the weakest topic in **all
five** units (3.07–3.25). Whatever drives it is organisation-wide, not local — so
it cannot be addressed by targeting a single unit.

For synthetic data this is the expected outcome, since the generator does not
model unit-level effects. On real data this analysis is the first thing
stakeholders will ask for, and the code is now in place to produce it.

---

## 6. Correlations between topics

### Strongest relationships

| Pair | r | Strength (Cohen) |
| :--- | ---: | :--- |
| Teamarbeit ↔ Kultur | 0.85 | Very strong |
| Führung ↔ Wertschöpfung | 0.84 | Very strong |
| Führung ↔ Strategie | 0.78 | Strong |
| Entwicklung ↔ Wertschöpfung | 0.74 | Strong |
| Kultur ↔ Führung | 0.69 | Strong |
| Kultur ↔ Wertschöpfung | 0.68 | Strong |

### Weakest relationships

| Pair | r |
| :--- | ---: |
| Teamarbeit ↔ Arbeitsgestaltung | 0.21 |
| Teamarbeit ↔ Entwicklung | 0.28 |
| Kultur ↔ Arbeitsgestaltung | 0.29 |

**Two readings, and the second matters more.** The obvious reading is that the
topics form clusters — team/culture on one side, leadership/strategy/value
creation on the other. The more important reading is that almost the entire
matrix is positive: people who rate one area well rate everything well. That is a
**general factor** (halo effect), and it means the eight topic scores are not
eight independent measurements. Differences *between* topics within one person
carry more information than the absolute levels.

**Arbeitsgestaltung is the genuine outlier**: it correlates only 0.21–0.29 with
Teamarbeit and Kultur while being the lowest-scoring topic. It measures something
the softer topics do not — autonomy, goal clarity, decision speed, influence over
workload. Team-building activities would not move it; structural changes would.

---

## 7. Item-level structure

The 54 × 54 item correlation matrix is rendered as a hierarchically clustered
heatmap (`sns.clustermap`), so blocks of related items surface on their own and
can be compared against the manual thematic grouping.

- The clustering broadly reproduces the eight groups, which supports the manual
  assignment.
- **`ueberlastung_r` remains the most weakly integrated item** even after reverse
  coding (item-total r = +0.24). Workload follows its own logic — the argument for
  treating it as a separate indicator rather than folding it into Engagement.
- **`anforderung_faehigkeit_match` is the second-weakest** (r = +0.18) while also
  being one of the two lowest-scoring items. A low mean combined with low
  integration means it is not simply "part of general dissatisfaction" — it points
  at something specific.

---

## 8. Free-text answers

### Coverage — and why it matters

| | |
| :--- | :--- |
| Responses with a comment | 482 of 500 (**96.4 %**) |
| Comment rate by unit | 95.6 % – 97.2 % |

A 96 % comment rate is extraordinarily high for an employee survey (10–30 % is
typical) and is a property of the generator, not of human behaviour. The rate is
uniform across all five units, so there is no unit-level self-selection.

**The bias check that matters — and its limit:** commenters score lower than
non-commenters on almost every topic (Arbeitsgestaltung −0.36, Teamarbeit −0.30,
Wertschöpfung −0.29). That would be a classic negativity bias in commenting.
**But the non-commenter group is only 18 people.** With that n, none of these
differences is statistically meaningful, and they should not be reported as a
finding. The analysis is in place for real data, where the silent group will be
the majority.

### Length

| Metric | Median | Mean | Min | Max |
| :--- | ---: | ---: | ---: | ---: |
| Characters | 161 | 183 | 31 | 608 |
| Words | 25 | 29 | 6 | 94 |
| Mean word length | 5.3 | 5.3 | 3.8 | 7.1 |
| Mean sentence length (chars) | 61 | 61 | 22 | 126 |

All metrics are computed on the 482 actual comments. The 18 empty ones are kept
as rows (they are needed for the coverage analysis) but excluded from every text
statistic — otherwise they would produce a spike at zero and pull every mean down.

**No boilerplate.** The shortest comment has 31 characters and 6 words; there are
zero one-word non-answers ("nein", "-", "keine") and zero exact duplicates. Real
survey data will not look like this — a filter for non-answers will be needed.

### Content

Top words: *gut, mal, kommt, mehr, zeit, finde, wirklich, team, niemand, neue,
arbeit* — a mix of evaluative words and topic words, as expected.

**The trigram analysis surfaces the most important finding for the modelling
phase.** The top trigrams each occur 15–19 times in only 482 comments:
*"fragen treffen situation"*, *"unangenehme themen benennt"*, *"zusatzleistungen
wirklich gut"*, *"buddy richtig zeit"*. These are **reused generator templates**,
not organic phrasing. Consequences:

1. A topic model on this data will recover the generator's templates and present
   them as themes. Any topic-modelling result on dummy data is meaningless.
2. Sentiment models will look artificially consistent, because the same phrasing
   recurs with the same polarity.
3. Neither can be used to estimate the accuracy that will be reached on real data.

**Two preprocessing requirements were found by reading the texts:**

- Two comments are identical except for capitalisation ("Nach meiner Rückkehr…" /
  "nach meiner rückkehr…") → case normalisation is required before deduplication.
- The corpus contains typos ("leetzte") → the sentiment model must tolerate
  misspellings, which rules out approaches that depend on exact lexicon matches.

### Text metrics vs. survey scores

| | word_count | length |
| :--- | ---: | ---: |
| Engagement | −0.13 | −0.14 |
| Strategie | −0.09 | −0.09 |
| Führung | −0.08 | −0.08 |
| **Overall score** | **−0.08** | **−0.09** |
| Arbeitsgestaltung | −0.00 | −0.00 |

All correlations are negative — longer comments come from less satisfied
respondents — but all are far below any relevance threshold (|r| < 0.15).
**Comment length carries no usable information about satisfaction in this data
set.** The direction is worth re-testing on real data, where the "long comment =
complaint" pattern is well documented; here it is noise.

---

## Limitations

1. **Synthetic data.** Generated dummy responses, not a real survey. Every number
   above describes the generator. What is being validated here is the pipeline.
2. **Free texts are generated** from reused templates (see section 8). The
   sentiment distribution does not represent how employees write, and accuracy
   measured on this data will not transfer to production.
3. **Self-selection.** Sentiment results describe the people who chose to comment,
   never the whole workforce — even though that distinction is invisible here at a
   96 % comment rate.
4. **`abteilungs_id` cannot be used for grouping** in this data set: 500 distinct
   random values for 500 rows. The department mapping can only be tested on real
   data.
5. **One wave only.** `iteration_id` is constant, so no development over time can
   be shown; `run_id` distinguishes four runs within the same wave.
6. **Escape answers are not imputed.** They are excluded pairwise. Any model
   requiring complete rows will lose up to 30 % of the sample.

---
---

# Ergebnisse der explorativen Datenanalyse

Interpretierte Ergebnisse aus [eda.ipynb](eda.ipynb) — ELFI, erste EDA auf
Dummy-Daten. Das Notebook enthält die Analyse, diese Datei die Schlussfolgerungen.

> ⚠️ **Alle Zahlen beschreiben synthetische Dummy-Daten.** Sie validieren die
> Pipeline, nicht eine Organisation. Siehe [Limitationen](#limitationen).

---

## 1. Datensatz und Vollständigkeit

| Kennzahl | Wert |
| :--- | :--- |
| Antworten | 500 |
| Spalten | 66 roh → 63 nach Entfernen von `E-Mail`, `Name`, `Language` |
| Likert-Items | 54, alle auf derselben 5-stufigen Zustimmungsskala |
| Freitextfrage | 1 (`freitext_kommentar`) |
| Bereiche | 5, Größen 82–114 |
| Befragungswellen | 1 Iteration, 4 Runs (`run_id`) |

**Bereichsgrößen:** IN-AL 114 · TR-DE 108 · ZU-BE 106 · KE-EP 90 · FO-GA 82 —
ausgewogen genug für Gruppenvergleiche ohne Gewichtung.

**Die Vollständigkeit ist ungewöhnlich sauber:** Keine einzige Likert-Frage wurde
übersprungen (0 echte Missings). Jedes `NaN` in den Item-Daten stammt aus der
Ausweichoption *„Kann ich nicht beurteilen"* — 950 Antworten, 3,5 % aller 27.000
Item-Antworten. Die Freitextfrage ließen 18 Personen leer (3,6 %).

**Ausweichantworten häufen sich bei Items, die man nicht beobachten kann:**

| Item | Ausweichantworten | Anteil |
| :--- | ---: | ---: |
| `prozessverbesserung_kontinuierlich` | 28 | 5,6 % |
| `gestaltungsspielraum` | 26 | 5,2 % |
| `rahmenbedingungen_change` | 25 | 5,0 % |
| `veraenderung_aktiv` | 25 | 5,0 % |
| `kundenfokus` | 24 | 4,8 % |

Das Muster ist stimmig: Diese Items fragen nach organisationsweiten Prozessen und
Veränderungen, nicht nach der eigenen Erfahrung. Pro Person liegt der Median bei
2 Ausweichantworten, das Maximum bei 8 — niemand hat sich pauschal entzogen.

**Konsequenz für die Pipeline:** Die Ausweichoption darf niemals als neutrale „3"
kodiert werden. Sie ist eine *verweigerte Beurteilung*, keine Mittelposition. Das
Notebook hält sie in `knb_mask` **vor** der Umkodierung fest und prüft per
`assert`, dass jedes entstehende `NaN` auf eine bekannte Ursache zurückgeht.

---

## 2. Item-Ebene: Stärken und Problemfelder

### Niedrigste Werte

| Item | Mittelwert | Lesart |
| :--- | ---: | :--- |
| `ueberlastung_r` | 2,84 | Umgepolt — ein niedriger Wert bedeutet **hohe** Belastung |
| `anforderung_faehigkeit_match` | 2,88 | Anforderungen passen nicht zu den Fähigkeiten |
| `strategieklarheit` | 3,10 | Strategische Richtung unklar |
| `klare_ziele` | 3,11 | Ziele nicht transparent |
| `arbeitsinformationen` | 3,11 | Für die Arbeit nötige Informationen fehlen |
| `zielbeitrag_strategie` | 3,12 | Eigener Beitrag zur Strategie nicht sichtbar |

Vier der sechs bilden ein zusammenhängendes Cluster: **Klarheit über die
Richtung** — Strategie, Ziele, Informationsfluss, Beitrag. Die anderen beiden
betreffen **Person-Rollen-Fit** und **Belastung**.

### Höchste Werte

| Item | Mittelwert |
| :--- | ---: |
| `rahmenbedingungen_change` | 4,21 |
| `work_life_balance` | 4,20 |
| `arbeitsfreude` | 3,72 |
| `lob_anerkennung` | 3,56 |
| `zusammenarbeit_team` | 3,55 |
| `teamstimmung` | 3,54 |

### Ein Widerspruch, der benannt gehört

`work_life_balance` ist das zweithöchste Item (4,20), `ueberlastung_r` das
niedrigste (2,84). Die Befragten sagen, Belastungs- und Erholungsphasen seien in
Balance — **und** sie fühlten sich überlastet. In echten Daten wäre das der
interessanteste Befund überhaupt: Entweder messen die beiden Items
unterschiedliche Zeithorizonte (aktuelle Woche vs. generelle Regelung), oder
chronische Überlastung wird als „ausgeglichen" normalisiert. Hier ist es
vermutlich ein Artefakt unabhängiger Item-Generierung — die Prüfung gehört
trotzdem in jede künftige Welle.

### Polarisierung

| Höchste Streuung (Std) | | Niedrigste Streuung (Std) | |
| :--- | ---: | :--- | ---: |
| `ueberlastung_r` | 1,17 | `arbeitgeber_empfehlung` | 0,75 |
| `anforderung_faehigkeit_match` | 1,14 | `beitrag_wettbewerbsfaehigkeit` | 0,79 |
| `veraenderung_aktiv` | 1,09 | `arbeitsfreude` | 0,81 |
| `erwartungsklarheit` | 1,09 | | |
| `klare_ziele` | 1,08 | | |

Die polarisierenden Items sind die nützlichen für Segmentierung und Clustering:
Hier gibt es klare Gewinner und Verlierer. Die Konsens-Items trennen niemanden und
werden in einem Modell wenig Signal tragen.

### Reverse-Coding

`ueberlastung` („Ich fühle mich aktuell durch die Arbeit überlastet") ist das
einzige negativ formulierte Item im Fragebogen. Das wurde zweifach geprüft:

- **Über die Formulierung** — alle 54 Item-Texte wurden gelesen.
- **Empirisch** — Item-Total-Korrelation (jedes Item gegen den Mittelwert der
  übrigen 53). `ueberlastung` war der einzige negative Wert mit **−0,24**; die
  anderen 53 Items liegen zwischen +0,18 und +0,75.

Das Item wird mit `6 − x` umgepolt und in **`ueberlastung_r`** umbenannt. Das
Suffix ist nicht kosmetisch: Nach der Umpolung bedeutet ein *hoher* Wert *geringe*
Belastung — ein mit „ueberlastung" beschrifteter Balken lädt zur genau
gegenteiligen Lesart ein.

Drei Items sehen negativ aus, dürfen aber **nicht** umgepolt werden — sie sind
negativ *formuliert*, aber positiv *gepolt*: `psychologische_sicherheit` („keine
Angst", r = +0,60), `fehlerkultur_keine_vorwuerfe` („keine Vorwürfe", r = +0,58),
`meinungsfreiheit` („ohne Risiken", r = +0,61).

### Ein mehrdeutiges Item

`strategie_einfluss` — *„Die Strategie von [Organization] beeinflusst meine
Arbeitsentscheidungen."* Hohe Zustimmung ist nicht eindeutig gut: Das Item misst,
wie **präsent** die Strategie ist, nicht wie zufrieden man mit ihr ist.
Statistisch verhält es sich unauffällig (r = +0,61), es bleibt deshalb im
`strategie_score` — jede Interpretation dieses Scores sollte es aber erwähnen,
denn ein Anstieg kann „die Strategie ist klarer" oder nur „die Strategie ist
präsenter" bedeuten.

---

## 3. Datenqualität

Alle vier survey-spezifischen Qualitätsprüfungen sind unauffällig:

| Prüfung | Ergebnis |
| :--- | :--- |
| Exakte Zeilen-Duplikate | 0 |
| Identische Antwortmuster über alle 54 Items | 0 |
| Bearbeitungsdauer | 3–24 Min., Median 13 Min. |
| Speeder (< 3 Min.) | 0 |
| Straightlining (personenintern Std < 0,25) | 0 |

Genau so sollten generierte Daten aussehen. Der Wert dieser Prüfungen liegt nicht
im Ergebnis, sondern darin, dass sie jetzt automatisch laufen: In echten
Befragungsdaten sind Speeder und Straightliner der Normalfall — und beide sähen in
jeder Statistik dieses Notebooks wie gültige Antworten aus.

`start` und `ende` waren bisher ungenutzt und ergeben nun `dauer_minuten`.

---

## 4. Themenebene und Skalen-Reliabilität

Die 54 Items werden zu 8 Themen-Scores verdichtet (Mittelwert pro Person, dann
über alle Personen). Alle 54 Items sind genau einer Gruppe zugeordnet — das
Notebook prüft das per `assert`, statt es anzunehmen.

| Rang | Thema | Mittelwert | Std | Min | Max | Cronbachs α | n vollständig |
| ---: | :--- | ---: | ---: | ---: | ---: | ---: | ---: |
| 1 | Engagement | 3,53 | 0,65 | 1,40 | 5,00 | 0,70 | 420 |
| 2 | Teamarbeit | 3,51 | 0,92 | 1,00 | 5,00 | 0,95 | 422 |
| 3 | Kultur | 3,44 | 0,74 | 1,33 | 5,00 | 0,84 | 391 |
| 4 | Führung | 3,41 | 0,65 | 1,33 | 4,83 | 0,73 | 387 |
| 5 | Entwicklung | 3,32 | 0,71 | 1,33 | 4,86 | 0,84 | 387 |
| 6 | Wertschöpfung | 3,29 | 0,67 | 1,22 | 4,89 | 0,83 | 349 |
| 7 | Strategie | 3,23 | 0,72 | 1,17 | 4,83 | 0,80 | 403 |
| 8 | Arbeitsgestaltung | 3,15 | 0,70 | 1,25 | 4,86 | 0,83 | 378 |

**Die gesamte Spannweite beträgt 0,38 Punkte** (3,15 bis 3,53) auf einer
5-stufigen Skala. Es gibt kein wirklich gutes und kein wirklich kaputtes Thema —
alles liegt zwischen „Teils/Teils" und „Stimme eher zu". Ein Ranking dieser acht
Themen ist damit eine deutlich schwächere Aussage, als die Reihenfolge suggeriert.

**Reliabilität:** Alle acht Skalen sind akzeptabel (α ≥ 0,70), sechs gut
(α ≥ 0,80). Zwei brauchen einen Kommentar:

- **Teamarbeit α = 0,95** ist für 6 Items *zu* hoch. So hohe Werte bedeuten meist,
  dass die Items eher Paraphrasen als Facetten eines Konstrukts sind — hier
  plausibel, weil der Generator sie aus ähnlichen Vorlagen erzeugt hat. In echten
  Daten wäre das ein Grund, die Skala zu kürzen.
- **Engagement α = 0,70** ist der schwächste Wert und passt zum Inhalt: Die Skala
  bündelt Anerkennung, Weiterempfehlung, Arbeitsfreude, Work-Life-Balance und
  Belastung — verwandt, aber nicht austauschbar.

**Auf die Fallzahlen achten.** Alpha wird listenweise berechnet, die
Ausweichantworten kosten deshalb je Skala 78 bis 151 Personen (Wertschöpfung mit
9 Items behält nur 349 von 500). Die Scores selbst nutzen paarweise Mittelwerte
und sind nicht betroffen — jedes künftige Modell, das vollständige Zeilen
braucht, verliert aber ein Drittel der Stichprobe, wenn die Ausweichantworten
nicht bewusst imputiert werden.

---

## 5. Unterschiede zwischen den Bereichen

| Thema | Spannweite (bester − schlechtester Bereich) |
| :--- | ---: |
| Teamarbeit | 0,20 |
| Strategie | 0,19 |
| Arbeitsgestaltung | 0,18 |
| Engagement | 0,18 |
| Kultur | 0,18 |
| Entwicklung | 0,14 |
| Führung | 0,08 |
| Wertschöpfung | 0,07 |

**Es gibt praktisch keine Bereichsunterschiede.** Der größte Abstand zwischen dem
stärksten und dem schwächsten von fünf Bereichen beträgt 0,20 Punkte — kleiner
als die Standardabweichung *innerhalb* jedes einzelnen Themas (0,65–0,92). Kein
Bereich sticht als Problemfall hervor, und keiner taugt als Best-Practice-Beispiel.

Das einzige stabile Muster: `arbeitsgestaltung_score` ist in **allen fünf**
Bereichen das schwächste Thema (3,07–3,25). Die Ursache ist organisationsweit,
nicht lokal — mit Maßnahmen in einem einzelnen Bereich ist ihr nicht beizukommen.

Für synthetische Daten ist das das erwartete Ergebnis, da der Generator keine
Bereichseffekte modelliert. Auf echten Daten ist diese Auswertung das Erste,
wonach Stakeholder fragen — der Code dafür steht jetzt.

---

## 6. Korrelationen zwischen den Themen

### Stärkste Zusammenhänge

| Paar | r | Stärke (Cohen) |
| :--- | ---: | :--- |
| Teamarbeit ↔ Kultur | 0,85 | Sehr stark |
| Führung ↔ Wertschöpfung | 0,84 | Sehr stark |
| Führung ↔ Strategie | 0,78 | Stark |
| Entwicklung ↔ Wertschöpfung | 0,74 | Stark |
| Kultur ↔ Führung | 0,69 | Stark |
| Kultur ↔ Wertschöpfung | 0,68 | Stark |

### Schwächste Zusammenhänge

| Paar | r |
| :--- | ---: |
| Teamarbeit ↔ Arbeitsgestaltung | 0,21 |
| Teamarbeit ↔ Entwicklung | 0,28 |
| Kultur ↔ Arbeitsgestaltung | 0,29 |

**Zwei Lesarten, und die zweite ist die wichtigere.** Die naheliegende Lesart ist,
dass die Themen Cluster bilden — Team/Kultur auf der einen,
Führung/Strategie/Wertschöpfung auf der anderen Seite. Die wichtigere Lesart ist,
dass praktisch die gesamte Matrix positiv ist: Wer einen Bereich gut bewertet,
bewertet alles gut. Das ist ein **Generalfaktor** (Halo-Effekt) und bedeutet, dass
die acht Themen-Scores keine acht unabhängigen Messungen sind. Unterschiede
*zwischen* Themen innerhalb einer Person tragen mehr Information als die absoluten
Niveaus.

**Arbeitsgestaltung ist der echte Ausreißer**: Sie korreliert nur mit 0,21–0,29
mit Teamarbeit und Kultur und ist gleichzeitig das schwächste Thema. Sie misst
etwas, das die weicheren Themen nicht erfassen — Autonomie, Zielklarheit,
Entscheidungsgeschwindigkeit, Einfluss auf die Arbeitsmenge. Teamentwicklung
würde hier nichts bewegen, strukturelle Änderungen schon.

---

## 7. Struktur auf Item-Ebene

Die 54 × 54-Item-Korrelationsmatrix wird als hierarchisch geclusterte Heatmap
(`sns.clustermap`) dargestellt. So treten Blöcke zusammengehöriger Items von
selbst hervor und lassen sich mit der manuellen Themenzuordnung vergleichen.

- Das Clustering reproduziert die acht Gruppen im Wesentlichen — das stützt die
  manuelle Zuordnung.
- **`ueberlastung_r` bleibt auch nach dem Reverse-Coding das am schwächsten
  eingebundene Item** (Item-Total r = +0,24). Belastung folgt einer eigenen Logik
  — das Argument dafür, sie als eigenständigen Indikator zu führen statt sie in
  Engagement aufgehen zu lassen.
- **`anforderung_faehigkeit_match` ist das zweitschwächste** (r = +0,18) und
  zugleich eines der beiden niedrigsten Items. Niedriger Mittelwert bei
  gleichzeitig geringer Einbindung heißt: Es ist nicht einfach Teil einer
  allgemeinen Unzufriedenheit, sondern zeigt auf etwas Spezifisches.

---

## 8. Freitextantworten

### Abdeckung — und warum sie zählt

| | |
| :--- | :--- |
| Antworten mit Kommentar | 482 von 500 (**96,4 %**) |
| Kommentarquote je Bereich | 95,6 % – 97,2 % |

Eine Kommentarquote von 96 % ist für eine Mitarbeitendenbefragung außergewöhnlich
hoch (üblich sind 10–30 %) und eine Eigenschaft des Generators, nicht
menschlichen Verhaltens. Die Quote ist über alle fünf Bereiche gleich — es gibt
also keine bereichsspezifische Selbstselektion.

**Die entscheidende Bias-Prüfung — und ihre Grenze:** Kommentierende bewerten
nahezu jedes Thema schlechter als Nicht-Kommentierende (Arbeitsgestaltung −0,36,
Teamarbeit −0,30, Wertschöpfung −0,29). Das wäre der klassische Negativity-Bias
beim Kommentieren. **Die Gruppe der Nicht-Kommentierenden umfasst aber nur 18
Personen.** Bei diesem n ist keiner dieser Unterschiede statistisch belastbar; sie
dürfen nicht als Befund berichtet werden. Die Auswertung steht für echte Daten
bereit, wo die schweigende Gruppe die Mehrheit sein wird.

### Länge

| Kennzahl | Median | Mittelwert | Min | Max |
| :--- | ---: | ---: | ---: | ---: |
| Zeichen | 161 | 183 | 31 | 608 |
| Wörter | 25 | 29 | 6 | 94 |
| Mittlere Wortlänge | 5,3 | 5,3 | 3,8 | 7,1 |
| Mittlere Satzlänge (Zeichen) | 61 | 61 | 22 | 126 |

Alle Kennzahlen werden auf den 482 tatsächlichen Kommentaren berechnet. Die 18
leeren bleiben als Zeilen erhalten (sie werden für die Abdeckungsanalyse
gebraucht), sind aber aus jeder Textstatistik ausgeschlossen — sonst erzeugten sie
eine Spitze bei null und zögen jeden Mittelwert nach unten.

**Keine Textbausteine.** Der kürzeste Kommentar hat 31 Zeichen und 6 Wörter; es
gibt null Ein-Wort-Nichtantworten („nein", „-", „keine") und null exakte
Duplikate. Echte Befragungsdaten sehen anders aus — ein Filter für Nichtantworten
wird nötig sein.

### Inhalt

Häufigste Wörter: *gut, mal, kommt, mehr, zeit, finde, wirklich, team, niemand,
neue, arbeit* — eine erwartbare Mischung aus bewertenden und thematischen Wörtern.

**Die Trigramm-Analyse liefert den wichtigsten Befund für die Modellierungsphase.**
Die häufigsten Trigramme kommen in nur 482 Kommentaren jeweils 15–19 Mal vor:
*„fragen treffen situation"*, *„unangenehme themen benennt"*, *„zusatzleistungen
wirklich gut"*, *„buddy richtig zeit"*. Das sind **wiederverwendete
Generator-Vorlagen**, keine organische Sprache. Konsequenzen:

1. Ein Topic Model auf diesen Daten findet die Vorlagen des Generators und
   präsentiert sie als Themen. Jedes Topic-Modelling-Ergebnis auf Dummy-Daten ist
   bedeutungslos.
2. Sentiment-Modelle wirken künstlich konsistent, weil dieselben Formulierungen
   mit derselben Polarität wiederkehren.
3. Aus beidem lässt sich die auf echten Daten erreichbare Genauigkeit nicht
   abschätzen.

**Zwei Preprocessing-Anforderungen wurden durch Lesen der Texte gefunden:**

- Zwei Kommentare sind bis auf die Groß-/Kleinschreibung identisch („Nach meiner
  Rückkehr…" / „nach meiner rückkehr…") → Normalisierung der Schreibung vor jeder
  Duplikaterkennung.
- Der Korpus enthält Tippfehler („leetzte") → das Sentiment-Modell muss
  Rechtschreibfehler tolerieren, was Ansätze mit exaktem Lexikon-Abgleich
  ausschließt.

### Textmetriken vs. Befragungswerte

| | word_count | length |
| :--- | ---: | ---: |
| Engagement | −0,13 | −0,14 |
| Strategie | −0,09 | −0,09 |
| Führung | −0,08 | −0,08 |
| **Gesamtscore** | **−0,08** | **−0,09** |
| Arbeitsgestaltung | −0,00 | −0,00 |

Alle Korrelationen sind negativ — längere Kommentare stammen von unzufriedeneren
Personen — aber alle liegen weit unter jeder Relevanzschwelle (|r| < 0,15).
**Die Kommentarlänge trägt in diesem Datensatz keine verwertbare Information über
die Zufriedenheit.** Die Richtung sollte auf echten Daten erneut geprüft werden,
wo das Muster „langer Kommentar = Beschwerde" gut dokumentiert ist; hier ist es
Rauschen.

---

## Limitationen

1. **Synthetische Daten.** Generierte Dummy-Antworten, keine echte Befragung. Jede
   Zahl oben beschreibt den Generator. Validiert wird hier die Pipeline.
2. **Die Freitexte sind generiert** und beruhen auf wiederverwendeten Vorlagen
   (siehe Abschnitt 8). Die Sentiment-Verteilung bildet nicht ab, wie
   Mitarbeitende schreiben; eine hier gemessene Genauigkeit überträgt sich nicht
   auf den Produktivbetrieb.
3. **Selbstselektion.** Sentiment-Ergebnisse beschreiben die Personen, die sich
   zum Kommentieren entschieden haben — nie die gesamte Belegschaft, auch wenn
   dieser Unterschied bei 96 % Kommentarquote hier unsichtbar bleibt.
4. **`abteilungs_id` taugt in diesem Datensatz nicht zum Gruppieren:** 500
   verschiedene Zufallswerte bei 500 Zeilen. Das Abteilungs-Mapping lässt sich
   erst auf echten Daten testen.
5. **Nur eine Welle.** `iteration_id` ist konstant, eine zeitliche Entwicklung
   lässt sich nicht zeigen; `run_id` unterscheidet vier Runs innerhalb derselben
   Welle.
6. **Ausweichantworten werden nicht imputiert**, sondern paarweise ausgeschlossen.
   Jedes Modell, das vollständige Zeilen benötigt, verliert bis zu 30 % der
   Stichprobe.
