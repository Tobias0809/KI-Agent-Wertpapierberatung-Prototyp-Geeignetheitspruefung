***Regeln entscheiden. KI erklärt. – Ein KI-Agent für die Geeignetheitsprüfung nach MiFID II***
***Problem-Statement***

Die Finanzkrise 2008 hat gezeigt, was passiert, wenn Anlegern Produkte verkauft werden, die sie nicht verstehen und deren Verluste sie nicht tragen können. Die EU hat mit MiFID II geantwortet, in Kraft seit Januar 2018. Ihr Zweck ist der Anlegerschutz: Eine Bank darf nur Produkte empfehlen, die zum Kunden passen.

Dieser Schutz bedeutet Arbeit. Vor jeder Empfehlung muss der Bankberater:

- Kenntnisse und Erfahrungen, finanzielle Verhältnisse inklusive Verlusttragfähigkeit sowie Anlageziele inklusive Risikobereitschaft des Kunden erfassen
- die Nachhaltigkeitspräferenzen abfragen, Pflicht seit August 2022
- jedes Produkt gegen dieses Profil prüfen
- dem Kunden eine Geeignetheitserklärung aushändigen, die begründet, warum das Produkt passt
- die Beratung aufzeichnen, damit sie später überprüfbar ist

Vieles davon ist Handarbeit: Daten aus mehreren Systemen zusammensuchen, Profile mit Produktdaten abgleichen, Dokumentation schreiben. Und die Fallhöhe ist groß. Eine falsche Prüfung ist kein Flüchtigkeitsfehler, sondern ein Compliance-Verstoß.

***Die Idee***

Generative KI liest, fasst zusammen und formuliert in Sekunden. Für eine regulierte Entscheidung ist sie allein aber nicht verlässlich genug. Deshalb teilt dieser Prototyp die Arbeit auf:

Regeln entscheiden, das Sprachmodell erklärt, jeder Schritt wird protokolliert, der Bankberater entscheidet.

Der Bankberater gibt nur einen Namen oder eine Kundennummer ein. Der Agent sucht das Profil, prüft alle Produkte nach festen Regeln und liefert bis zu drei Vorschläge. Zu jedem Vorschlag zeigt er Risikoklasse und Nachhaltigkeit neben den Werten des Kunden, und er begründet, warum jedes andere Produkt aussortiert wurde.

***So funktioniert es***

Der Agent basiert auf smolagents und nutzt drei Werkzeuge:

| Werkzeug | Was es tut |
|---|---|
| `kunde_suchen` | Sucht das Anlageprofil nach Name oder Kundennummer |
| `geeignetheit_pruefen` | Prüft alle Produkte nach festen Regeln und nennt für jedes aussortierte Produkt den Grund |
| `vorschlaege_auswaehlen` | Schließt Produkte mit negativer Hausmeinung aus und wählt bis zu drei Vorschläge |

Beispiel: Für Lena Berger sind 4 von 15 Produkten geeignet. Eines davon ist eine nachhaltige Aktie mit negativer Hausmeinung und wird deshalb nicht vorgeschlagen. Der Bankberater bekommt 3 Vorschläge und für alles Aussortierte einen Grund.

***Designentscheidungen***

***Die Entscheidung liegt im Code, nicht im Modell.*** Die Regeln stehen im Körper der Werkzeuge. Das Modell liest nur den Kopf (Name, Parameter, Docstring), wählt die Werkzeuge und formuliert die Antwort. Am Ergebnis kann es nichts ändern.

***Gleiche Skalen bei Kunde und Produkt.*** Risiko 1–7, Anlagehorizont in Jahren, Kenntnisse 1–3, Verlustrisiko 1–3, Nachhaltigkeit 0–2. Ohne gemeinsame Skala gibt es kein verlässliches Mapping.

***Zwei Stufen.*** Stufe A ist die Geeignetheitsprüfung, die MiFID II verlangt. Stufe B ist eine Vorgabe der Bank, die Hausmeinung aus dem Research. Die Hausmeinung ist ein Datenfeld, keine Prognose der KI.

***Datenminimierung.*** Die Kundendatenbank enthält nur die Felder, die für die Prüfung nötig sind. Kein Geschlecht, keine Adresse.

***Nachvollziehbarkeit.*** Jeder Werkzeugaufruf landet mit Zeit, Eingabe und Ergebnis im Audit-Log (audit_log.json).

***Human-in-the-Loop.*** Der Agent schlägt vor, der Bankberater entscheidet und gibt frei.

***Starten***
Notebook über den Colab-Button öffnen
Token unter huggingface.co/settings/tokens anlegen (fine grained, Make calls to Inference Providers)
In Colab: Schlüssel-Symbol links, Secret HF_TOKEN anlegen, Notebook-Zugriff einschalten
Zellen von oben nach unten ausführen und in Zelle 5 einen Namen wählen: „Lena Berger“, „Markus Weber“ oder „Jonas Schmitt“

Kein Token? Dann laufen dieselben Werkzeuge ohne Sprachmodell, die Entscheidung ist identisch.

***Grenzen***
Fiktive Kunden und Produkte, stark vereinfachte Regeln
Keine Anbindung an echte Systeme, kein Login
Keine Evaluation der Antworten des Modells
Ein Prototyp, der das Prinzip zeigt, keine Rechtsberatung

***Stack***
Python, smolagents, SQLite, pandas, Qwen2.5-Coder-32B-Instruct über die Hugging Face Inference API.
