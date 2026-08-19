# References, Models and Tools

Everything the six notebooks in `notebooks/` build on: the theory the measures come
from, the word lists they look up, the models that do the scoring, and the libraries
that hold it together.

Each entry says which notebooks use it, so a reader can go from a number in a cell to
the thing it rests on.

---

## Theory of affect

The two-dimensional view of affect the whole project is organised around, and the
measurement instrument it borrows its criterion construction from.

**Russell, J. A. (1980).** A circumplex model of affect. *Journal of Personality and
Social Psychology, 39*(6), 1161–1178. https://doi.org/10.1037/h0077714
→ *Notebooks 02, 03, 04.* The circumplex itself: affect as a position on a valence
and an arousal axis rather than a list of discrete emotions.

**Barrett, L. F., & Russell, J. A. (1999).** The structure of current affect:
Controversies and emerging consensus. *Current Directions in Psychological Science,
8*(1), 10–14. https://doi.org/10.1111/1467-8721.00003
→ *Notebooks 02, 03, 04.* Core affect as the pair of dimensions underlying momentary
feeling — the framing this project uses when it places a comment in the space.

**Warr, P. (1990).** The measurement of well-being and other aspects of mental
health. *Journal of Occupational Psychology, 63*(3), 193–210.
→ *Notebook 03.* The source of the criterion construction: rotating two survey items
so that their sum isolates activation and their difference isolates mood, instead of
using the items as axes directly.

**Van Katwyk, P. T., Fox, S., Spector, P. E., & Kelloway, E. K. (2000).** Using the
Job-Related Affective Well-Being Scale (JAWS) to investigate affective responses to
work stressors. *Journal of Occupational Health Psychology, 5*(2), 219–230.
→ *Notebook 03,* as the recommendation rather than as something used: four JAWS items,
one per circumplex corner, would give the arousal axis the criterion this survey does
not contain.

---

## Affective word norms

Two independent German word lists. One supplies the arousal measure, the other judges
it — a measure cannot be validated against its own source.

**Köper, M., & Schulte im Walde, S. (2018).** Analogies in complex verb meaning
shifts: The effect of affect in semantic similarity models. *Proceedings of
NAACL-HLT 2018.*
Resource: https://www.ims.uni-stuttgart.de/en/research/resources/experiment-data/de-affect-norms/
→ *Notebooks 03, 04, 06.* The IMS Stuttgart affective norms: valence and arousal
values for German word forms, from which the winning arousal measure is built.

**Võ, M. L.-H., Jacobs, A. M., & Conrad, M. (2009).** Cross-validating the Berlin
Affective Word List Reloaded (BAWL-R). *Behavior Research Methods, 41*(2), 534–538.
https://doi.org/10.3758/BRM.41.2.534
→ *Notebook 03.* Used only as the independent referee against which every arousal
candidate is judged, including the ones built from the IMS norms.

---

## Models

### Sentiment and emotion classification

**Guhr, O., Schumann, A.-K., Bahrmann, F., & Böhme, H. J. (2020).** Training a
broad-coverage German sentiment classification model for dialog systems.
*Proceedings of LREC 2020,* 1620–1625.
Model: https://huggingface.co/oliverguhr/german-sentiment-bert
→ *Notebook 02.* The first valence attempt: three-class sentiment, turned into one
number. Superseded, and the notebook shows why.

**Lalk, C. (n.d.).** German-Emotions [Model]. Hugging Face.
https://huggingface.co/ChrisLalk/German-Emotions
→ *Notebook 03.* Returns a probability for each of 28 emotions per comment. Basis of
the first arousal attempt, which failed.

**Demszky, D., Movshovitz-Attias, D., Ko, J., Cowen, A., Nemade, G., & Ravi, S.
(2020).** GoEmotions: A dataset of fine-grained emotions. *Proceedings of ACL 2020,*
4040–4054. https://aclanthology.org/2020.acl-main.372/
→ *Notebook 03,* indirectly: the 28 emotion labels the model above returns, and which
the hand-made mapping in that notebook assigns arousal weights to, come from this
dataset.

### Language models

**Gemma Team, Google DeepMind (2024).** Gemma 2: Improving open language models at a
practical size. arXiv:2408.00118
→ *Notebooks 02, 03, 05, 06.* Run locally as `gemma2:9b`. Scores valence, is tested
and rejected for arousal, and names the topics.

**Reimers, N., & Gurevych, I. (2019).** Sentence-BERT: Sentence embeddings using
Siamese BERT-networks. *Proceedings of EMNLP-IJCNLP 2019,* 3982–3992.
Model: https://huggingface.co/sentence-transformers/paraphrase-multilingual-MiniLM-L12-v2
→ *Notebook 05.* Supplies the multilingual sentence embeddings BERTopic groups.

### Topic modelling

**Grootendorst, M. (2022).** BERTopic: Neural topic modeling with a class-based
TF-IDF procedure. arXiv:2203.05794
→ *Notebooks 05, 06.* Embeds the comments, clusters them, and reads the
characteristic words out of each cluster. Fitted fresh on each corpus rather than
reused.

**McInnes, L., Healy, J., & Melville, J. (2018).** UMAP: Uniform manifold
approximation and projection for dimension reduction. arXiv:1802.03426
→ *Notebook 05.* Reduces the 384-dimensional comment embeddings before clustering,
and separately produces the two-dimensional map the topics are drawn on. The two runs
use different settings on purpose: the first serves the clustering, the second the eye.

**McInnes, L., Healy, J., & Astels, S. (2017).** hdbscan: Hierarchical density based
clustering. *Journal of Open Source Software, 2*(11), 205.
https://doi.org/10.21105/joss.00205
→ *Notebook 05.* Groups the reduced embeddings. It assigns no cluster to comments it
cannot place, which is why "no topic" is a real category here rather than missing
data; comments close enough to a cluster are reassigned afterwards above a similarity
threshold.

---

## Software

**Ollama.** https://ollama.com
→ *Notebooks 02, 03, 05.* Runs the language models locally, which is what allows
free-text comments to be scored without any of them leaving the machine.

**Wolf, T., Debut, L., Sanh, V., Chaumond, J., Delangue, C., Moi, A., … Rush, A. M.
(2020).** Transformers: State-of-the-art natural language processing. *Proceedings of
EMNLP 2020: System Demonstrations,* 38–45.
→ *Notebooks 02, 03, 05.* Loads and runs the sentiment and emotion classifiers.

**Pedregosa, F., Varoquaux, G., Gramfort, A., Michel, V., Thirion, B., Grisel, O., …
Duchesnay, É. (2011).** Scikit-learn: Machine learning in Python. *Journal of Machine
Learning Research, 12,* 2825–2830.
→ *Notebook 05.* Cross-validation, ridge regression with per-fold regularisation,
TF-IDF features, random forest.

**Harris, C. R., Millman, K. J., van der Walt, S. J., Gommers, R., Virtanen, P.,
Cournapeau, D., … Oliphant, T. E. (2020).** Array programming with NumPy. *Nature,
585,* 357–362. https://doi.org/10.1038/s41586-020-2649-2
→ *All notebooks.*

**McKinney, W. (2010).** Data structures for statistical computing in Python.
*Proceedings of the 9th Python in Science Conference,* 56–61.
→ *All notebooks.* pandas.

**Virtanen, P., Gommers, R., Oliphant, T. E., Haberland, M., Reddy, T., Cournapeau,
D., … van Mulbregt, P. (2020).** SciPy 1.0: Fundamental algorithms for scientific
computing in Python. *Nature Methods, 17,* 261–272.
https://doi.org/10.1038/s41592-019-0686-2
→ *Notebook 04.* The kernel density estimate the circumplex is drawn from.

**Hunter, J. D. (2007).** Matplotlib: A 2D graphics environment. *Computing in
Science & Engineering, 9*(3), 90–95. https://doi.org/10.1109/MCSE.2007.55
→ *Notebooks 01, 02, 04, 05.*

---

## Word lists

**Götze, M., & Geyer, S.** German enhanced stopwords (1,849 entries).
https://github.com/solariz/german_stopwords ·
https://solariz.de/de/downloads/6/german-enhanced-stopwords.htm
→ *Notebooks 03, 05.* Removed before the arousal lexicon averages a comment, and
before the topic model reads characteristic words out of a cluster. Dropping them was
the single largest gain on the arousal axis — function words carry no activation and
dilute the average. The list is offered as link-ware; the attribution above is the
condition of use, and its header is kept intact in the file.

**German stopwords, extended list (127 entries).** `data/config/german_stopwords_extended.txt`
→ Kept as the smaller alternative from an earlier comparison. Very likely the plain
variant from the same repository, but the file carries no header, so the provenance is
not certain. The full list is the one in use.

---

## Theoretical background of the model comparison

These are not used by the notebooks. They are the alternatives the project weighed
before settling on the circumplex.

**Ekman, P. (1992).** An argument for basic emotions. *Cognition and Emotion, 6*(3–4),
169–200. https://doi.org/10.1080/02699939208411068
→ The categorical alternative: a small set of universal, discrete emotions. Rejected
here because free-text feedback rarely falls cleanly into six categories.

**Plutchik, R. (1980).** *Emotion: A psychoevolutionary synthesis.* Harper & Row.
→ The hybrid alternative: eight primary emotions in opposing pairs, with intensity
levels. Vivid but hard to annotate automatically, which is why dimensional models are
more common in text analysis.

**Plutchik, R. (1962).** *The emotions: Facts, theories, and a new model.* Random
House.
→ The earlier statement of the same model, listed because the wheel is often cited
from this work rather than from the 1980 synthesis.

**Barrett, L. F. (2017).** The theory of constructed emotion: An active inference
account of interoception and categorization. *Social Cognitive and Affective
Neuroscience, 12*(1), 1–23. https://doi.org/10.1093/scan/nsw154
→ The constructivist alternative: emotions as concepts the brain builds in context
rather than fixed reactions. It argues against keyword lists and in favour of
context-sensitive models, which is the reasoning behind using a language model here.

---

## Sentiment tools considered but not used

German sentiment approaches that were compared before one of them was chosen.

**Tymann, K. M., Lutz, M., Palsbröker, P., & Gips, C. (2019).** GerVADER — A German
adaptation of the VADER sentiment analysis tool for social media texts. *Proceedings
of LWDA 2019.* FH Bielefeld University of Applied Sciences.
→ The rule-based German option: transparent and fast, but clearly weaker than the
transformer models, so it served only as a possible baseline.

**Hutto, C. J., & Gilbert, E. (2014).** VADER: A parsimonious rule-based model for
sentiment analysis of social media text. *Proceedings of the Eighth International AAAI
Conference on Weblogs and Social Media (ICWSM-14).*
→ The English original GerVADER adapts.

---

## A note on completeness

Sections 1 to 4 cover what the six notebooks in `notebooks/` actually use. Sections 5
and 6 cover what is cited in the project pages under `project_setup/` — the models and
theories that were weighed and not chosen. The separation is deliberate: a reader
should be able to tell what carries a result from what informed a decision.

Four entries still need checking against the originals, because they were written from
memory rather than from the sources at hand: **Warr (1990)**, **Van Katwyk et al.
(2000)**, **Plutchik (1962)** and **Plutchik (1980)** — volume numbers, page ranges and
publishers in particular. Every other entry came from the previous version of this
file, from a model card, from a resource page, or from the paper itself.

**Barrett (2017)** was checked against Crossref and PubMed on 19 August 2026. An
earlier version of this file cited *12*(11), 1833 — that is the erratum
(doi:10.1093/scan/nsx060, a single page), not the article.
