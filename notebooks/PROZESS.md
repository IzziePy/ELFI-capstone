# ELFI — der Prozess auf den synthetischen Daten

Dieses Dokument beschreibt, was die sieben Notebooks in diesem Ordner tun, was dabei
herauskommt und woran geprüft wird, ob das Ergebnis etwas taugt.

Alle Notebooks laufen mit `SOURCE = "synthetic"`. Der parallele Ordner
`notebooks_high5/` enthält dieselbe Kette auf der echten Mitarbeiterbefragung; dort
liegt ein eigenes Prozessdokument.

---

## Warum es diese Kette zweimal gibt

Die echten Kommentare sind Mitarbeiterfeedback und dürfen das Unternehmen nicht
verlassen. Um die Methode zeigen, teilen und nachvollziehbar machen zu können, gibt es
einen synthetischen Zwilling: 5.000 erzeugte Antwortbögen mit derselben Struktur —
gleiche 54 Fragen, gleiche acht Dimensionen, gleiches Exportformat.

**Was dieser Ordner leistet.** Er zeigt, dass die Kette von Anfang bis Ende läuft, und
er ist die Grundlage für das teilbare Dashboard.

**Was er nicht leisten kann.** Die synthetischen Kommentare wurden aus Bausteinen
zusammengesetzt, die anhand eines gezogenen Emotionslabels und eines Valenzniveaus
ausgewählt wurden. Emotionaler Inhalt ist hier eine **Eingabe** des Generators, keine
Eigenschaft beobachteten Feedbacks. Ergebnisgrößen sind darum nicht auf echte Daten
übertragbar — Reihenfolgen von Verfahren schon, Effektstärken nicht.

Die wichtigste Abweichung steht am Anfang: hier schreibt **die Hälfte** der Befragten
einen Kommentar. Auf echten Daten fällt die Kommentarquote deutlich geringer aus, und
damit wiegt der Selektionseffekt dort erheblich schwerer.

---

## Die Kette

```
data/synthetic/synthetic_data_transformed.csv
      │
01    Die Daten          bereinigen, Qualitätssignale, Dimensionen
02    Valenz             wie angenehm ist der Kommentar?
03    Arousal            wie aktivierend ist er?
04    Circumplex         beide Achsen zusammen
05    Themen             worüber wird geschrieben?
06    Pipeline           Export für Power BI
      │
07    Datengetriebene Achse    Seitenzweig, prüft die Methode selbst
```

Ein Ingest-Schritt fehlt hier: die synthetischen Daten liegen fertig im Paket. Die
echte Kette hat davor ein Notebook `00`, das die Excel-Dateien der Befragung
zusammenführt.

---

## Zwei Dinge, die beim Ausführen zu wissen sind

**Der Datenordner heißt `synthetic_dataset`.** Alle Notebooks setzen oben
`DATA = Path("../synthetic_dataset")` und leiten daraus Eingabe, Ausgabeordner und
`config/` ab. Der Ordner muss neben `notebooks/` liegen.

**Jedes Notebook hat einen zweiten, hier nicht benutzten Zweig.** Die Zelle mit den
Pfaden enthält ein `if SOURCE == "synthetic": … elif SOURCE == "real": …`. Der
Real-Zweig stammt aus der parallelen Kette auf der echten Befragung und zeigt in
diesem Paket ins Leere — die Dateien, die er sucht, sind nicht enthalten und werden
auch nicht mitgeliefert. Da `SOURCE = "synthetic"` fest gesetzt ist, wird er nie
betreten. Er ist absichtlich stehen geblieben, damit erkennbar bleibt, dass beide
Ketten denselben Code teilen und sich nur in der Datenquelle unterscheiden.

**Notebook 03 braucht zwei Dateien, die nicht im Paket sind.** Die Wortlexika BAWL-R
und die IMS-Stuttgart-Normen sind veröffentlichte Forschungsdaten mit eigenen
Nutzungsbedingungen und werden nicht mitverteilt. Bezugsorte stehen in
`REFERENCES.md`; eine Notiz am Anfang von 03 sagt, wohin sie gehören.

---

## Schritt 01 — Die Daten

**Was passiert.** Textantworten werden zu Zahlen (1 bis 5), die 54 Fragen zu acht
Themendimensionen verdichtet, Qualitätssignale berechnet.

**Ergebnis.**

```
5.000 Bögen, 84 Spalten
davon mit Kommentar: 2.500  (50 %)
8 Dimensionen + Gesamtscore
2.268 "Kann ich nicht beurteilen" → als fehlend, nicht als neutral gezählt
12 Bereiche, 5 Wellen / 14 Läufe
mittlere Ausfülldauer: 8,9 Minuten
```

**Qualitätssignale.** Sie werden markiert, nicht entfernt.

```
Schnellantworten (unterste 5 % der Dauer)   238   4,8 %
gleichförmiges Antworten (Streuung < 0,3)   104   2,1 %
beides zugleich                               2   0,0 %
```

*Was „gleichförmig" heißt:* wer bei allen 54 Fragen fast dasselbe ankreuzt, hat eine
Standardabweichung nahe null. Der Schwellwert 0,3 liegt in einer Lücke der
Verteilung, trennt also eine abgesetzte Gruppe ab statt einen willkürlichen
Prozentsatz.

---

## Schritt 02 — Valenz

**Was passiert.** Valenz heißt: wie angenehm oder unangenehm ist das Erlebte, von dem
der Kommentar berichtet. Zwei Verfahren treten gegeneinander an.

*Der Grundlinien-Ansatz:* `oliverguhr/german-sentiment-bert`, ein auf deutsche
Stimmungserkennung trainiertes Modell.

*Der Herausforderer:* `gemma2:9b`, ein Sprachmodell, lokal über Ollama, mit einer
erklärten Skala von −1 bis +1 und ohne Kenntnis der Umfrageantworten — deshalb
„blind".

**Wogegen validiert.** Gegen sechs Umfragewerte derselben Person: `arbeitsfreude`,
`arbeitgeber_empfehlung`, `teamstimmung`, `work_life_balance`, `engagement_score`,
`overall_score`.

**Ergebnis.**

```
Verfahren                    Übereinstimmung   Werte am Rand   verschiedene Werte
BERT, einfache Differenz            +0,304           65 %             2.497
BERT, neutral-adjustiert            +0,306           68 %             2.497
Gemma 2 · 9B, blind                 +0,393            0 %                18

Sieger: Gemma 2 · 9B
```

*„Werte am Rand"* heißt: Anteil der Kommentare jenseits von ±0,9. Bei BERT sind das
zwei Drittel — das Modell drängt alles an die Extreme, was für eine Rangfolge
unbrauchbar ist.

**Die Grenze des Siegers.** Gemma nutzt nur **18 verschiedene Werte** und legt 21 %
aller Kommentare auf denselben Wert (+0,50).

**Vergleich mit der echten Befragung.** Die Zahlen unten sind Kennwerte der
*Verfahren*, keine Aussagen über die Befragten.

```
Verfahren                    hier      echt
BERT, einfache Differenz    +0,304    +0,243
BERT, neutral-adjustiert    +0,306    +0,259
Gemma 2 · 9B, blind         +0,393    +0,389
verschiedene Werte              18        27
```

Die **Reihenfolge** der Verfahren ist identisch — Gemma vorn, dann BERT. Die
**Abstände** nicht: auf synthetischem Text erreicht BERT deutlich mehr, weil erzeugte
Kommentare ein klareres Stimmungssignal tragen als echtes Feedback. Genau das ist die
Grenze synthetischer Texte: Ordnungen übertragen sich, Effektstärken nicht.

---

## Schritt 03 — Arousal

**Was passiert.** Arousal heißt Aktivierung: „ich bin wütend" und „ich habe
resigniert" sind beide unangenehm, aber das eine ist aufgewühlt und das andere matt.

**Das Problem vorweg.** Die Befragung fragt Aktivierung nicht ab, es gibt also kein
direktes Kriterium. Behelf: aus den Umfrageantworten wird nach einer Idee von Warr
(1990) eine Ersatzachse rotiert, und ein unabhängiges Wortlexikon dient als
Schiedsrichter.

```
Kriterium aus der Umfrage    Überlappung mit dem Valenzkriterium: −0,001
Schiedsrichter BAWL-R        deckt 568 von 5.940 Wortformen ab
                             bewertet 77 % der Kommentare
IMS-Stuttgart-Normen         decken 5.561 von 5.940 Wortformen ab
```

**Ergebnis.**

```
Verfahren                              ~ BAWL-R   ~ Valenz
Emotions-Zuordnung von Hand              +0,030     +0,184
IMS-Lexikon, Mittel alle Wörter          +0,165     +0,179
IMS-Lexikon, Mittel ohne Stoppwörter     +0,221     +0,012   ← Sieger
IMS-Lexikon, Spitzenwort                 +0,167     −0,081
IMS-Lexikon, drei extremste              +0,146     −0,101
Sprachmodell gemma2                      +0,094     −0,161   ← verworfen
```

**Warum die letzte Spalte entscheidet.** Ein Verfahren, das stark mit Valenz
korreliert, misst die erste Achse noch einmal statt der zweiten. Der Sieger liegt bei
+0,012, also praktisch unabhängig. Das Weglassen der Stoppwörter ist der größte
Einzelgewinn: `+0,165 → +0,221`.

**Derselbe Sieger wie auf den echten Daten** — auch dort das IMS-Lexikon, Mittelwert
ohne Stoppwörter. Auf echten Daten fällt das Sprachmodell allerdings viel deutlicher
durch: dort korreliert es mit **−0,678** gegen Valenz, hier nur mit −0,161. Auch das
ist eine Kennzahl des Verfahrens, nicht der Befragten — sie sagt, dass das Modell zwei
Achsen verwechselt hat. Der Generator kannte Aktivierung nie als Parameter, weshalb
dieser Datensatz über diese Achse ohnehin am wenigsten sagen kann.

---

## Schritt 04 — Der Circumplex

**Was passiert.** Beide Achsen werden zusammengeführt, sodass jeder Kommentar ein
Punkt in einer Fläche ist — das Circumplex-Modell nach Russell (1980), mit Valenz und
Arousal als den zwei Kerneigenschaften jedes empfundenen Zustands (Lisa Feldman
Barrett).

**Zwei Korrekturen, die keine Kosmetik sind.**

*Valenz ist gerastert.* Nur 18 verschiedene Werte, also zeichnet sich der Circumplex
als Kamm senkrechter Streifen. Vor der Darstellung wird jeder Wert über das Intervall
gestreut, für das er steht.

*Arousal wird rangnormiert.* Deshalb bedeutet **0 auf der Arousalachse nicht „keine
Aktivierung", sondern „mittlere Aktivierung"**.

**Ergebnis.**

```
2.500 Kommentare mit beiden Achsen
Korrelation der Achsen: +0,012   (nahe null, sie messen Verschiedenes)

angespannt / nervös        unangenehm   hoch      649   26,0 %
erschöpft / resigniert     unangenehm   niedrig   647   25,9 %
begeistert / energiegel.   angenehm     hoch      602   24,1 %
gelassen / entspannt       angenehm     niedrig   602   24,1 %

unangenehm insgesamt: 51,9 %
```

**Hier zeigt sich der Charakter des Datensatzes.** Die vier Quadranten sind fast
gleich groß, weil der Generator die Emotionslabels gleichmäßig gezogen hat. Echtes
Feedback ist nicht gleichmäßig verteilt — welche Schieflage sich dort zeigt, ist ein
Ergebnis und gehört nicht in ein teilbares Dokument. Festzuhalten bleibt: eine
Verteilung, die aus diesem Datensatz stammt, sagt nichts über eine Belegschaft.

---

## Schritt 05 — Themen und Engagement

**Was passiert.** Zwei Fragen: worüber wird geschrieben, und lässt sich aus der
Sprache das Engagement vorhersagen.

**Themen.** BERTopic bettet jeden Kommentar in einen 384-dimensionalen Raum ein
(`paraphrase-multilingual-MiniLM-L12-v2`), reduziert mit UMAP, gruppiert mit HDBSCAN.
`gemma2:9b` vergibt die Namen. Dieser Lauf fittet **sein eigenes Modell** und sieht
das auf echten Kommentaren trainierte nie.

```
84 Themen gefunden, auf 35 reduziert  →  34 Themen plus Ausreißergruppe
ohne Thema: 329 Kommentare (13 %)

Informationsfluss im Unternehmen   212    8 %
Strategiekommunikation             207    8 %
Arbeitsumfeld und Wohlbefinden     205    8 %
Arbeitsbelastung                   196    8 %
Flexible Arbeitszeit               191    8 %
```

*Ausreißer.* HDBSCAN ordnet Kommentare, die es nicht sicher zuordnen kann, keiner
Gruppe zu. „Ohne Thema" ist deshalb eine echte Kategorie, keine fehlende Angabe.

*Zum Vergleich:* echtes Feedback ist thematisch weit breiter. Über mehrere Jahre
gewachsen bringen einige tausend Kommentare über zweihundert unterscheidbare Themen
hervor, und dort wird nicht reduziert. Die Reduktion auf 35 passt zu 2.500
gleichförmigen erzeugten Kommentaren; auf ein echtes Korpus übertragen presst sie
einen großen Teil in ein einziges Sammelthema.

**Engagement.** Kann die Sprache eines Kommentars den Engagement-Score vorhersagen,
den dieselbe Person in den geschlossenen Fragen gegeben hat? Getestet mit
Kreuzvalidierung.

```
Modell                          R²      Streuung
Grundlinie (Mittelwert)     −0,0017      0,0015
1 · nur Valenz               0,1858      0,0247
2 · + Arousal und Länge      0,1851      0,0234
3 · + TF-IDF                 0,1844      0,0216
    Random Forest            0,0989      0,0310
```

*Was R² bedeutet:* der Anteil der Streuung im Engagement, den das Modell erklärt.
0 heißt „nicht besser als der Mittelwert", 1 heißt „vollständig erklärt". 0,19 heißt:
ein einziger Stimmungswert aus dem Text erklärt fast ein Fünftel der Streuung eines
Fragebogenscores, den er nie gesehen hat.

*Der Maßstab für „echt".* Die Kreuzvalidierung teilt die Daten in Blöcke und rechnet
mehrfach. Wie stark das Ergebnis dabei schwankt — hier 0,025 — ist der Maßstab: eine
Verbesserung, die kleiner ist als diese Schwankung, ist Rauschen.

**Das Ergebnis ist eindeutig: nur Valenz zählt.** Arousal, Kommentarlänge und
tausende TF-IDF-Merkmale verbessern nichts, was über die Fold-Streuung hinausgeht. Der
Random Forest ist sogar schlechter als die linearen Modelle.

*Auf echten Daten kann das anders ausfallen, und dann ist eine Prüfung nötig.* Wenn
Vokabular die Vorhersage verbessert, heißt das nicht zwangsläufig, dass es etwas über
die Haltung der Person aussagt. Solche Störfaktoren sind bei echten Daten zu erwarten
und gehören geprüft: ein Modell, das allein die Strukturmerkmale kennt, zeigt, wie viel
des scheinbaren Textgewinns tatsächlich vom Text kommt. Dieses Notebook hat den Test
eingebaut, damit er auf jedem Korpus mitläuft.

---

## Schritt 06 — Die Pipeline

**Was passiert.** Der Export für Power BI: ein Sternschema aus Faktentabellen und
Dimensionen, dazu ein geglättetes Dichtefeld für die Heatmap.

**Datenschutz im Modell.** Kennzahlen mit dem Zusatz *(geschuetzt)* geben unter fünf
Antworten `BLANK()` zurück statt eines Wertes. Bei einem einzelnen Kommentar wäre der
sonst einer Person zuordenbar. Das Dichtefeld hat eine zweite Schwelle bei 50
Kommentaren — darunter zeigt eine geglättete Fläche Struktur, die die Daten nicht
hergeben.

Auf diesem Datensatz sind diese Regeln unkritisch, weil niemand identifizierbar ist.
Sie sind hier eingebaut, weil dieselbe Pipeline auf den echten Daten läuft.

**Beigelegte Dokumentation.** `power_query.m` mit typisierten Ladeblöcken,
`measures.dax`, `relationships.md`.

---

## Schritt 07 — Datengetriebene Achse (Seitenzweig)

**Die Frage.** Beide Achsen wurden von diesem Projekt gesetzt: Valenz wurde erfragt,
Arousal aus Wortnormen zusammengesetzt, die „Arousal" heißen. Lassen sich die Achsen
stattdessen in den Daten **finden**?

**Was passiert.** Nach dem Verfahren von Semo et al.: PCA über die 27
Emotionswahrscheinlichkeiten aus Schritt 03, die ersten zwei Komponenten mit Varimax
rotieren, und erst danach nachsehen, was herausgekommen ist.

**Ergebnis.**

```
Komponente   Varianz   kumuliert          Bezug zu den Projektachsen
PC1            9,0 %      9,0 %       Valenz −0,767   Arousal +0,043
PC2            6,3 %     15,3 %              +0,414          +0,124
PC3            5,7 %     21,0 %              +0,515          +0,057

Rotationswinkel: 24,9 Grad
Varimax-Kriterium: 0,5966 vorher, 0,7824 nachher

beste erreichbare Übereinstimmung über alle Rotationen:
  Valenzmaß         0,799
  Arousalmaß        0,161
  Arousalkriterium  0,088
```

**Die Valenzachse wird bestätigt, und deutlich.** Ein Verfahren, das nie erfährt, was
Valenz ist, findet eine Achse, die mit dem Valenzmaß bei 0,80 übereinstimmt.

**Die Arousalachse wird nicht bestätigt.** Der beste Wert über alle Rotationen ist
0,161. Es ist keine Frage der Rotation — die Dimension ist in der Fläche nicht
vorhanden, nicht bloß verdreht. Beide rotierten Achsen bestehen aus Angenehmheit: eine
ist in negativen Emotionen verankert, eine in positiven.

**Was dieser Datensatz dazu beitragen kann, und was nicht.**

*Das Valenzergebnis ist schwächer als es aussieht.* Ein Emotionsklassifikator, der
Valenz aus Text zurückgewinnt, dessen Valenz vor dem Text festgelegt wurde, ist ein
kürzerer Schluss als er scheint. Die 0,80 zeigen, dass die Maschinerie
funktioniert — der Join, die PCA, die Rotation — nicht dass es über echtes Feedback
etwas aussagt.

*Das Arousalergebnis steht dagegen nicht allein.* Aktivierung war nie ein Parameter
des Generators, ihr Fehlen könnte also am Generator liegen. Dieselbe Analyse auf
echten Daten kommt jedoch zum selben Schluss, und dort fällt die Entschuldigung mit
dem Generator weg.

---

## Die Qualitätsmaße auf einen Blick

**Spearman-Korrelation (ρ).** Vergleicht Reihenfolgen, nicht Werte. +1 heißt: beide
Messungen sortieren die Kommentare identisch. 0 heißt: kein Zusammenhang. Wird hier
benutzt, weil es auf die Rangfolge ankommt und nicht darauf, ob eine Skala linear zur
anderen liegt.

**R² (Bestimmtheitsmaß).** Anteil der erklärten Streuung, 0 bis 1. Braucht immer einen
Vergleich: allein ist 0,19 nicht interpretierbar, gegen eine Grundlinie von −0,002 und
eine Fold-Streuung von 0,025 schon.

**Kreuzvalidierung und Fold-Streuung.** Die Daten werden in Blöcke geteilt, das Modell
mehrfach auf einem Teil trainiert und am Rest geprüft. Die Schwankung zwischen den
Durchläufen ist der Maßstab dafür, ob eine Verbesserung echt ist.

**95-%-Konfidenzintervall.** Bei den Themen: der Bereich, in dem der Mittelwert dieser
Menge Kommentare wandern würde. Ein Thema mit 11 Kommentaren hat ein breites
Intervall — ohne diese Spanne sieht jede Rangfolge bedeutsam aus.

**Abdeckung.** Wie viele Kommentare überhaupt einen Wert bekommen. Beim
Arousal-Lexikon 100 %, beim BAWL-Schiedsrichter 77 %. Eine hohe Übereinstimmung auf
wenigen Kommentaren ist wenig wert.

**Unabhängigkeit vom Nebenkriterium.** Beim Arousal die wichtigste Zahl überhaupt: ein
Verfahren, das mit Valenz korreliert, misst die falsche Achse.

---

## Was dieser Datensatz zeigt und was nicht

**Zeigt er.** Dass die Kette vollständig läuft — Einlesen, zwei Messachsen,
Validierung gegen Fragebogenwerte, Themenmodell, Export, Dashboard. Dass die
**Reihenfolge** der Verfahren stabil ist: Gemma vor BERT bei der Valenz, das
IMS-Lexikon ohne Stoppwörter beim Arousal, Valenz allein beim Engagement.

**Zeigt er nicht.** Effektstärken. Sie fallen hier durchweg freundlicher aus, weil
erzeugter Text ein klareres Signal trägt. Und die Verteilung der Emotionen: die vier
gleich großen Quadranten sind ein Merkmal des Generators, nicht der Arbeitswelt.

**Der wichtigste Unterschied.** Hier schreibt die Hälfte der Befragten, auf echten
Daten ein weit kleinerer Teil. Der Selektionseffekt — wer sich äußert, ist im Schnitt
unzufriedener als wer schweigt — ist auf diesem Datensatz nicht messbar, weil er nicht
eingebaut wurde. Er ist aus anderen Zusammenhängen gut bekannt, etwa aus
Produktbewertungen oder sozialen Netzwerken: die Unzufriedenen schreiben, die
Zufriedenen schweigen.
