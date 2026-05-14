# Latein-Dativ-Trainer — Design-Dokument

## Zusammenfassung

Interaktive Lern-App (einzelne HTML-Datei) für eine 13-jährige Schülerin, um die **Substantiv-Deklination im Dativ** zu lernen — o-Deklination, a-Deklination, konsonantische Deklination, Singular und Plural. Vier Übungsmodi (Karteikarten, Quick Quiz, Endung tippen, Tabellen-Puzzle) plus ein Mix-Modus, alle mit Leitner-Wiederholung. Schwerpunkt Dativ, alle fünf Kasus im Code vorbereitet (zukunftsfähig für die nächsten Schulthemen).

## Zielgruppe & Nutzung

- Schülerin, 13 Jahre (etwa Klasse 7–8)
- Einsatz auf Computer und Tablet (Browser)
- Eine einzelne HTML-Datei, kein Server, keine externen Abhängigkeiten
- Konsistent zum etablierten Pattern (`stilmittel-trainer.html`, `my-day-time-trainer.html`)

## Lerninhalt

### 3 Deklinationen × 5 Kasus × 2 Numeri

| Numerus | Kasus | o-Dekl. | a-Dekl. | kons. Dekl. |
|---------|-------|---------|---------|-------------|
| Sg. | Nom. | -us | -a | — (Stamm) |
| Sg. | Gen. | -ī | -ae | -is |
| Sg. | **Dat.** | **-ō** | **-ae** | **-ī** |
| Sg. | Akk. | -um | -am | -em |
| Sg. | Abl. | -ō | -ā | -e |
| Pl. | Nom. | -ī | -ae | -ēs |
| Pl. | Gen. | -ōrum | -ārum | -um |
| Pl. | **Dat.** | **-īs** | **-īs** | **-ibus** |
| Pl. | Akk. | -ōs | -ās | -ēs |
| Pl. | Abl. | -īs | -īs | -ibus |

**Default-Filter beim Start:** Dativ Sg. + Pl. aktiv. Andere Kasus per Toggle zuschaltbar.

### Wortschatz (Starter-Pool: ~60 Substantive)

20 klassische Schulvokabeln pro Deklination. Jedes Wort mit deutscher Übersetzung, Genus und Stamm. Vokabel-Liste als JSON-Konstante am Anfang des Scripts → leicht erweiterbar / anpassbar an das verwendete Lehrbuch.

**o-Deklination (Maskulina):**
servus (Sklave), dominus (Herr), amicus (Freund), puer (Junge), ager (Acker), magister (Lehrer), filius (Sohn), deus (Gott), populus (Volk), equus (Pferd), vir (Mann), liber (Buch), discipulus (Schüler), medicus (Arzt), nuntius (Bote), legatus (Gesandter), gladius (Schwert), hortus (Garten), animus (Geist), Romanus (Römer)

**a-Deklination (Feminina):**
filia (Tochter), puella (Mädchen), rosa (Rose), terra (Erde/Land), via (Weg), aqua (Wasser), vita (Leben), regina (Königin), schola (Schule), fabula (Geschichte), amica (Freundin), dea (Göttin), silva (Wald), patria (Heimat), domina (Herrin), ancilla (Magd), familia (Familie), gloria (Ruhm), victoria (Sieg), ira (Zorn)

**Konsonantische Deklination:**
pater (Vater, m.), mater (Mutter, f.), frater (Bruder, m.), soror (Schwester, f.), rex (König, m.), homo (Mensch, m.), miles (Soldat, m.), virgo (junge Frau, f.), lex (Gesetz, f.), corpus (Körper, n.), senator (Senator, m.), consul (Konsul, m.), dux (Anführer, m.), imperator (Feldherr, m.), urbs (Stadt, f.), nox (Nacht, f.), pax (Frieden, f.), mors (Tod, f.), pes (Fuß, m.), caput (Kopf, n.)

**Hinweis zur Liste:** Diese Auswahl ist neutral gegenüber Lehrbüchern. Die Tochter arbeitet mit **Pontes (Klett-Verlag)** — die exakte Pontes-Vokabelliste wird zu einem späteren Zeitpunkt nachgereicht und ersetzt diesen Starter-Pool. Daher ist die Vokabel-Konstante so strukturiert, dass ein Austausch ohne Code-Änderung am Rest der App möglich ist (nur das `vocabulary`-Array am Anfang des Scripts wird ersetzt).

### Optionale Erweiterung (Toggle)

**Adjektive und Possessivpronomina der o-/a-Deklination.** Laut Bildhinweis bilden sie den Dativ wie die Substantive — daher nahezu gratis zuschaltbar:

- Adjektive: bonus/-a/-um, magnus/-a/-um, parvus/-a/-um, longus/-a/-um
- Possessivpronomina: meus/-a/-um, tuus/-a/-um, suus/-a/-um, noster/-tra/-trum

Default: aus. Per Checkbox im Setup zuschaltbar.

## Architektur

- **Format:** Single-file HTML mit eingebettetem CSS und JavaScript
- **Tech:** Vanilla HTML/CSS/JS, kein Framework
- **Storage:** `localStorage` für Leitner-Status, Setup-Präferenzen, Statistik
- **Primärgerät:** iPad — siehe iPad-Spezifika weiter unten
- **Responsive:** Desktop, Tablet, optional Phone (Tap-Targets ≥ 44px)

### iPad-Spezifika (verbindlich für die Implementierung)

- `<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">`
- Alle Texteingaben mit `autocomplete="off"`, `autocorrect="off"`, `autocapitalize="off"`, `spellcheck="false"` — verhindert, dass iOS aus "patri" "Patrick" macht
- **Längezeichen-Toleranz:** Eingabe `o` und `ō` (sowie `a`/`ā`, `i`/`ī`, `e`/`ē`, `u`/`ū`) gleichermaßen akzeptieren. Anzeige immer mit Längezeichen, damit das Auge sie sieht
- **Drag-and-Drop** im Tabellen-Puzzle: HTML5 Pointer Events (nicht nur mousedown), damit Touch-Drag funktioniert. Alternative Implementierung: Tap-Pool-Element → Tap-Ziel-Zelle (kein eigentliches Ziehen, robuster)
- `touch-action: manipulation` auf interaktiven Elementen → unterdrückt 300 ms Doppel-Tap-Zoom-Delay
- Ausreichend Bottom-Padding im Content, damit Bildschirmtastatur nicht das aktive Eingabefeld verdeckt; alternativ Eingabefeld bei Fokus in den sichtbaren Bereich scrollen
- Keine Hover-only-Interaktionen — alles muss per Tap zugänglich sein
- Im Safari getestet (iPad-Standard-Browser), idealerweise auch im Chrome iOS

### Struktur: Startscreen-Layout

Hauptmenü mit großen Modus-Buttons (entsprechend `my-day-time-trainer`-Pattern):

```
┌─────────────────────────────────────────┐
│             DATIVVS · Latina            │
│                                         │
│  [Setup-Leiste: Deklinationen / Kasus]  │
│                                         │
│   📜 Karteikarten     ⚡ Quick Quiz      │
│   ✏️ Endung tippen    🧩 Tabellen-Puzzle│
│                                         │
│   🎯 Mix-Modus (alle Modi gemischt)     │
│                                         │
│   📊 Fortschritt: 23/80 Formen sitzen   │
└─────────────────────────────────────────┘
```

Pro Modus: Vollbild-Ansicht mit "Zurück zum Menü"-Button oben links.

### Setup-Leiste (Startscreen)

Persistente Einstellungen, oben auf dem Startscreen sichtbar:

- **Deklinationen:** o ✓ · a ✓ · kons. ✓ (Checkboxen, mindestens eine muss aktiv sein)
- **Kasus:** Nom · Gen · **Dat ✓** · Akk · Abl (Default: nur Dat aktiv)
- **Numerus:** Sg ✓ · Pl ✓
- **+ Adjektive/Possessivpronomina** (Checkbox, Default: aus)
- **Reset Fortschritt** (kleiner Link unten)

Auswahl persistiert in `localStorage`.

## Module

### 1. 📜 Karteikarten (passives Lernen)

- Karten der aktiven Auswahl frei durchblätterbar (vor/zurück, Shuffle)
- **Vorderseite:** Deutsche Wendung mit Artikel (z. B. "dem Sklaven", "den Vätern")
- **Rückseite (Tap-Flip):** Lateinische Form (z. B. "servō"), Hinweis-Tag (z. B. "Dat. Sg. · o-Dekl.")
- Buttons: "Sitzt" (Box +1) / "Nochmal" (Box → 1) — optional, Leitner-Update läuft auch nur durch Quiz-Modi
- Keine "richtig/falsch"-Bewertung, rein zum Anschauen

### 2. ⚡ Quick Quiz (Multiple-Choice Speed-Drill)

- Eine Form-Aufgabe, 4 Antwortmöglichkeiten
- Aufgaben-Varianten gemischt:
  - "Dativ Singular von **filia**?" → 4 Lateinformen
  - "Welcher Kasus/Numerus ist **patribus**?" → 4 Kombinationen
- **Plausible Distraktoren:** Nachbarformen derselben Deklination (filiae/filiam/filia/filiīs)
- Sofort-Feedback: grün/orange + kurze Erklärung
- 10 Aufgaben pro Session, Endbildschirm mit Score und Leitner-Aufstiegen

### 3. ✏️ Endung tippen (aktive Produktion)

- Substantiv (Grundform) + gewünschter Kasus + Numerus
- Schülerin tippt **nur die Endung** in ein Eingabefeld
- Tolerant: Längezeichen optional (`o` und `ō` beide ok), case-insensitive
- Bei Fehlversuch: Hinweis ("Endung der o-Deklination Dat. Sg.?")
- Nach 2 Fehlern: korrekte Endung anzeigen, weiterblättern
- 10 Aufgaben pro Session

### 4. 🧩 Tabellen-Puzzle (Drag & Drop)

- Leere oder teilbefüllte Deklinationstabelle (eine Deklination auf einmal)
- Pool von Endungen unten — passende per Drag-and-Drop in die richtige Zelle ziehen
- Schwierigkeitsstufen:
  - **Level 1:** nur Dativ Sg.+Pl. leer, Rest ausgefüllt
  - **Level 2:** Dativ + zwei zufällige weitere Kasus leer
  - **Level 3:** alle 10 Zellen leer
- Korrekte Zelle: grün, falsche: orange (Endung springt zurück in den Pool)
- Touch-Drag funktioniert auf Tablet

### 5. 🎯 Mix-Modus (kombiniert)

- 10 Aufgaben zufällig aus Modi 2 (Quick Quiz) und 3 (Endung tippen)
- Karteikarten und Tabellen-Puzzle bewusst ausgenommen: Karteikarten ist kein Aufgaben-Format, Tabellen-Puzzle dauert pro Runde zu lange für ein gemischtes Set
- Adaptives Sampling: Formen aus Leitner-Box 1 häufiger, Box 5 selten

## Adaptives System (Leitner)

5 Boxen pro Form (Form = Wort × Kasus × Numerus, also z. B. "filia · Dat · Sg" → "filiae"):

| Box | Status | Wiederholung |
|-----|--------|--------------|
| 1 | Neu / unsicher | Jede Session |
| 2 | 1× richtig | Jede 2. Session |
| 3 | 2× richtig | Jede 4. Session |
| 4 | 3× richtig | Jede 8. Session |
| 5 | Gemeistert | Selten |

- Richtige Antwort (Quick Quiz, Endung tippen, Mix) → Box +1
- Falsche Antwort → zurück auf Box 1
- Karteikarten-Modus modifiziert Boxen nur über die optionalen "Sitzt"/"Nochmal"-Buttons
- Tabellen-Puzzle: alle Endungen einer Tabelle korrekt → alle beteiligten Formen +1 Box

## Feedback & Motivation

- **Pro Aufgabe:**
  - Korrekt: grüner Rahmen + kurzes "Recte!" / "Optime!" / "Egregie!" (Latein-Augenzwinkern)
  - Falsch: orange + Erklärung der korrekten Form
- **Session-Ende:**
  - Score (z. B. "8/10 richtig")
  - Aufgestiegene Formen ("3 Formen sind eine Box weiter!")
  - Fortschritts-Balken: wie viel Prozent der aktiven Auswahl ist in Box 4–5
- Kein "Streak", keine Punkte-Gamification — bewusst nüchtern, da 13 Jahre

## Visuelles Design — Mediterran modern

### Farbpalette

| Element | Farbe | Hex |
|---------|-------|-----|
| Hintergrund | Terrakotta-Beige hell | `#fef7ed` |
| Hintergrund-Gradient bis | warmes Apricot | `#fde4cf` |
| Akzent Karteikarten | Terrakotta | `#c75d36` |
| Akzent Quiz | Olive | `#5a8a3a` |
| Akzent Endung tippen | Senf | `#d4a017` |
| Akzent Puzzle | Meerblau | `#2e6b8a` |
| Text | dunkles Sienna | `#7a3d2a` |
| Erfolg / korrekt | Olive | `#5a8a3a` |
| Fehler / falsch | warmes Rot | `#c75d36` |

### Typographie

- Überschriften: Georgia / Cormorant Garamond (Serif, klassisch)
- Latein-Formen: Georgia (Serif), eindeutig hervorgehoben
- UI / Buttons: System-Sans (Segoe UI / system-ui)
- Mindestgröße 16 px, Latein-Formen 22–26 px

### Decorative Elemente

- Modi-Buttons: weiße Karten mit farbigem linken Rand (4 px), dezenter Box-Shadow
- Titel: "DATIVVS" in Versalien, gesperrt — kleine Anspielung auf antike Inschriften
- Keine Mascots, keine Sticker — bewusst sachlich
- Optionale Säulen-Silhouette als Header-Element (SVG)

### Responsive

- iPad Landscape (Hauptzielgerät): 2×2 Grid für die vier Modi + breite Mix-Karte unten, komfortabel auf einen Bildschirm
- iPad Portrait: 2×2 bleibt, schmaler
- Desktop: identisch zu iPad Landscape, max-width-Container zentriert
- Phone: 1-spaltige Stapel

## localStorage Schema

```js
{
  "dativ-trainer:setup": {
    declensions: ["o", "a", "kons"],
    cases: ["dat"],
    numbers: ["sg", "pl"],
    includeAdjectivesPronouns: false
  },
  "dativ-trainer:leitner": {
    "servus|dat|sg": { box: 3, lastSeen: 1715600000000 },
    "filia|dat|sg": { box: 1, lastSeen: 1715600000000 },
    // ...
  },
  "dativ-trainer:stats": {
    sessionsTotal: 12,
    answersCorrect: 87,
    answersWrong: 23
  }
}
```

## Technisches Vorgehen — Bausteine

1. **Daten-Layer:** JSON-Konstanten für Vokabular und Endungstabelle, generische `decline(word, case, num)`-Funktion
2. **Leitner-Layer:** Auswahl der nächsten Aufgabe nach Box-Verteilung, Persistierung
3. **Setup-Layer:** Auswahl im Startscreen, Filter über das Datenset
4. **Modul-Layer:** vier Modi + Mix-Modus, alle als unabhängige Renderer mit gleichem Aufgaben-Interface
5. **UI-Layer:** Theme-CSS (Mediterran modern), Komponenten (Karte, Button, Eingabefeld, Drag-Slot)
6. **Feedback-Layer:** korrekt/falsch-Animation, Session-Bilanz

## Out-of-Scope (bewusst weggelassen)

- Akkusativ/Genitiv/Ablativ als aktiver Default — nur als zuschaltbare Erweiterung
- Übersetzungs-Modus Deutsch → Latein (kommt evtl. später)
- Mini-Satz-Modus mit kompletten lateinischen Sätzen (anspruchsvoller, andere Lernzielebene)
- Dativ-Funktion erkennen im Satz (zu komplex für diese Iteration)
- Spaced-Repetition mit Zeitintervallen (Leitner reicht)
- Account/Cloud-Sync, Multi-User
