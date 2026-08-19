# ELFI – Emotional & Latent Feedback Interpretation

## Project Description

### Background

Employee surveys generate large volumes of qualitative feedback. While quantitative survey results can be analysed and reported efficiently, free-text comments often remain underused despite containing valuable information about employee experiences, concerns, and opportunities for improvement.

ELFI (Emotional & Latent Feedback Interpretation) was developed to make employee feedback more accessible, scalable, and actionable through Natural Language Processing (NLP) and modern analytics techniques.

---

## Project Objective

The objective of ELFI is to transform German-language employee comments into structured analytical signals that support data-driven organisational development.

The solution combines core-affect measurement, topic discovery, engagement indicators, and interactive reporting to help organisations understand employee feedback at scale.

The project addresses three central questions:

- How do employees feel?
- What are employees talking about?
- Which topics and affective patterns are associated with engagement and organisational outcomes?

---

## Scope

ELFI processes free-text comments from employee surveys and enriches them with analytical indicators.

The solution includes:

- Valence measurement (pleasant vs. unpleasant experiences)
- Arousal measurement (activated vs. deactivated experiences)
- Circumplex-based mapping of core affect

Valence and arousal together are *core affect*, not emotion. Named emotions such as
anger or disappointment are built on top of core affect through learned concepts and
context, and ELFI does not attempt to recover them. What it measures is the affective
substrate; what it reports are positions in that space, never emotion labels.
- Topic modelling and thematic clustering
- Automated data-processing pipelines
- Interactive Power BI reporting

The resulting metrics and classifications are written back to structured tables and can be integrated into existing reporting environments.

---

## Methodological Approach

The project combines multiple NLP techniques and analytical methods:

- Transformer-based language models
- Sentiment and affect analysis
- Lexicon-based affective scoring
- Topic modelling
- Statistical validation against survey measures
- Data visualisation in Power BI

Particular attention is given to model validation and interpretability. Wherever possible, analytical measures are evaluated against independent survey criteria and external reference datasets.

---

## Deliverables

### 1. Validated Affect Measures

- Valence score
- Arousal score
- Circumplex coordinates

### 2. Topic Analytics

- Topic identification
- Topic prevalence
- Topic-level sentiment and affect measures

### 3. Data Processing Pipeline

- Automated text preprocessing
- Model execution
- Structured result export

### 4. Power BI Dashboard

- Interactive exploration of employee comments
- Affective and thematic reporting
- Filters for organisational units and survey waves
- Action-oriented reporting views for organisational stakeholders

### 5. Documentation

- Methodology
- Validation results
- Technical implementation
- Operational recommendations

---

## Expected Benefits

The solution enables organisations to:

- Analyse large volumes of employee comments consistently
- Identify affective patterns in employee feedback
- Detect recurring themes and concerns
- Support evidence-based decision-making
- Reduce manual effort in qualitative analysis
- Scale feedback analysis across survey formats and organisational units

---

## Future Development

Potential future enhancements include:

- Full automation of the processing pipeline
- Integration into recurring employee-listening processes
- Support for additional survey formats
- Multilingual language support
- Enhanced predictive analytics and trend monitoring

---

## Project Outcome

ELFI demonstrates how modern NLP methods can be combined with organisational analytics to transform unstructured employee feedback into actionable insights.

The project delivers both a validated analytical methodology and an operational reporting solution that supports employee listening at scale. By combining core-affect measurement, topic modelling, and interactive reporting, ELFI bridges the gap between qualitative employee feedback and evidence-based organisational decision-making.