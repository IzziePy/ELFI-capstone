# Findings on Synthetic Data

# English

## Dataset & Data Quality

- The synthetic dataset contains **500 survey responses** and **66 variables**.
- The dataset includes survey items, organisational variables, timestamps, and free-text comments.
- Data quality is generally excellent.
- No malformed records, invalid response categories, or empty columns were identified.
- Language and technical export variables were successfully removed during preprocessing.
- Survey duration values appear plausible, with a median completion time of approximately **13 minutes**.
- The synthetic dataset provides a structurally clean and internally consistent testing environment.

---

## Survey Structure & Missingness

- The dataset contains **54 automatically detected Likert items**.
- All response categories were successfully standardised and numerically recoded.
- The questionnaire structure follows the High5 framework and was organised into eight thematic dimensions.
- Missing values are rare compared to the real High5 dataset.
- Most survey items have nearly complete response coverage.
- Explicit *"Cannot Judge"* responses occur at low rates (approximately 3–5% per item).
- Non-judgement responses can be clearly distinguished from actual missing values.

### Key Findings

- Missingness is substantially lower than in the real dataset.
- Structural missingness is virtually absent.
- The synthetic data exhibits a highly complete survey structure.

---

## Reliability of Topic Groups

- Cronbach's Alpha values range from approximately **0.68** to **0.96**.
- Most topic groups achieve strong reliability (α > 0.80).
- Teamwork & Collaboration shows the highest internal consistency.
- Leadership & Communication demonstrates the lowest reliability but remains within an acceptable range for exploratory analyses.

### Key Findings

- Topic groups are psychometrically robust.
- The predefined High5 structure is well represented in the synthetic dataset.
- Reliability estimates support the use of aggregated topic scores.

---

## Topic Scores

- Topic scores generally range between **3.1 and 3.7** on the 1–5 scale.
- Most dimensions show moderate variability across respondents.
- Teamwork, Engagement, and Wellbeing tend to achieve the highest average scores.
- No major floor or ceiling effects were observed.

### Key Findings

- The full response scale is actively used.
- Score distributions resemble realistic employee-survey response patterns.
- The synthetic data captures meaningful variation across constructs.

---

## Organisational Analysis

### Business Unit Structure

- Five business units are represented.
- Business-unit sample sizes are relatively balanced.
- Group sizes range from approximately **82 to 114 respondents**.
- No business units fall below the minimum sample-size threshold.

### Business Unit Differences

- Average topic scores vary moderately across business units.
- No major outliers were identified.
- Score differences between business units generally remain small.

### Key Findings

- The organisational structure is suitable for benchmarking and comparison analyses.
- The balanced distribution avoids problems associated with very small groups.
- Synthetic business-unit differences remain realistic without introducing extreme group effects.

---

## Correlation Analysis

- Topic scores show strong positive relationships.
- Correlations are consistent with the conceptual overlap expected in employee-survey dimensions.
- The strongest relationships occur between Teamwork, Culture, and related organisational constructs.

### Key Findings

- Teamwork and Culture exhibit particularly strong associations (approximately r = 0.89).
- The correlation structure resembles a realistic employee-experience framework.
- Results support the construct validity of the synthetic dataset.

---

## Free-Text Comments

### Participation

- More than **96%** of respondents provided a free-text comment.
- Only a small number of respondents chose not to submit a comment.

### Comment Length

- Typical comments range between **16 and 38 words**.
- The median comment length is approximately **25 words**.
- Comment lengths show realistic variation without extreme outliers.

### Key Findings

- The synthetic dataset contains a very large volume of qualitative data.
- The high comment rate provides an excellent basis for NLP experiments and model development.
- Free-text participation is substantially higher than in the real High5 dataset.

---

## Commenters vs Non-Commenters

- Respondents without comments tend to report slightly higher topic scores across all dimensions.
- Differences between commenters and non-commenters range from approximately **-0.05 to -0.36 points**.

### Key Findings

- Commenters show somewhat more critical evaluations than non-commenters.
- The pattern is consistent with observations from the real dataset.
- Potential selection effects are already visible in the synthetic data.
- The synthetic dataset successfully reproduces realistic commenting behaviour.

---

## Overall Assessment

- The synthetic dataset is structurally realistic and internally consistent.
- Data quality is very high.
- Missingness is minimal.
- Topic groups demonstrate strong psychometric properties.
- Organisational comparisons are possible without major sample-size concerns.
- Free-text participation is exceptionally high.
- The dataset provides a strong foundation for feature engineering, NLP experimentation, model development, and benchmarking against the real High5 dataset.

---

# Deutsch

## Datensatz & Datenqualität

- Der synthetische Datensatz umfasst **500 Befragungen** und **66 Variablen**.
- Enthalten sind Survey-Items, Organisationsinformationen, Zeitvariablen und Freitextkommentare.
- Die Datenqualität ist insgesamt sehr hoch.
- Es wurden keine fehlerhaften Datensätze, ungültigen Antwortkategorien oder leeren Spalten identifiziert.
- Technische Export- und Sprachvariablen wurden erfolgreich entfernt.
- Die Bearbeitungszeiten erscheinen plausibel; die mediane Survey-Dauer liegt bei etwa **13 Minuten**.
- Der Datensatz stellt eine strukturell saubere und konsistente Testumgebung dar.

---

## Fragebogenstruktur & Missingness

- Es wurden **54 Likert-Items** automatisch erkannt.
- Alle Antwortkategorien konnten erfolgreich harmonisiert und numerisch rekodiert werden.
- Die Struktur folgt dem High5-Modell mit acht Themenbereichen.
- Fehlende Werte treten nur selten auf.
- Die meisten Items weisen nahezu vollständige Antwortabdeckung auf.
- *„Kann ich nicht beurteilen“*-Antworten treten mit etwa 3–5 % je Item auf.
- Non-Judgement-Antworten lassen sich klar von tatsächlichen Missing Values unterscheiden.

### Kernaussagen

- Missingness ist deutlich geringer als im Originaldatensatz.
- Strukturelle Missing Values treten praktisch nicht auf.
- Die Fragebogenstruktur ist nahezu vollständig.

---

## Reliabilität der Themenbereiche

- Die Cronbach-Alpha-Werte liegen zwischen etwa **0,68 und 0,96**.
- Die meisten Themenbereiche erreichen gute bis exzellente Reliabilitätswerte.
- Teamwork & Collaboration zeigt die höchste interne Konsistenz.
- Leadership & Communication weist die niedrigste Reliabilität auf, bleibt jedoch für explorative Analysen akzeptabel.

### Kernaussagen

- Die Themenbereiche sind psychometrisch robust.
- Die High5-Struktur wird durch die synthetischen Daten gut abgebildet.
- Die Reliabilität unterstützt die Nutzung aggregierter Themen-Scores.

---

## Themen-Scores

- Die Themen-Scores liegen überwiegend zwischen **3,1 und 3,7** auf der fünfstufigen Skala.
- Die meisten Dimensionen zeigen eine moderate Streuung.
- Teamwork, Engagement und Wellbeing weisen tendenziell die höchsten Mittelwerte auf.
- Es wurden keine ausgeprägten Floor- oder Ceiling-Effekte beobachtet.

### Kernaussagen

- Die komplette Antwortskala wird genutzt.
- Die Verteilungen wirken realistisch und entsprechen typischen Mitarbeiterbefragungen.
- Die Daten bilden unterschiedliche Wahrnehmungen sinnvoll ab.

---

## Organisationsanalyse

### Business Units

- Es sind fünf Business Units enthalten.
- Die Gruppengrößen sind relativ ausgewogen.
- Die Fallzahlen liegen zwischen etwa **82 und 114 Personen**.
- Es existieren keine kritischen Kleinstgruppen.

### Unterschiede zwischen Business Units

- Die Themen-Scores unterscheiden sich moderat zwischen den Business Units.
- Es wurden keine auffälligen Ausreißer identifiziert.
- Die Unterschiede bleiben insgesamt gering.

### Kernaussagen

- Die Organisationsstruktur eignet sich gut für Vergleichsanalysen.
- Die ausgewogenen Gruppengrößen ermöglichen stabile Auswertungen.
- Die simulierten Unterschiede wirken realistisch, ohne extreme Effekte zu erzeugen.

---

## Korrelationsanalyse

- Die Themen-Scores korrelieren durchgehend positiv miteinander.
- Die Zusammenhänge entsprechen den erwarteten Beziehungen zwischen den High5-Dimensionen.
- Besonders starke Zusammenhänge zeigen sich zwischen Teamwork, Kultur und verwandten Konstrukten.

### Kernaussagen

- Teamwork und Kultur zeigen besonders hohe Zusammenhänge (ca. r = 0,89).
- Die Korrelationsstruktur wirkt realistisch.
- Die Ergebnisse unterstützen die Konstruktvalidität der synthetischen Daten.

---

## Freitext-Kommentare

### Teilnahme

- Mehr als **96 %** der Befragten haben einen Freitextkommentar abgegeben.
- Nur ein kleiner Teil verzichtete auf einen Kommentar.

### Kommentarlänge

- Die meisten Kommentare enthalten zwischen **16 und 38 Wörtern**.
- Die mediane Kommentarlänge liegt bei etwa **25 Wörtern**.
- Die Länge der Kommentare variiert in realistischer Weise.

### Kernaussagen

- Die synthetischen Daten enthalten eine große Menge qualitativer Informationen.
- Die hohe Kommentarquote eignet sich hervorragend für NLP- und ML-Experimente.
- Die Freitextbeteiligung liegt deutlich über der des Originaldatensatzes.

---

## Kommentierende vs. Nicht-Kommentierende

- Personen ohne Kommentar bewerten sämtliche Themenbereiche leicht positiver.
- Die Unterschiede liegen zwischen etwa **-0,05 und -0,36 Punkten**.

### Kernaussagen

- Kommentierende bilden auch im synthetischen Datensatz eine etwas kritischere Teilgruppe.
- Das Muster entspricht den Beobachtungen im Originaldatensatz.
- Potenzielle Selektionseffekte werden realistisch reproduziert.
- Das Antwortverhalten wirkt plausibel und näherungsweise realitätsnah.

---

## Gesamtfazit

- Der synthetische Datensatz ist strukturell realistisch und intern konsistent.
- Die Datenqualität ist sehr hoch.
- Fehlende Werte treten nur in geringem Umfang auf.
- Die Themenbereiche weisen gute psychometrische Eigenschaften auf.
- Organisationsvergleiche sind problemlos möglich.
- Die Freitextdaten bieten eine hervorragende Grundlage für NLP-, Feature-Engineering- und Machine-Learning-Analysen.
- Der Datensatz eignet sich sehr gut als Entwicklungs-, Test- und Benchmark-Datensatz für den Vergleich mit den realen High5-Daten.