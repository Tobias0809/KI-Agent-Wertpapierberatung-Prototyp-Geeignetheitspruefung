# Regeln entscheiden. KI erklärt. – KI-gestützte Geeignetheitsprüfung nach MiFID II

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/drive/1uOTTDFCrw7_NipCBTdkGfLezjrKnCcP7)

## Das Problem

Die Finanzkrise 2008 hat gezeigt, was passiert, wenn Anlegern Produkte verkauft werden, die sie nicht verstehen und deren Verluste sie nicht tragen können. Die EU hat mit MiFID II geantwortet, in Kraft seit Januar 2018. Ihr Zweck ist der Anlegerschutz: Eine Bank darf nur Produkte empfehlen, die zum Kunden passen.

Dieser Schutz bedeutet Arbeit. Vor jeder Empfehlung muss der Bankberater:

- Kenntnisse und Erfahrungen, finanzielle Verhältnisse inklusive Verlusttragfähigkeit sowie Anlageziele inklusive Risikobereitschaft des Kunden erfassen
- die Nachhaltigkeitspräferenzen abfragen, Pflicht seit August 2022
- jedes Produkt gegen dieses Profil prüfen
- dem Kunden eine Geeignetheitserklärung aushändigen, die begründet, warum das Produkt passt
- die Beratung aufzeichnen, damit sie später überprüfbar ist

Vieles davon ist Handarbeit: Daten aus mehreren Systemen zusammensuchen, Profile mit Produktdaten abgleichen, Dokumentation schreiben. Und die Fallhöhe ist groß. Eine falsche Prüfung ist kein Flüchtigkeitsfehler, sondern ein Compliance-Verstoß.

## Die Idee

Generative KI liest, fasst zusammen und formuliert in Sekunden. Für eine regulierte Entscheidung ist sie allein aber nicht verlässlich genug. Deshalb teilt dieser Prototyp die Arbeit auf:

**Regeln entscheiden, das Sprachmodell erklärt, jeder Schritt wird protokolliert, der Bankberater entscheidet.**

Der Bankberater gibt nur einen Namen oder eine Kundennummer ein. Der Prototyp sucht das Profil, prüft alle Produkte nach festen Regeln und liefert bis zu drei Vorschläge. Zu jedem Vorschlag zeigt er Risikoklasse und Nachhaltigkeit neben den Werten des Kunden, und er begründet, warum jedes andere Produkt aussortiert wurde.

## So funktioniert es

Drei Schritte, bewusst getrennt:

| Schritt | Was passiert |
|---|---|
| Kunde suchen | Anlageprofil nach Name oder Kundennummer |
| Geeignetheit prüfen | Feste Regeln prüfen alle Produkte und nennen für jedes aussortierte Produkt den Grund |
| Vorschläge auswählen | Produkte mit negativer Hausmeinung raus, bis zu drei Vorschläge |

Danach formuliert ein kleines Open-Weight-Sprachmodell (Qwen2.5-Instruct) aus dem fertigen Ergebnis eine kurze Begründung für den Bankberater. Ein Regel-Check prüft den Text, bevor er angezeigt wird.

Beispiel: Für Lena Berger sind 4 von 15 Produkten geeignet. Eines davon ist eine nachhaltige Aktie mit negativer Hausmeinung und wird deshalb nicht vorgeschlagen. Der Bankberater bekommt 3 Vorschläge und für alles Aussortierte einen Grund.

## Designentscheidungen

- **Die Entscheidung liegt im Code, nicht im Modell:** Das Sprachmodell bekommt nur das fertige Ergebnis und formuliert die Begründung. Am Ergebnis kann es nichts ändern.
- **Regel-Check auf die KI-Ausgabe:** Bevor die Begründung angezeigt wird, prüft eine feste Regel den Text. Alle Vorschläge müssen genannt sein, die Risikostufe muss stimmen, Aussagen wie „nicht geeignet“ oder Renditeangaben sind verboten. Fällt der Text durch, wird er verworfen und ein Textbaustein aus den Regeln genutzt. Im Test hat das kleine Modell zum Beispiel einem Kunden mit niedriger Risikobereitschaft eine hohe zugeschrieben. Der Regel-Check fängt genau solche Halluzinationen ab.
- **Modell in der eigenen Umgebung:** Das Sprachmodell läuft direkt im Notebook, ohne externen Modellanbieter.
- **Gleiche Skalen bei Kunde und Produkt:** Risiko 1–7, Anlagehorizont in Jahren, Kenntnisse 1–3, Verlustrisiko 1–3, Nachhaltigkeit 0–2. Ohne gemeinsame Skala gibt es kein verlässliches Mapping.
- **Zwei Stufen:** Stufe A ist die Geeignetheitsprüfung, die MiFID II verlangt. Stufe B ist eine Vorgabe der Bank, die Hausmeinung aus dem Research. Die Hausmeinung ist ein Datenfeld, keine Prognose der KI.
- **Datenminimierung:** Die Kundendatenbank enthält nur die Felder, die für die Prüfung nötig sind. Kein Geschlecht, keine Adresse.
- **Nachvollziehbarkeit:** Jeder Schritt landet mit Zeit, Eingabe und Ergebnis im Audit-Log (`audit_log.json`).
- **Human-in-the-Loop:** Der Prototyp schlägt vor, der Bankberater entscheidet und gibt frei.

## Starten

1. Notebook über den Colab-Button öffnen
2. Optional schneller: Laufzeit → Laufzeittyp ändern → T4-GPU
3. Zellen von oben nach unten ausführen und in Zelle 6 einen Namen wählen: „Lena Berger“, „Markus Weber“ oder „Jonas Schmitt“

Kein Token nötig. Lädt das Sprachmodell nicht, kommt die Begründung aus einem Textbaustein, die Entscheidung ist identisch.

## Grenzen

- Fiktive Kunden und Produkte, stark vereinfachte Regeln
- Keine Anbindung an echte Systeme, kein Login
- Der Regel-Check ist einfach gehalten, eine systematische Evaluation der Modellantworten fehlt
- Ein Prototyp, der das Prinzip zeigt, keine Rechtsberatung

## Stack

Python, SQLite, pandas, transformers, Qwen2.5-Instruct (Open-Weight-Modell)
