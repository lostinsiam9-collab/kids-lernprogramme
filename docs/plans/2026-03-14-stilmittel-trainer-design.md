# Stilmittel-Trainer — Design-Dokument

## Zusammenfassung

Interaktive Lern-App (einzelne HTML-Datei) für eine 13-jährige Schülerin (Klasse 7), um rhetorische Stilmittel anhand von Balladen-Zitaten zu lernen und zu üben. Adaptives Quiz-System nach dem Leitner-Prinzip.

## Zielgruppe & Nutzung

- Schülerin, 13 Jahre, 7. Klasse
- Einsatz auf Computer und Tablet (Browser)
- Eine einzelne HTML-Datei, kein Server nötig

## Umfang: 8 Stilmittel

1. Alliteration
2. Anapher
3. Ellipse
4. Hyperbel
5. Personifikation
6. Vergleich
7. Metapher
8. Wiederholung

## Architektur

Einzelne HTML-Datei mit eingebettetem CSS und JavaScript. Kein Framework, keine externen Abhängigkeiten. Responsive Design für Desktop und Tablet.

### Drei Hauptbereiche (Tab-Navigation)

### 1. Karteikarten (Lernbereich)

- Alle 8 Stilmittel frei durchblätterbar (vor/zurück)
- Vorderseite: Name des Stilmittels
- Rückseite (Klick/Tap-Flip): Definition + Balladen-Beispiel
- Beliebige Reihenfolge

### 2. Quiz (Übungsbereich)

- Balladen-Zitat mit Quellenangabe (Ballade, Autor)
- Stilmittel im Zitat farblich hervorgehoben
- 4 Antwortmöglichkeiten (Multiple Choice)
- Nach Antwort: Erklärung warum es dieses Stilmittel ist
- Adaptives System wählt die nächste Frage

### 3. Fortschritt (Dashboard)

- Alle 8 Stilmittel mit Ampel-Status:
  - Rot = unsicher (Box 1)
  - Gelb = wird besser (Box 2)
  - Grün = sitzt (Box 3)
- Gesamtpunktestand der Sitzung
- Anzahl richtig/falsch pro Stilmittel

## Adaptives System (Leitner-Prinzip)

- 3 Boxen pro Stilmittel
- Start: alle in Box 1 (kommt häufig dran)
- Richtige Antwort: wandert eine Box hoch (seltener)
- Falsche Antwort: zurück auf Box 1 (häufiger)
- Box-Level bestimmt Ampel-Status im Fortschritt

## Balladen-Quellen

Zitate aus dem Kanon Klasse 7:

- Der Erlkönig (Goethe)
- Der Handschuh (Schiller)
- Die Bürgschaft (Schiller)
- John Maynard (Fontane)
- Der Zauberlehrling (Goethe)

Pro Stilmittel mindestens 3-4 verschiedene Zitate.

## Visuelles Design

- Klar, modern, freundlich (nicht "schulisch")
- Warme Farben
- Große Schrift
- Touch-freundliche Buttons (min. 44px)
- Responsive: Desktop und Tablet gleich gut nutzbar
