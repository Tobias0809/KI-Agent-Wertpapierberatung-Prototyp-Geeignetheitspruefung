Prototyp einer technischen Komponente für den Case „KI in der Wertpapierberatung“. Ein Agent auf Basis von smolagents prüft für einen Kunden, welche Anlageprodukte geeignet sind, und schlägt dem Bankberater bis zu drei Produkte vor.

Prinzip

Regeln entscheiden, das Sprachmodell erklärt, jeder Schritt wird protokolliert, der Bankberater entscheidet.

Der Bankberater nennt nur Namen oder Kundennummer. Der Agent ruft drei Werkzeuge auf:

Werkzeug	Was es tut
kunde_suchen	Sucht das Anlageprofil nach Name oder Kundennummer
geeignetheit_pruefen	Prüft alle Produkte nach festen Regeln und nennt für jedes aussortierte Produkt den Grund
vorschlaege_auswaehlen	Schließt Produkte mit negativer Hausmeinung aus und wählt bis zu drei Vorschläge
Designentscheidungen

Die Entscheidung liegt im Code, nicht im Modell. Die Regeln stehen im Körper der Werkzeuge. Das Modell liest nur den Kopf (Name, Parameter, Docstring), wählt die Werkzeuge und formuliert die Antwort.

Gleiche Skalen bei Kunde und Produkt. Risiko 1–7, Anlagehorizont in Jahren, Kenntnisse 1–3, Verlustrisiko 1–3, Nachhaltigkeit 0–2. Nur so ist das Mapping eindeutig.

Zwei Stufen. Stufe A ist die Geeignetheit nach MiFID II (Pflicht). Stufe B ist eine Vorgabe der Bank (Hausmeinung). Die Hausmeinung ist ein Datenfeld aus dem Research, keine Prognose der KI.

Datenminimierung. Die Kundendatenbank enthält nur Felder, die für die Prüfung nötig sind.

Nachvollziehbarkeit. Jeder Werkzeugaufruf landet mit Zeit, Eingabe und Ergebnis im Audit-Log (audit_log.json). Risikoklasse und Nachhaltigkeit werden dem Bankberater immer neben den Werten des Kunden angezeigt.

Starten
Notebook über den Colab-Button öffnen
Token unter huggingface.co/settings/tokens anlegen (fine grained, Make calls to Inference Providers)
In Colab: Schlüssel-Symbol links, Secret HF_TOKEN anlegen, Notebook-Zugriff einschalten
Zellen von oben nach unten ausführen, in Zelle 5 einen Namen wählen: „Lena Berger“, „Markus Weber“ oder „Jonas Schmitt“

Ohne Token laufen dieselben Werkzeuge ohne Sprachmodell. Die Entscheidung ist identisch.

Grenzen
Fiktive Daten, stark vereinfachte Regeln
Keine Anbindung an echte Systeme, kein Login
Keine Evaluation der Antworten des Modells
Stack

Python, smolagents, SQLite, pandas, Qwen2.5-Coder-32B-Instruct über die Hugging Face Inference API.
