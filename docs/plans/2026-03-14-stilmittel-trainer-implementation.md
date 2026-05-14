# Stilmittel-Trainer — Implementierungsplan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Eine einzelne HTML-Datei, die 8 rhetorische Stilmittel mit Karteikarten, adaptivem Quiz (Balladen-Zitate) und Fortschritts-Dashboard vermittelt.

**Architecture:** Einzelne HTML-Datei mit eingebettetem CSS (responsive, Desktop + Tablet) und vanilla JavaScript. Kein Framework, keine externen Abhängigkeiten. Datenmodell komplett in JS-Objekten. Leitner-System mit 3 Boxen steuert die Quiz-Fragenauswahl.

**Tech Stack:** HTML5, CSS3 (Flexbox/Grid, CSS-Transitions für Kartenflip), Vanilla JavaScript (ES6+)

---

### Task 1: HTML-Grundgerüst + Tab-Navigation

**Files:**
- Create: `stilmittel-trainer.html`

**Step 1:** HTML-Datei erstellen mit:
- DOCTYPE, meta viewport, charset UTF-8
- Titel "Stilmittel-Trainer"
- Header mit App-Titel
- Tab-Navigation mit 3 Tabs: "Karteikarten", "Quiz", "Fortschritt"
- 3 Content-Sections (nur eine sichtbar je nach aktivem Tab)
- Basis-CSS: Reset, Schriftart, Farben, Tab-Styling
- JS: Tab-Umschaltung (click handler, active-class toggle, section show/hide)

**Step 2:** Im Browser testen — Tabs sollen umschaltbar sein, Inhalte wechseln.

---

### Task 2: Datenmodell — Stilmittel + Balladen-Zitate

**Files:**
- Modify: `stilmittel-trainer.html` (JS-Block)

**Step 1:** JavaScript-Datenobjekt anlegen mit:

```javascript
const stilmittel = [
  {
    name: "Alliteration",
    definition: "Wiederholung des Anfangsbuchstabens benachbarter Wörter",
    beispielKarte: "Milch macht müde. (Alltagsbeispiel)",
    quizFragen: [
      {
        zitat: "Wer wagt es, Rittersmann oder Knapp...",
        hervorhebung: "Wer wagt es",
        ballade: "Der Handschuh",
        autor: "Schiller",
        erklaerung: "Die Wörter 'Wer' und 'wagt' beginnen mit dem gleichen Buchstaben."
      },
      // 2-3 weitere Zitate
    ]
  },
  // ... alle 8 Stilmittel
];
```

Pro Stilmittel: 3-4 Quiz-Fragen mit echten Balladen-Zitaten.

**Step 2:** Leitner-State-Objekt anlegen:

```javascript
const leitnerState = {};
stilmittel.forEach(s => {
  leitnerState[s.name] = { box: 1, richtig: 0, falsch: 0 };
});
```

---

### Task 3: Karteikarten-Modus

**Files:**
- Modify: `stilmittel-trainer.html` (CSS + JS)

**Step 1:** CSS für Flip-Karten:
- Card-Container mit perspective
- Vorder-/Rückseite mit backface-visibility: hidden
- Flip-Animation via CSS transform rotateY(180deg)
- Karte mindestens 300x200px, zentriert

**Step 2:** JS-Logik:
- Aktuelle Karte tracken (Index 0-7)
- Klick/Tap auf Karte: togglet "flipped" CSS-Klasse
- Vor/Zurück-Buttons: wechseln den Index, zeigen neue Karte (ungeflippt)
- Karteninhalt dynamisch aus `stilmittel`-Array rendern

**Step 3:** Im Browser testen — Karten flippen, Navigation funktioniert.

---

### Task 4: Quiz-Modus — Grundlogik

**Files:**
- Modify: `stilmittel-trainer.html` (HTML + CSS + JS)

**Step 1:** Quiz-UI aufbauen:
- Zitat-Anzeige (groß, zentriert) mit hervorgehobenem Stilmittel-Teil
- Quellenangabe darunter (Ballade, Autor)
- 4 Antwort-Buttons (Multiple Choice)
- Feedback-Bereich (richtig/falsch + Erklärung)
- "Nächste Frage"-Button

**Step 2:** Quiz-Logik:
- Funktion `pickNextQuestion()`: wählt zufällig ein Stilmittel + zufälliges Zitat daraus
- Funktion `generateOptions(correctAnswer)`: gibt 4 Optionen zurück (1 richtig + 3 zufällige andere)
- Antwort-Handler: prüft ob richtig, zeigt Feedback, wartet auf "Nächste Frage"

**Step 3:** Im Browser testen — Quiz-Fragen erscheinen, Antworten funktionieren.

---

### Task 5: Adaptives Quiz (Leitner-System)

**Files:**
- Modify: `stilmittel-trainer.html` (JS)

**Step 1:** `pickNextQuestion()` adaptiv machen:
- Gewichtung basierend auf Box: Box 1 = Gewicht 4, Box 2 = Gewicht 2, Box 3 = Gewicht 1
- Gewichtete Zufallsauswahl: Stilmittel in Box 1 kommen ~4x häufiger dran als Box 3
- Bei richtiger Antwort: Box hochstufen (max 3)
- Bei falscher Antwort: zurück auf Box 1
- `richtig`/`falsch`-Counter aktualisieren

**Step 2:** Im Browser testen — nach mehreren Antworten prüfen, ob schwache Stilmittel häufiger kommen.

---

### Task 6: Fortschritts-Dashboard

**Files:**
- Modify: `stilmittel-trainer.html` (HTML + CSS + JS)

**Step 1:** Dashboard-UI:
- Grid/Liste aller 8 Stilmittel
- Pro Stilmittel: Name + Ampel-Punkt (rot/gelb/grün) + "X richtig / Y falsch"
- Gesamtübersicht oben: "Gesamt: X/Y richtig (Z%)"
- "Sitzung zurücksetzen"-Button

**Step 2:** JS-Logik:
- Funktion `updateDashboard()`: liest `leitnerState` und rendert die Anzeige
- Wird aufgerufen bei jedem Tab-Wechsel zum Dashboard
- Ampelfarben: Box 1 = rot (#e74c3c), Box 2 = gelb (#f39c12), Box 3 = grün (#27ae60)
- Reset-Button: setzt alle Boxen und Counter zurück

**Step 3:** Im Browser testen — nach Quiz-Runden Dashboard prüfen.

---

### Task 7: Visuelles Feintuning + Responsive

**Files:**
- Modify: `stilmittel-trainer.html` (CSS)

**Step 1:** Design aufpolieren:
- Warme Farbpalette (Header, Buttons, Hintergrund)
- Schriftgrößen optimieren (Titel, Kartentext, Buttons)
- Touch-Targets mindestens 44px
- Hover-Effekte für Desktop, Touch-Feedback für Tablet
- Media Queries: Layout-Anpassungen für Tablet (< 768px)
- Smooth Transitions bei Tab-Wechsel

**Step 2:** Im Browser auf verschiedenen Breiten testen (Desktop + Tablet-Simulation).

---

## Reihenfolge & Abhängigkeiten

```
Task 1 (Grundgerüst)
  └→ Task 2 (Datenmodell)
       ├→ Task 3 (Karteikarten)
       ├→ Task 4 (Quiz-Grundlogik)
       │    └→ Task 5 (Leitner-System)
       └→ Task 6 (Dashboard)
            └→ Task 7 (Feintuning)
```

Tasks 3, 4 und 6 können nach Task 2 parallel gebaut werden. Task 5 braucht Task 4, Task 7 kommt zuletzt.
