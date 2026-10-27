# PRD v2: NutriTrack – Precision Nutrition & Calorie Tracker

## 1. Summary
**User**: Male, 5'10", 90 kg → 80 kg goal (10 kg loss). 8-hour workday, 26 km daily commute, 1 hour of weightlifting.
**Problem**: Holding a steady 500 kcal deficit without hidden oils, liquid calories, unplanned snacks, or muscle loss from low protein. Re-typing the same foods every day makes tracking tedious, so people quit.
**Solution**: A zero-backend, zero-setup web app that logs food in seconds, remembers every custom food, and shows remaining calories and macros live.

## 2. Targets (unchanged)
| | Target | Safe range |
|---|---|---|
| Calories | 2,100 kcal | 2,000–2,150 |
| Protein | 145 g | 140–150 |
| Carbs | 230 g | 220–240 |
| Fats | 60 g | 55–65 |

BMR ≈ 1,850–1,900 kcal, TDEE ≈ 2,550–2,650 kcal, deficit 500 kcal/day ≈ 0.45–0.5 kg/week.

## 3. Functional Requirements

### 3.1 Food library (new)
1. Built-in staples: whey, eggs, egg whites, paneer, chicken, tofu, rice, dal, roti, oats, peanut butter, oil/ghee, banana, curd, milk.
2. **Saved foods**: choosing "➕ Add a new food…" in the dropdown reveals a form (name, serving description, kcal, protein, carbs, fats). With "Save to my foods" ticked, the food is stored permanently.
3. **Dropdown**: saved foods appear in their own group at the top, above the staples. Each option shows name, serving, and kcal. The newest saved food is listed first.
4. Quantity is a multiplier of the serving (0.5, 1, 2.5…), so "1 scoop" or "100 g" is defined once.
5. Saved foods can be deleted from "My saved foods". Past log entries keep their own snapshot of values and never change.

### 3.2 Logging
* Meals: Breakfast, Lunch, Pre/Post Workout, Dinner, Snacks (default chosen from the time of day).
* Live preview of what an entry adds before it is saved.
* Day navigation with a date picker, so past days can be viewed or corrected.
* Entries are grouped by meal with per-meal kcal subtotals, and can be removed individually.

### 3.3 Calculation engine
* Remaining = 2,100 − consumed. Surplus = max(0, consumed − 2,100).
* Macro deltas shown as "left" or "over" for protein, carbs, and fats.

### 3.4 Status states
| State | Condition | Display |
|---|---|---|
| Green | ≤ 1,950 kcal | On track, kcal still available |
| Amber | 1,950–2,150 kcal | Target zone |
| Red | > 2,150 kcal | Warning banner and surplus amount |
* Protein banner appears when protein ≥ 145 g.

## 4. Design
Nutrition theme: leaf-green primary, a calorie ring as the main visual, and macro colors that match their role (protein = tomato orange, carbs = golden, fats = violet). The ring turns amber and red with the status state. Light and dark mode follow the device setting.

## 5. Technical Architecture
* **Single `index.html`**: HTML + CSS + vanilla JS. No build step, no framework, no CDN, no dependencies.
* **Storage**: `localStorage` key `cutting_tracker_state_v1`, one JSON object: `{ foods: [...], logs: { "YYYY-MM-DD": [...] } }`. It is loaded with validation on start, and every write is wrapped in try/catch with a visible error if the browser blocks storage.
* **Backup**: JSON export/import (restores foods and logs) and CSV export of all logged days.
* **Hosting**: Static site on Vercel. Put `index.html` in a folder and deploy it (steps below). No environment variables or config.
* **Privacy**: Zero network requests. Data never leaves the browser.

## 6. Deploy on Vercel
1. Create a folder with `index.html` at the root.
2. Either drag the folder onto vercel.com/new, or run `npx vercel --prod` inside it.
3. Alternative: push to GitHub, import the repo in Vercel, and keep the framework preset as "Other" with no build command.

## 7. Known limits and next steps
* `localStorage` is per browser and per device, so phone and laptop will not sync. Use JSON export/import to move data, or add Vercel KV or Supabase later if sync is needed.
* Edit-in-place for saved foods and a weekly weight and trend view are the natural next features.



https://script.google.com/macros/s/AKfycbyEWAKE71lIA5Gdk4DcYXsEQtdzCsOGJvRi-qVzKt_8OK59DaUo2mPFUWG3r2uRuPbr3Q/exec