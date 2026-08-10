# ELFI – Daily Project Diary & Decision Log

This document serves as the chronological daily journal for the ELFI (Emotional & Latent Feedback Interpretation) project. Here, all daily activities, technical decisions, and roadblocks are recorded.

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
* Conceptual work on the project framing on a Miro board (8 board snapshots archived under `prep/miro_board/01_2026_0727/`)
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
* Wrote the prompt specification for synthetic data generation (`prep/prompt_datengenerierung.docx`)
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
* Drafted the column mapping for the original survey export (`data/column_mapping.md`)
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
* Finalised the project setup documentation: `goals.md` (4 milestones with KPIs), `project_description.md`, `emotional_models.md`, `german_sentiment_models.md`, README, ELFI visuals
* Integrated the original High5 survey exports (26 files in `data/high5/`, plus 27 out-of-scope files separated into `data/out_of_scope/`)
* Built the mapping suggestion notebook (`data/03_create_mapping_suggestions.ipynb`) and generated the question→feature mapping
* Produced the first mapping metadata: `private_question_feature_mapping.csv`, `mapping_check_by_file.csv`, `mapping_duplicates_by_file.csv`, `transform_report.csv`, `unmapped_columns_transform.csv`
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
* Built the file and question inventory across all survey runs (`file_inventory.csv`, `questions_inventory.csv`, `question_master.csv`, `schema_profile.csv`, `question_extraction_check.csv`)
* Restructured the repository for the original data (`fa1e52d prepare for original data`, 16:49): notebooks moved into `notebooks/`, documentation reworked, README expanded

**Decisions made:**
* A single `question_master.csv` becomes the canonical reference for all question variants across survey runs
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
* Ran the full High5 transformation (`data/04_transform_high5.ipynb` → `high5_transformed.csv`, 05:36) including the duplicate/parking decisions (`mapping_decisions_by_file.csv`, `mapping_parked_duplicates.csv`)
* Built the High5 inventory notebook (`data/02_create_high5_inventory.ipynb`) and consolidated the synthetic data notebook (`data/01_create_synthetic_survey_data.ipynb`)
* Generated automated EDA profiles for both datasets (`fast_dummy_eda.html`, `private/reports/fast_eda_high5.html`)
* Full EDA on the real High5 data (`private/01_eda_on_high5_data.ipynb` → `high5_eda_ready.csv`, 14:03) with written findings (`findings_eda_on_high5.md`)
* Compared synthetic vs. real data structurally and in distribution (`compare_synthetic_high5.md`)
* First German sentiment run on the synthetic data (`synthetic_sentiment_enriched.csv`, 18:24) and first results write-up (`first_results.md`, 18:56)
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
* Defined the MVP scope (`private/MVP.md`) and the measurable success metrics (`presentation/00_SuccessMetrics.md`) — closing the open item from Milestone 1
* Documented the synthetic-data findings (`findings_synthetic_data.md`)
* **Started the first BERTopic experiments** (`archive/05_topic_modeling_bertopic.ipynb`, 13:56)
* Built two German stopword lists for topic modelling (`german_stopwords_extended.txt`, `german_stopwords_full.txt`)
* Rewrote the synthetic data generator as reproducible scripts (`create_synthetic_high5_dataset.py`, then `_v2.py`) and generated a 5,000-row dataset
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
* Renumbered and consolidated the public notebooks (01 EDA / 02 Zero-Shot / 03 German Sentiment / 04 BERTopic) and regenerated `synthetic_eda_ready.csv`
* Ran the zero-shot core-affect classification on the real data (`private/02_zero_shot_text_analysis_high5.ipynb` → `zero_shot_core_affect.csv`, 14:00)
* Continued the BERTopic work (`notebooks/04_topic_modeling_bertopic.ipynb`, 15:20)
* Built the midterm deck generator (`presentation/build_midterm_deck.py`) and produced the first two deck versions (16:31 and 23:10)
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
* Built the Power BI dashboard (`presentation/01_Dashboards.pbix`, 16:43) for the interim results
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
* Built a reduced variant with 35 topics (`models/bertopic_reduced_35`, 17:18) to lower the outlier rate
* Generated LLM-based topic labels via Ollama (`private/05_topic_labelling_ollama.ipynb` → `topic_labels_ollama.csv`, 17:59)
* Documented the BERTopic evaluation (`private/findings/findings_bertopic.md`, 18:14)
* Ran the German sentiment model across the full High5 dataset (`high5_sentiment_enriched.csv`, 18:53)

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
* Built the arousal modelling notebook (`private/06_arousal_modelling.ipynb`, 21:12)
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
* Reviewed and corrected this decision log against the Git history and file timestamps (week structure was misaligned; Week 1 was undocumented)
* Continued work on `private/03_german_sentiment_analysis_high5.ipynb` and `private/04_topic_modeling_bertopic_high5.ipynb`
* Extended `.gitignore` to exclude the large model artefacts and Office lock files

**Decisions made:**
* Keep the log aligned with verifiable artefacts (commits, file timestamps) rather than reconstructing from memory
* `models/` stays out of Git — model artefacts are regenerated from the notebooks, not versioned

**Roadblocks & Next Steps:**
* Commit the backlog of work from 06.–09.08.
* Build the automated end-to-end pipeline (Input → Preprocessing → Model → Output) for the Power BI connection
* Select the final model from at least 3 tested variants and document the feature engineering

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
* **What I did:** [Write here, or leave empty if resting!]
* **Decisions made:** [Write here...]
* **Roadblocks & Next Steps:** [Write here...]

---

## 📅 Week 4: Finalization, Documentation & Presentation (17.08. – 20.08.2026)

**Milestone 4 target: Project Finalized (Deadline 20.08.2026 / Dry Run 18.08.2026)**

### 🟢 Monday, 17.08.2026
* **What I did:** [Write here...]
* **Decisions made:** [Write here...]
* **Roadblocks & Next Steps:** [Write here...]

### 🟢 Tuesday, 18.08.2026 (Dry Run!)
* **What I did:** [Write here...]
* **Decisions made:** [Write here...]
* **Roadblocks & Next Steps:** [Write here...]

### 🟢 Wednesday, 19.08.2026
* **What I did:** [Write here...]
* **Decisions made:** [Write here...]
* **Roadblocks & Next Steps:** [Write here...]

### 🟢 Thursday, 20.08.2026 (Final Presentation!)
* **What I did:** Held final presentation, successfully completed the project!
