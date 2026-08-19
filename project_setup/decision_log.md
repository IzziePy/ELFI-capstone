# ELFI – Daily Project Diary & Decision Log

This document serves as the chronological daily journal for the ELFI (Emotional & Latent Feedback Interpretation) project. Here, all daily activities, technical decisions, and roadblocks are recorded.

> **A note on the paths in this log.** The paths describe the state at the time of
> the entry. In the course of the clean-up in August 2026, many of the files named
> here were archived, renamed or deleted, and some are no longer part of this
> repository at all. The log is not kept up to date — it records what was decided
> when, not where something lives today. Paths followed by *(archived)* have moved
> into the archive.
>
> **Notebooks that ran on the real survey data were never part of the public
> repository at any point.** They were kept in a separate, git-ignored folder
> throughout, and no executed copy of them has ever been published.

---

## 📅 Week 0: Kick-off & Conceptual Preparation (24.07. – 26.07.2026)

### 🟢 Friday, 24.07.2026

**What I did:**
* Created the first project repository structure (locally)
* Set up the notebook workflow for reproducible analyses
* Defined a separation between data preparation, modelling, and dashboarding
* Continued work on synthetic employee survey data
* Reviewed the structure of the original survey export and identified relevant fields for modelling

**Decisions made:**
* Use synthetic data for development and prototyping activities
* Separate technical notebooks from findings documentation
* Preserve the overall survey structure while removing sensitive organisational attributes

**Roadblocks & Next Steps:**
* Need a scalable text analysis workflow
* Prepare the first NLP-ready dataset
* Define preprocessing steps for employee comments

---

## 📅 Week 1: Setup, Data & First Vertical Slice (27.07. – 02.08.2026)

**Milestone 1 target: Project Setup Done (Deadline 02.08.2026)**

### 🟢 Monday, 27.07.2026

**What I did:**
* Conceptual work on the project framing on a Miro board (8 board snapshots archived under `prep/miro_board/01_2026_0727/` (archived))
* Literature research on emotion models: Ekman (1992) *An Argument For Basic Emotions*, Erdal et al. (2026) *Multi-Label Emotion Classification Based on Plutchik's Wheel of Emotions*
* Sketched the ELFI concept: from open-text survey comments to emotion and topic insights

**Decisions made:**
* Ground the emotion component in an established theoretical model rather than an ad-hoc label set
* Keep basic emotions (Ekman) and the wheel-of-emotions perspective (Plutchik) as candidate frameworks

**Roadblocks & Next Steps:**
* Decide on a concrete emotion representation
* Generate realistic synthetic data to develop against

---

### 🟢 Tuesday, 28.07.2026

**What I did:**
* Wrote the prompt specification for synthetic data generation (`prep/prompt_datengenerierung.docx`, archived)
* Defined which survey structure the synthetic dataset has to mirror

**Decisions made:**
* Generate synthetic data via a documented, reproducible prompt instead of ad-hoc sampling
* The synthetic dataset must mirror the original survey structure 1:1 so that notebooks transfer without changes

**Roadblocks & Next Steps:**
* Produce and validate the first synthetic dataset

---

### 🟢 Wednesday, 29.07.2026
* **What I did:** [Write here...]
* **Decisions made:** [Write here...]
* **Roadblocks & Next Steps:** [Write here...]

---

### 🟢 Thursday, 30.07.2026

**What I did:**
* Initialised the GitHub repository (`525bd69 Initial commit`, 11:51)
* Set up the clean folder structure and the first project documentation (`db0a5c9 cleaned repo`, 11:57): README, `project_setup/` with decision log, goals, project description, emotional models, german sentiment models
* Added the synthetic data template notebook and the first EDA notebook
* Drafted the column mapping for the original survey export (`data/column_mapping.md`, archived)
* Worked through a reference guide on EDA for text data
* First substantial EDA iteration (`fea0dd8 Interim update EDA`, 18:02)

**Decisions made:**
* Repository structure separates `data/` (preparation), `notebooks/` (analysis), `presentation/` (delivery), `project_setup/` (documentation)
* Documentation lives in the repository, not in external files
* Pin the Python version via `.python-version` for reproducibility

**Roadblocks & Next Steps:**
* Complete the EDA on the synthetic data
* Prepare the repository for the original High5 data

---

### 🟢 Friday, 31.07.2026

**What I did:**
* Completed the first EDA on the synthetic data and documented it in `findings.md` (`d1eaa48`, 14:10; merged via PR #15)
* Finalised the project setup documentation: `goals.md` (archived) (4 milestones with KPIs), `project_description.md`, `emotional_models.md` (archived), `german_sentiment_models.md` (archived), README, ELFI visuals
* Integrated the original High5 survey exports (26 files in `data/high5/`, plus 27 out-of-scope files separated into `data/out_of_scope/`)
* Built the mapping suggestion notebook (`data/03_create_mapping_suggestions.ipynb`, archived) and generated the question→feature mapping
* Produced the first mapping metadata: `private_question_feature_mapping.csv` (archived), `mapping_check_by_file.csv` (archived), `mapping_duplicates_by_file.csv` (archived), `transform_report.csv` (archived), `unmapped_columns_transform.csv` (archived)
* Restructured the EDA notebook for the original data (`4bd33f8 prepare for original data`, 15:28)

**Decisions made:**
* Separate in-scope (core organisation) from out-of-scope surveys (AUSSENORGA, Baur, early iterations) rather than merging everything
* Question texts vary across survey runs → map them to stable feature names via an explicit, reviewable mapping table instead of hard-coded column indices
* All original data stays out of Git (`data/` gitignored); only derived, non-sensitive artefacts are shared

**Roadblocks & Next Steps:**
* Column naming is inconsistent across the 26 survey runs
* Duplicate question mappings need a decision rule
* Build the transformation into a unified long/wide dataset

---

### 🟢 Saturday, 01.08.2026 (Weekend/Buffer)
* **What I did:** [Write here, or leave empty if resting!]

---

### 🟢 Sunday, 02.08.2026

**What I did:**
* Built the file and question inventory across all survey runs (`file_inventory.csv` (archived), `questions_inventory.csv` (archived), `question_master.csv` (archived), `schema_profile.csv` (archived), `question_extraction_check.csv`, archived)
* Restructured the repository for the original data (`fa1e52d prepare for original data`, 16:49): notebooks moved into `notebooks/`, documentation reworked, README expanded

**Decisions made:**
* A single `question_master.csv` (archived) becomes the canonical reference for all question variants across survey runs
* Schema profiling before transformation, so that structural breaks between runs are visible rather than silently absorbed

**Roadblocks & Next Steps:**
* Milestone 1 essentially reached (setup, EDA, structure) — success metrics still outstanding
* Run the transformation across all files
* Start the NLP pipeline on the real data

---

## 📅 Week 2: NLP Modeling – Sentiment & Clustering (03.08. – 09.08.2026)

**Milestone 2 target: Product MVP built (Deadline 09.08.2026) · Midterm Presentation 06.08.2026**

### 🟢 Monday, 03.08.2026

**What I did:**
* Ran the full High5 transformation (`data/04_transform_high5.ipynb` (archived) → `high5_transformed.csv`, 05:36) including the duplicate/parking decisions (`mapping_decisions_by_file.csv` (archived), `mapping_parked_duplicates.csv`, archived)
* Built the High5 inventory notebook (`data/02_create_high5_inventory.ipynb`, archived) and consolidated the synthetic data notebook (`data/01_create_synthetic_survey_data.ipynb`, archived)
* Generated automated EDA profiles for both datasets (`fast_dummy_eda.html` (archived), `private/reports/fast_eda_high5.html`, archived)
* Full EDA on the real High5 data (`private/01_eda_on_high5_data.ipynb` (archived) → `high5_eda_ready.csv` (archived), 14:03) with written findings (`findings_eda_on_high5.md`, archived)
* Compared synthetic vs. real data structurally and in distribution (`compare_synthetic_high5.md`, archived)
* First German sentiment run on the synthetic data (`synthetic_sentiment_enriched.csv` (archived), 18:24) and first results write-up (`first_results.md` (archived), 18:56)
* Committed the zero-shot and BERT work (`7d259f6 ZeroShot und BERT`, 18:59)

**Decisions made:**
* Build the project around a notebook-based workflow, modular and reproducible
* Focus on German-language models due to the primary language of the comments
* Keep a strict split: `private/` for notebooks touching real data, `notebooks/` for the shareable synthetic-data versions
* Keep the synthetic dataset structurally aligned with the real one so notebooks are portable between both

**Roadblocks & Next Steps:**
* Need a robust approach for sentiment and topic extraction
* Evaluate suitable embedding models
* Define measurable success metrics (still open from Milestone 1)

---

### 🟢 Tuesday, 04.08.2026

**What I did:**
* Defined the MVP scope (`private/MVP.md`, archived) and the measurable success metrics (`presentation/00_SuccessMetrics.md`, archived) — closing the open item from Milestone 1
* Documented the synthetic-data findings (`findings_synthetic_data.md`, archived)
* **Started the first BERTopic experiments** (`archive/05_topic_modeling_bertopic.ipynb` (archived), 13:56)
* Built two German stopword lists for topic modelling (`german_stopwords_extended.txt`, `german_stopwords_full.txt`)
* Rewrote the synthetic data generator as reproducible scripts (`create_synthetic_high5_dataset.py` (archived), then `_v2.py`) and generated a 5,000-row dataset
* Extended `requirements.txt` for the NLP stack

**Decisions made:**
* Prioritise BERTopic over simpler keyword-based approaches
* Preserve as much semantic information as possible — avoid aggressive text cleaning that removes contextual meaning
* Build the analysis around original employee wording rather than handcrafted dictionaries
* Use a domain-specific extended stopword list instead of the generic German default
* Move synthetic data generation from notebook to script, so datasets are regenerable

**Roadblocks & Next Steps:**
* Need a stable BERTopic baseline
* Need an evaluation strategy for topic quality
* Test different embedding and clustering configurations

---

### 🟢 Wednesday, 05.08.2026

**What I did:**
* Renumbered and consolidated the public notebooks (01 EDA / 02 Zero-Shot / 03 German Sentiment / 04 BERTopic) and regenerated `synthetic_eda_ready.csv` (archived)
* Ran the zero-shot core-affect classification on the real data (`private/02_zero_shot_text_analysis_high5.ipynb` (archived) → `zero_shot_core_affect.csv` (archived), 14:00)
* Continued the BERTopic work (`notebooks/04_topic_modeling_bertopic.ipynb` (archived), 15:20)
* Built the midterm deck generator (`presentation/build_midterm_deck.py`, archived) and produced the first two deck versions (16:31 and 23:10)
* Committed the midterm state (`8828ded Midterm Presentation`, 16:41)

**Decisions made:**
* Generate the presentation programmatically from a script, so content changes can be re-rendered instead of hand-edited
* Evaluate topics through a combination of statistical quality and interpretability
* Plan to use coherence and outlier rates as key evaluation metrics

**Roadblocks & Next Steps:**
* Identify the optimal preprocessing strategy
* Compare alternative embeddings
* Reduce outlier rates while preserving meaningful topics

---

### 🟢 Thursday, 06.08.2026 (Midterm Presentation!)

**What I did:**
* Reworked the deck into the final design (v3 at 10:49, final "modern blue" at 16:03)
* Built the Power BI dashboard (`presentation/01_Dashboards.pbix` (archived), 16:43) for the interim results
* Held the midterm presentation

**Decisions made:**
* BERTopic will be used as the primary topic modelling approach
* Topic quality will be evaluated using coherence, interpretability and outlier rates
* Topic labels should later be generated and refined using LLM support
* Start the Power BI integration early (Milestone 4) rather than leaving it to the final week

**Roadblocks & Next Steps:**
* Consolidate the BERTopic model into a final, reusable artefact
* Move from raw topic numbers to human-readable topic labels

---

### 🟢 Friday, 07.08.2026

**What I did:**
* Trained and saved the final BERTopic model (`models/bertopic_final`, 16:04)
* Built a reduced variant with 35 topics (`models/bertopic_reduced_35` (archived), 17:18) to lower the outlier rate
* Generated LLM-based topic labels via Ollama (`private/05_topic_labelling_ollama.ipynb` (archived) → `topic_labels_ollama.csv` (archived), 17:59)
* Documented the BERTopic evaluation (`private/findings/findings_bertopic.md` (archived), 18:14)
* Ran the German sentiment model across the full High5 dataset (`high5_sentiment_enriched.csv` (archived), 18:53)

**Decisions made:**
* Reduce the topic count to 35 as the working configuration — outlier rate vs. interpretability trade-off
* Use Ollama (local LLM) for topic labelling, so no survey content leaves the machine
* Keep both the full and the reduced model as artefacts for comparison

**Roadblocks & Next Steps:**
* Evaluate the labelled topics for plausibility
* Move on to the emotion/arousal dimension
* Model artefacts are ~500 MB each — must not enter Git

---

### 🟢 Saturday, 08.08.2026 (Weekend/Buffer)
* Rest day.

---

### 🟢 Sunday, 09.08.2026

**What I did:**
* Built the arousal modelling notebook (`private/06_arousal_modelling.ipynb` (archived), 21:12)
* Integrated the German Emotions model and compared its outputs against Ollama (`gemma2:9b`)
* Built a first ensemble concept for emotion scoring
* Investigated valence and arousal representations, created emotion distribution visualisations
* Analysed model agreement and disagreement
* Refined the project narrative and the positioning of ELFI as an employee listening analytics solution

**Decisions made:**
* Represent arousal on a continuous scale from -1 to +1
* Continue evaluating Ollama as a complementary component rather than a replacement
* Explore ensemble strategies instead of relying on a single model
* Focus communication on capabilities rather than individual technical tools, emphasising:
  * Team Analytics
  * Data-driven Team Development
  * AI-Powered Insights
  * Employee Listening

**Findings:**
* Pearson correlation between German Emotions and Ollama arousal outputs: **0.325**
* Valid comparison sample size: **51 comments**
* German Emotions produced more continuous scores than Ollama
* Long comments exceeded the transformer limit of 512 tokens in some cases

**Roadblocks & Next Steps:**
* Evaluate ensemble performance on larger samples
* Improve arousal estimation quality
* Investigate model scalability on the full dataset
* **Nothing has been committed since 05.08.** — deck, dashboard, BERTopic models, Ollama labelling and arousal notebook are all uncommitted

---

## 📅 Week 3: Database & Power BI Integration (10.08. – 16.08.2026)

**Milestone 3 target: Working Data Product (Deadline 16.08.2026)**

### 🟢 Monday, 10.08.2026

**What I did:**

*Housekeeping*
* Reviewed and corrected this decision log against the Git history and file timestamps (week structure was misaligned; Week 1 was undocumented)
* Extended `.gitignore` for the ~500 MB model artefacts and Office lock files; added `scipy` and `pyarrow` to `requirements.txt`
* Corrected four text cells in `03_german_sentiment_analysis_high5.ipynb` (archived) that contradicted their own output (the distribution was described as polarised with a positive upper quartile; the 75 % quantile is −0.000012) and two stale summary cells (50 %/18 % where the run reports 54 %/16 %)

*Valence*
* Built `07_valence_validation.ipynb` (archived) — validates the valence score against the closed survey items, so no manual annotation is needed
* Compared three formulations of the valence formula; `neutral-adjusted` wins on all six criteria, bootstrap-confirmed
* Ran the anchored Gemma pilot (100 comments) and fed it into 07 as a fourth variant — it leads by +0.13 to +0.20
* Prepared two full runs: an anchored one as Part 3 of notebook 03, and a blind one in `03_b_gemma_valence_full_blind.ipynb` (archived)

*Arousal*
* `08_arousal_validation.ipynb` (archived): the existing arousal measure **fails** as a separate dimension — it matches the valence criterion four times more closely than the arousal criterion
* `06_b_arousal_diagnostics.ipynb` (archived): located the cause. A cross-validated ceiling test showed the best achievable emotion → arousal mapping reaches 0.126 where the hand-made one reached 0.032
* Replaced the hand-assigned mapping with published norms (IMS Stuttgart, 2.27 M German word forms); valence contamination fell from +0.121 to +0.010
* Tested 14 candidates for a second axis in total, all retained in one comparison table
* Fixed three defects in `06_arousal_modelling.ipynb` (archived): `temperature` sat outside `options` and was silently ignored (the run used the default 0.8), the ensemble calls had no timeout, and `/api/generate` bypassed gemma2's chat template

**Decisions made:**
* Keep the log aligned with verifiable artefacts (commits, file timestamps) rather than reconstructing from memory
* `models/` stays out of Git — model artefacts are regenerated from the notebooks, not versioned
* **Validate against the closed survey items instead of annotating by hand.** Every comment comes from a respondent who also answered the Likert questions, so the reference already exists in the data
* **Never overwrite a variant.** Every approach tried becomes an additional row in the comparison table, including the ones that failed — that table is the process documentation
* **Tag data sources and blank out shared-source comparisons** rather than footnoting them. A measure built from a word list cannot be validated against that same list
* **Rank by BAWL-R, not by the best value across criteria.** Taking the maximum rewards whichever candidate matches the most permissive yardstick
* Valence measure: `BERT: neutral-adjusted`; dashboard aggregation: `BERT: evenly scaled` (rank-normalised, because averages over a saturated scale are dominated by its edges)
* Arousal measure, provisional: `Lexicon: mean, stopwords removed`
* Parquet between notebooks, CSV only for Power BI — keeps dtypes and avoids the decimal-comma ambiguity
* Long LLM runs write an **append-only JSONL checkpoint** and resume; deterministic decoding (`temperature 0`, fixed seed)

**Findings:**
* Valence: all criterion correlations positive, and the ordering follows affect theory — `arbeitgeber_empfehlung` strongest (ρ = 0.32), `work_life_balance` weakest (ρ = 0.19). AUC 0.70–0.75 separating the least from the most satisfied respondents
* The expected acquiescence bias in the anchored Gemma design **did not occur**: only 46 % of scores rated plausible, mean absolute correction 0.395, 16 sign reversals in 100
* Arousal is **real but weak** — 0.18 against an independent human-rated reference at −0.01 against valence
* The two word lists agree only moderately on arousal (+0.23) but better on valence (+0.38): arousal is the harder dimension for automatic norm generation too
* The questionnaire covers only 2 of the 4 circumplex corners; the *exhausted / resigned* corner — the one that matters most for retention — has no item at all
* All arousal numbers are on the complete dataset; German-Emotions is cached, everything after it is arithmetic

**Roadblocks & Next Steps:**
* Commit the backlog of work from 06.–09.08. and today
* **Overnight:** run `03_b` (blind) first, then Part 3 of notebook 03 (anchored) — sequentially, not in parallel, both use gemma2:9b. Run 07 afterwards; it picks up both exports automatically
* Arousal is stopped at a documented state. The one step that would settle the ranking is lemmatising the comments so BAWL-R becomes a reliable referee (median 3 matches today)
* An LLM arousal run is now **optional** rather than necessary
* Still open for Milestone 3: the automated end-to-end pipeline and the draft presentation
* For the next survey wave: four items in the JAWS pattern, one per circumplex corner, would make the arousal axis properly validatable

---

### 🟢 Tuesday, 11.08.2026
* **What I did:** [Write here...]
* **Decisions made:** [Write here...]
* **Roadblocks & Next Steps:** [Write here...]

### 🟢 Wednesday, 12.08.2026
* **What I did:** [Write here...]
* **Decisions made:** [Write here...]
* **Roadblocks & Next Steps:** [Write here...]

### 🟢 Thursday, 13.08.2026
* **What I did:** [Write here...]
* **Decisions made:** [Write here...]
* **Roadblocks & Next Steps:** [Write here...]

### 🟢 Friday, 14.08.2026
* **What I did:** [Write here...]
* **Decisions made:** [Write here...]
* **Roadblocks & Next Steps:** [Write here...]

### 🟢 Saturday, 15.08.2026 & Sunday, 16.08.2026 (Weekend/Buffer)
* **What I did:**
    * Reworked the key visual (`elfi_visual_v3`) — warmer palette, and adopted it for the deck and the dashboard header so both read as one product
    * Built the final deck generator (`presentation/build_final_deck.py`, archived) against the course brief: ten slides, one image or at most four points each, speaker notes with timings that the build refuses to let exceed ten minutes
    * Loading the real export into Power BI failed with *"We couldn't parse the input provided as a DateTime value"*. Traced it to `QuoteStyle.None`, which the Text/CSV connector inserts by default: comments containing a line break were torn into several rows, every column shifted, and text landed in the timestamp columns
    * That trail led further. The question-to-feature mapping compared column headers as exact strings, and the survey tool leaves zero-width spaces (`U+200B`) in some of them — those questions never matched and their features stayed empty. Fifteen features were affected, three of them entirely
    * Also found that the department question is a cascading dropdown spread over 54 columns, of which the transform read only the first, and that the `ID` column was emptied by a byte-order-mark in a template column name
    * Replaced `data/02`–`04` with a single `notebooks_high5/00_ingest.ipynb` that normalises headers before matching, follows the dropdown chain per row, derives iteration and run from the files themselves, and drops rows carrying no metadata at all
    * Reworked the topic step: dropped the reduction to 35 topics (a number carried over from the synthetic run, which on real comments produced one catch-all topic), added `reduce_outliers`, and cut the topic hierarchy with Ward linkage into twelve groups
    * Replaced the two unreadable charts in `05` — a scatter carrying one label per topic, and a dot plot with one row per topic — with small multiples per group and a ranked bar of the largest topics
* **Decisions made:**
    * Keep the export CSVs but load them with `QuoteStyle.Csv` and explicit types; the generated `power_query.m` (archived) now carries the types so nothing has to be set by hand in Power BI
    * Iteration and run are read from the files' own columns, with the file name as a cross-check that raises on disagreement rather than guessing
    * Rows without id, timestamp and any answer are dropped as spreadsheet residue; everything else is flagged, never removed
    * Company topics become a second dimension beside the themes rather than being folded into them — a project name is discussed across many themes, and only its own axis follows it across all of them
* **Roadblocks & Next Steps:**
    * The topic labels come from a language model, one call per topic, so it cannot tell them apart: several topics were handed the same name. Bundling equal names is deliberate now, but the group names were never reviewed and one of them describes only part of its content
    * Level 3 of the department hierarchy still carries numbering the form invented for units sharing a name; deferred, documented

---

## 📅 Week 4: Finalization, Documentation & Presentation (17.08. – 20.08.2026)

**Milestone 4 target: Project Finalized (Deadline 20.08.2026 / Dry Run 18.08.2026)**

### 🟢 Monday, 17.08.2026
* **What I did:**
    * Brought the real export into the existing dashboard. The five queries already there needed the quote style corrected, the fixed column count removed and a stray drill-down step deleted; four tables were missing entirely and were added from the generated blocks
    * Rebuilt the department hierarchy end to end. Codes are now derived only where an answer really is a code — an earlier version reshaped free text instead of rejecting it, which made it harder to read rather than cleaner. Level two is cut to two segments, which removes the numbering the form invented
    * Found that the department question exists two or three times per file, once per company, and that only the first column was read. Reading the first *filled* column per row instead recovered the department for almost everyone who previously had none
    * Curated the division mapping with domain input. Several units the dropdown places under another one are divisions in their own right; some families of codes belong together under one heading; one group the survey asks for has no place in the department hierarchy at all and was kept as its own entry. The mapping lives in a small, readable table that can be extended as further cases surface
    * Wrote `bereich_mapping.txt` (archived), generated from the data rather than from the code, listing the divisions, every answer a rule changed, the remainder, and the open cases — a rule that stops matching disappears from the file by itself
    * Wrote two process documents, one per notebook folder, in German and step by step: what happens, what comes out, what it is validated against, and the quality measures explained in plain terms
    * Cleaned up: findings reports, superseded transform notebooks, exploration outputs, obsolete mapping logs and the lexicon source downloads moved into a structured archive
* **Decisions made:**
    * The shareable package is the generated dataset, not the generator. The generator reads a real survey file as its template and names real structures, so it stays where it is; and a freshly generated dataset would no longer match the figures the notebooks' own text quotes
    * Topics that mean one thing to a reader share a label, and the reporting tool groups equal labels by itself. The keys stay distinct underneath, so nothing is merged in the model and the fine topics remain available
    * Absence gets named rather than left blank: "no company topic" is a member of its dimension with its own key, so it can be filtered like any other. A blank would silently drop those comments from every selection
    * Documentation is generated from the data wherever possible, so it cannot drift from what the pipeline actually does
* **Roadblocks & Next Steps:**
    * Two group names were produced by a language model and never reviewed; one covers only part of what sits in its group
    * One unit that is a division of its own can be reached through two different branches of the dropdown; the rule matches on one of them, so the other path does not join it
    * Fine topics carry names generated one at a time; several are generic enough to repeat, which is now handled by bundling rather than by better naming
    * Next: fill the results slide in the deck, run the dry run, keep the shareable package free of anything that points back at the original survey

### 🟢 Tuesday, 18.08.2026 (Dry Run!)
* **What I did:** [Write here...]
* **Decisions made:** [Write here...]
* **Roadblocks & Next Steps:** [Write here...]

### 🟢 Wednesday, 19.08.2026
* **What I did:** [Write here...]
* **Decisions made:** [Write here...]
* **Roadblocks & Next Steps:** [Write here...]

### 🟢 Thursday, 20.08.2026 (Final Presentation!)
* **What I did:** [Write here after the presentation.]
