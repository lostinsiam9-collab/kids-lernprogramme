# My Day — English Time Trainer: Design Document

## Overview

Single-file HTML learning app for a 9-year-old girl (4th grade) to practice telling time and describing daily routines in English. Girly design with pink tones, flowers, and doll-like mascot figures.

## Architecture

- **Format:** Single-file HTML (like existing stilmittel-trainer.html)
- **Tech:** Vanilla HTML/CSS/JS, SVG for clocks, CSS for decorative elements
- **Storage:** localStorage for Leitner system persistence

## Modules

### 1. What time is it? — Clock Reading

- Analog clock rendered as SVG with random times
- Child types the time in English (free text input)
- Input tolerance: case-insensitive, with/without "It's", numbers and words accepted
- After 2 wrong attempts, correct answer is shown
- 10 tasks per session

### 2. My Day — Gap-Fill Text

- Pre-generated daily routine sentences with blanks
- Three types of gaps:
  - Times (dropdown)
  - Time of day (dropdown: in the morning / afternoon / evening)
  - Activities (dropdown from word bank: have breakfast, go to school, have lunch, do my homework, watch TV, read a book, go to bed, go home)
- Logic check: activities must match sensible times of day
- "Check my day!" button with per-gap feedback
- Multiple daily routine variations

### 3. Time Quiz — Multiple Choice

- Two question types alternating:
  - Clock shown → pick correct text from 4 options
  - Text shown → pick correct clock from 4 options
- Plausible distractors (e.g., "half past" vs "quarter past" same hour)
- 10 questions per session

### 4. Mix it up! — Combined Mode

- Random mix of tasks from all three modules
- 10 tasks per session
- Same Leitner tracking

## Difficulty Levels (automatic progression)

- **Level 1:** Full hours — "It's seven o'clock"
- **Level 2:** Half hours — "It's half past seven"
- **Level 3:** Quarter hours — "It's a quarter past / to seven"

### Progression Rules

- Level up: 8 correct answers in a row at current level
- Level down: 5 of 10 wrong in a session

## Leitner System

5 boxes:

| Box | Status | Frequency |
|-----|--------|-----------|
| 1 | New/difficult | Every session |
| 2 | 1x correct | Every 2nd session |
| 3 | 2x correct | Every 4th session |
| 4 | 3x correct | Every 8th session |
| 5 | Mastered | Rare refresh |

- Correct → move up one box
- Wrong → back to box 1
- Each task has a unique ID, tracked across all modules

### localStorage Schema

- Tasks with box number and last practice date
- Current difficulty level
- Total statistics (tasks practiced, mastered, streak)

## Feedback System

- **Immediate per question:**
  - Correct → green border + star animation + encouragement ("Well done!", "Great job!")
  - Wrong → orange highlight + hint + "Try again!"
  - Clock module: after 2 wrong attempts, show correct answer
- **Session summary:**
  - Correct/wrong count
  - Box promotions ("Aufgestiegen!")
  - Motivational message

## Visual Design

### Color Palette

| Element | Color | Hex |
|---------|-------|-----|
| Background | Soft pink | #FFE4EC |
| Card/Button 1 | Pink | #FF9BC1 |
| Card/Button 2 | Lilac | #C9A2D9 |
| Card/Button 3 | Mint green | #A8E6CF |
| Card/Button 4 | Light yellow | #FFF3B0 |
| Text | Dark brown | #4A3728 |
| Accent/Stars | Gold | #FFD700 |

### Decorative Elements (pure CSS/SVG)

- Subtle flower pattern background (CSS)
- Flower icons on card corners
- SVG doll/girl mascot: waves on start screen, smiles on correct answers
- Star animation on correct answers
- Confetti effect on session completion

### Clock Design

- Round face with pink border
- Flower markers at 12, 3, 6, 9
- Pastel hands: hour = lilac, minute = pink

### Typography

- System font stack: 'Segoe UI', system-ui, -apple-system, sans-serif
- Minimum 18px font size for readability
- Round, friendly appearance

### Responsive

- Works on iPad, desktop, and phone
- Touch-optimized buttons (min 48px tap targets)

## Start Screen Layout

Title: "My Day — English Time Trainer"

Four large round buttons:
1. What time is it? (clock icon)
2. My Day (pencil icon)
3. Time Quiz (target icon)
4. Mix it up! (rainbow icon)

Progress bar below showing Leitner overall progress.

Mascot figure on the side.
