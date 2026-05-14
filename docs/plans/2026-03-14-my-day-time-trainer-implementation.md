# My Day — English Time Trainer: Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Single-file HTML learning app to practice English time-telling and daily routines for a 9-year-old, with Leitner-based spaced repetition, automatic difficulty progression, and a girly pink/flower design.

**Architecture:** Single HTML file with embedded CSS and vanilla JavaScript. SVG for analog clocks, CSS for decorative elements (flowers, confetti, stars). Leitner system persisted in localStorage. No frameworks, no external dependencies.

**Tech Stack:** HTML5, CSS3 (Flexbox/Grid, CSS animations, SVG), Vanilla JavaScript (ES6+)

---

### Task 1: HTML-Grundgerüst + Startseite + Navigation

**Files:**
- Create: `my-day-time-trainer.html`

**Step 1:** HTML-Datei erstellen mit:
- DOCTYPE, meta viewport, charset UTF-8
- Titel "My Day — English Time Trainer"
- CSS-Variablen für Farbpalette:
  ```css
  :root {
    --bg-pink: #FFE4EC;
    --pink: #FF9BC1;
    --lilac: #C9A2D9;
    --mint: #A8E6CF;
    --yellow: #FFF3B0;
    --text: #4A3728;
    --gold: #FFD700;
    --correct: #81C784;
    --wrong: #FFB74D;
  }
  ```
- Basis-CSS: Reset, Schriftart (system-ui, min 18px), responsive Layout
- Rosa Hintergrund mit CSS-Blumenmuster (repeating radial-gradient)
- Header mit App-Titel
- Startseite mit 4 großen runden Buttons (SVG-Icons):
  1. "What time is it?" (Uhr-Icon, pink)
  2. "My Day" (Stift-Icon, lila)
  3. "Time Quiz" (Stern-Icon, mint)
  4. "Mix it up!" (Regenbogen-Icon, gelb)
- Fortschrittsbalken unter den Buttons
- Screens: `#screen-home`, `#screen-clock`, `#screen-myday`, `#screen-quiz`, `#screen-summary`
- JS: Navigation zwischen Screens (show/hide), Zurück-Button auf jeder Seite

**Step 2:** Im Browser testen — Startseite sichtbar, Buttons wechseln Screens, Zurück-Navigation funktioniert.

---

### Task 2: SVG-Uhr-Komponente

**Files:**
- Modify: `my-day-time-trainer.html` (CSS + JS)

**Step 1:** JS-Funktion `createClockSVG(hours, minutes, size)`:
- SVG-Element erstellen (viewBox 0 0 200 200)
- Rundes Zifferblatt mit rosa Rand (#FF9BC1, stroke-width 4)
- Weiße Füllung
- Blumen-Marker als kleine SVG-Pfade an 12, 3, 6, 9
- Einfache Striche an den anderen Positionen
- Stundenzeiger: lila (#C9A2D9), kürzer und dicker
- Minutenzeiger: rosa (#FF9BC1), länger und dünner
- Kleiner Kreis in der Mitte
- Zeigerpositionen berechnen:
  ```javascript
  function createClockSVG(hours, minutes, size = 200) {
    const hourAngle = ((hours % 12) + minutes / 60) * 30 - 90;
    const minuteAngle = minutes * 6 - 90;
    // ... SVG als String zusammenbauen
  }
  ```
- Rückgabe als SVG-String (innerHTML-kompatibel)

**Step 2:** Test-Render auf der Startseite — verschiedene Uhrzeiten anzeigen und visuell prüfen.

---

### Task 3: Datenmodell + Leitner-System + localStorage

**Files:**
- Modify: `my-day-time-trainer.html` (JS)

**Step 1:** Aufgaben-Pool definieren:

```javascript
// Time tasks pool - generated dynamically based on difficulty level
function generateTimeTasks(level) {
  const tasks = [];
  for (let h = 1; h <= 12; h++) {
    tasks.push({ hours: h, minutes: 0 }); // Level 1: full hours
    if (level >= 2) {
      tasks.push({ hours: h, minutes: 30 }); // Level 2: half hours
    }
    if (level >= 3) {
      tasks.push({ hours: h, minutes: 15 }); // Level 3: quarter hours
      tasks.push({ hours: h, minutes: 45 });
    }
  }
  return tasks;
}

// Time-to-English conversion
function timeToEnglish(hours, minutes) {
  if (minutes === 0) return `It's ${numberWord(hours)} o'clock`;
  if (minutes === 15) return `It's a quarter past ${numberWord(hours)}`;
  if (minutes === 30) return `It's half past ${numberWord(hours)}`;
  if (minutes === 45) return `It's a quarter to ${numberWord(hours % 12 + 1)}`;
  // ... 5-minute steps for future levels
}

// Activities word bank
const activities = [
  { en: "have breakfast", timeOfDay: "morning" },
  { en: "go to school", timeOfDay: "morning" },
  { en: "have lunch", timeOfDay: "afternoon" },
  { en: "do my homework", timeOfDay: "afternoon" },
  { en: "watch TV", timeOfDay: "evening" },
  { en: "read a book", timeOfDay: "evening" },
  { en: "go to bed", timeOfDay: "evening" },
  { en: "go home", timeOfDay: "afternoon" }
];

// Daily routine templates
const dayTemplates = [ /* 4-5 variations */ ];
```

**Step 2:** Leitner-System implementieren:

```javascript
const LEITNER = {
  load() {
    return JSON.parse(localStorage.getItem('myDayTrainer') || 'null') || {
      cards: {},        // taskId → { box: 1, lastSeen: null }
      level: 1,         // current difficulty
      streak: 0,        // consecutive correct
      sessionCount: 0,  // total sessions played
      stats: { correct: 0, wrong: 0 }
    };
  },
  save(state) {
    localStorage.setItem('myDayTrainer', JSON.stringify(state));
  },
  promote(state, taskId) {
    const card = state.cards[taskId] || { box: 1 };
    card.box = Math.min(card.box + 1, 5);
    card.lastSeen = Date.now();
    state.cards[taskId] = card;
  },
  demote(state, taskId) {
    const card = state.cards[taskId] || { box: 1 };
    card.box = 1;
    card.lastSeen = Date.now();
    state.cards[taskId] = card;
  },
  pickTasks(state, pool, count = 10) {
    // Weighted selection: box 1 = weight 16, box 2 = 8, box 3 = 4, box 4 = 2, box 5 = 1
    // Also factor in session count for spaced repetition
  }
};
```

**Step 3:** Im Browser testen — `LEITNER.load()` und `.save()` in der Konsole prüfen.

---

### Task 4: Modul „What time is it?" — Uhr ablesen

**Files:**
- Modify: `my-day-time-trainer.html` (HTML + CSS + JS)

**Step 1:** UI aufbauen in `#screen-clock`:
- SVG-Uhr (groß, zentriert, mittels `createClockSVG`)
- Textfeld darunter: `<input type="text" placeholder="It's ...">`
- "Check"-Button
- Feedback-Bereich (versteckt bis Antwort)
- Fortschrittsanzeige: „Frage 3 von 10"
- Attempts-Counter (versteckt, max 2)

**Step 2:** Eingabe-Validierung:

```javascript
function normalizeTimeAnswer(input) {
  let answer = input.trim().toLowerCase();
  answer = answer.replace(/^it'?s\s+/, '');  // Strip "It's"
  // Replace digit words: "3" → "three"
  // Accept both: "half past three" and "half past 3"
  return answer;
}

function checkTimeAnswer(input, hours, minutes) {
  const normalized = normalizeTimeAnswer(input);
  const correct = timeToEnglish(hours, minutes)
    .toLowerCase().replace(/^it'?s\s+/, '');
  return normalized === correct;
}
```

**Step 3:** Session-Logik:
- 10 Aufgaben pro Session (via `LEITNER.pickTasks`)
- Pro Frage: max 2 Versuche
- Versuch 1 falsch → oranger Hinweis + Tipp (z.B. „Look at the short hand!")
- Versuch 2 falsch → richtige Antwort anzeigen
- Richtig → grüner Rahmen + Stern-Animation + Ermutigung
- Ergebnisse sammeln für Summary

**Step 4:** Im Browser testen — komplette Clock-Session durchspielen.

---

### Task 5: Modul „My Day" — Lückentext

**Files:**
- Modify: `my-day-time-trainer.html` (HTML + CSS + JS)

**Step 1:** Tagesablauf-Templates definieren:

```javascript
const dayTemplates = [
  {
    sentences: [
      { text: "Today is {weekday}.", gaps: ["weekday"] },
      { text: "At {time} in the morning I {activity}.",
        gaps: ["time", "activity"],
        constraints: { timeOfDay: "morning", timeRange: [6,9] } },
      { text: "At {time} I {activity}.",
        gaps: ["time", "activity"],
        constraints: { timeOfDay: "morning", timeRange: [7,9] } },
      { text: "At {time} in the afternoon I {activity}.",
        gaps: ["time", "activity"],
        constraints: { timeOfDay: "afternoon", timeRange: [12,15] } },
      { text: "At {time} in the evening I {activity}.",
        gaps: ["time", "activity"],
        constraints: { timeOfDay: "evening", timeRange: [17,20] } },
      { text: "At {time} I {activity}.",
        gaps: ["time", "activity"],
        constraints: { timeOfDay: "evening", timeRange: [20,22] } }
    ]
  }
  // ... 3-4 more variations
];
```

**Step 2:** UI aufbauen in `#screen-myday`:
- Sätze untereinander, Lücken als `<select>` Dropdowns
- Uhrzeiten-Dropdown: Optionen basierend auf aktuellem Level + timeRange-Constraint
- Aktivitäten-Dropdown: gefiltert nach timeOfDay-Constraint + Distraktoren
- Weekday-Dropdown: Monday–Sunday
- „Check my day!"-Button unten

**Step 3:** Validierung:
- Pro Lücke: richtig → grün, falsch → orange mit korrekter Antwort
- Logik-Check: Aktivität muss zur Tageszeit passen
- Leitner-Update pro korrekt/falsch beantwortete Lücke

**Step 4:** Im Browser testen — Lückentext ausfüllen und Feedback prüfen.

---

### Task 6: Modul „Time Quiz" — Multiple Choice

**Files:**
- Modify: `my-day-time-trainer.html` (HTML + CSS + JS)

**Step 1:** UI aufbauen in `#screen-quiz`:
- Fragebereich: entweder SVG-Uhr ODER Text-Frage
- 4 Antwort-Buttons (entweder Text oder kleine SVG-Uhren)
- Fortschrittsanzeige: „Frage 5 von 10"

**Step 2:** Frage-Generierung:

```javascript
function generateQuizQuestion(task, allTasks) {
  const isClockToText = Math.random() > 0.5;

  if (isClockToText) {
    // Show clock, pick correct text + 3 distractors
    const correct = timeToEnglish(task.hours, task.minutes);
    const distractors = pickDistractors(task, allTasks, 3);
    return { type: 'clockToText', clock: task, options: shuffle([correct, ...distractors]) };
  } else {
    // Show text, pick correct clock + 3 distractor clocks
    const text = timeToEnglish(task.hours, task.minutes);
    const distractors = pickDistractorClocks(task, allTasks, 3);
    return { type: 'textToClock', text, options: shuffle([task, ...distractors]) };
  }
}

function pickDistractors(task, allTasks, count) {
  // Prefer plausible: same hour different minutes, or same minutes different hour
}
```

**Step 3:** Antwort-Handling:
- Richtig → Button grün + Stern-Animation
- Falsch → gewählter Button orange, richtiger blinkt grün
- 1.5 Sekunden Pause, dann automatisch nächste Frage
- Leitner-Update

**Step 4:** Im Browser testen — Quiz-Session mit beiden Fragetypen durchspielen.

---

### Task 7: Mix-Modus + Session-Summary

**Files:**
- Modify: `my-day-time-trainer.html` (HTML + CSS + JS)

**Step 1:** Mix-Modus:
- Zufällige Auswahl aus allen drei Modulen (gewichtet durch Leitner)
- 10 Aufgaben: ca. 3-4 Clock, 1-2 MyDay, 3-4 Quiz (zufällig gemischt)
- Gleiche UI-Komponenten, nur abwechselnd eingeblendet

**Step 2:** Session-Summary in `#screen-summary`:
- Großer Stern/Pokal oben
- „Du hast X von Y richtig!" (deutsch, da die App-Rahmensprache deutsch ist)
- Liste: welche Aufgaben richtig/falsch (mit grünen/orangen Punkten)
- Box-Aufstiege hervorheben: „⬆ 3 Aufgaben aufgestiegen!"
- Motivationsspruch (zufällig aus Pool):
  - "Great job! You're becoming a time expert!"
  - "Wonderful! Keep it up!"
  - "Amazing progress!"
- Buttons: „Nochmal spielen" / „Zurück zur Startseite"

**Step 3:** Schwierigkeits-Progression:
- Nach Session: Streak und Level prüfen
- 8 korrekt in Folge → Level hoch (max 3)
- 5+ falsch in Session → Level runter (min 1)
- Level-Wechsel in Summary anzeigen

**Step 4:** Im Browser testen — komplette Session mit Summary durchspielen.

---

### Task 8: SVG-Maskottchen + Animationen

**Files:**
- Modify: `my-day-time-trainer.html` (CSS + JS)

**Step 1:** SVG-Maskottchen (einfache Puppen-Figur):
- Rundes Gesicht, Zöpfe, Kleid (alles in Pastelltönen)
- 2 Zustände: winkend (Startseite), lächelnd (richtiges Feedback)
- Als inline-SVG, ca. 80x120px
- Positionierung: rechts unten auf der Startseite, neben Feedback bei Fragen

**Step 2:** Animationen:
- Stern-Animation bei richtiger Antwort (CSS @keyframes: scale + rotate + fade)
- Konfetti-Effekt bei Session-Ende (CSS-basiert, 20-30 fallende bunte Rechtecke)
- Sanftes Pulsieren der Buttons auf der Startseite
- Smooth Screen-Übergänge (opacity transition)

**Step 3:** Blumen-Deko:
- CSS-Blumen an den Ecken der Karten-Buttons (::before/::after pseudo-elements)
- Subtiles Blumenmuster im Hintergrund (CSS radial-gradient)

**Step 4:** Im Browser testen — alle Animationen und Deko-Elemente visuell prüfen.

---

### Task 9: Fortschrittsanzeige auf Startseite + localStorage-Reset

**Files:**
- Modify: `my-day-time-trainer.html` (HTML + CSS + JS)

**Step 1:** Fortschrittsbalken auf Startseite:
- Gesamtfortschritt: Anteil Aufgaben in Box 3+ / alle gesehenen Aufgaben
- Visueller Balken (rosa → gold Gradient)
- Text: „Du hast schon X Aufgaben gemeistert!"
- Aktuelles Level anzeigen: „Level 1: Volle Stunden"

**Step 2:** Kleines Zahnrad-Icon unten rechts:
- Öffnet Mini-Menü: „Fortschritt zurücksetzen" mit Bestätigungs-Dialog
- Reset löscht localStorage komplett

**Step 3:** Im Browser testen — Fortschritt wird korrekt angezeigt und aktualisiert sich nach Sessions.

---

### Task 10: Responsive Feintuning + Touch-Optimierung

**Files:**
- Modify: `my-day-time-trainer.html` (CSS)

**Step 1:** Responsive Design:
- Mobile (< 480px): Buttons vertikal gestapelt, Uhr max 180px
- Tablet (480–768px): 2x2 Grid für Startbuttons, Uhr 200px
- Desktop (> 768px): 4er-Reihe, Uhr 240px
- Media Queries für alle Breakpoints

**Step 2:** Touch-Optimierung:
- Alle Buttons min 48px Höhe
- Input-Felder min 44px Höhe, font-size 18px (verhindert iOS-Zoom)
- Dropdown-Selects groß genug für Finger-Tap
- Kein Hover-Abhängigkeit (nur als Enhancement)

**Step 3:** Im Browser testen — Desktop, iPad-Simulation, Handy-Simulation im DevTools.

---

## Reihenfolge & Abhängigkeiten

```
Task 1 (Grundgerüst + Startseite)
  └→ Task 2 (SVG-Uhr-Komponente)
       └→ Task 3 (Datenmodell + Leitner + localStorage)
            ├→ Task 4 (Modul: Clock Reading)
            ├→ Task 5 (Modul: My Day Lückentext)
            ├→ Task 6 (Modul: Time Quiz)
            │    └→ Task 7 (Mix-Modus + Summary)
            └→ Task 9 (Fortschritt + Reset)
       └→ Task 8 (Maskottchen + Animationen)
            └→ Task 10 (Responsive + Touch)
```

Tasks 4, 5, 6 können nach Task 3 parallel gebaut werden. Task 7 braucht 4+5+6. Tasks 8–10 sind unabhängig voneinander nach ihren Vorgängern.
