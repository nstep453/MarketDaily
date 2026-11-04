[README (2).md](https://github.com/user-attachments/files/34349804/README.2.md)

# Market Daily

A daily five-question investing quiz, in the spirit of Wordle. Everyone gets the same five questions each day, answers against the clock, and shares a spoiler-free emoji grid with friends.

The questions cover market history, personal finance, fundamentals, technical analysis, and risk management. Every answer comes with a short explanation, so the point is learning something, not just scoring.

## How to play

1. Open the app and tap **Play**.
2. Answer five multiple-choice questions. After each one you see whether you were right and a short explanation.
3. Finish to see your score and result grid, then tap **Share result** to copy it.
4. Come back tomorrow. A countdown on the results screen shows when the next puzzle unlocks.

You can play each day's puzzle once.

### Scoring

| Item | Points |
| --- | --- |
| Correct answer | 100 |
| Speed bonus | Up to +50, losing 2.5 points per second (gone after 20 seconds) |
| Wrong answer | 0 |
| **Max per day** | **750** |

### Share format

```
Market Daily #12
🟩🟩🟥🟩🟩
640/750 · 🔥4
```

The grid shows which answers you got right (🟩) or wrong (🟥) without revealing the questions. The 🔥 number is your current daily streak.

## Features

- **Daily puzzle:** the same five questions for everyone on a given date, numbered from #1 (October 17, 2026).
- **Question bank:** 70 questions, served five at a time, so the cycle repeats every 14 days.
- **Streak tracking:** consecutive days played, shown at the top right.
- **Shareable results:** one tap copies your score card to the clipboard. If clipboard access is blocked, a text box appears so you can copy it manually.
- **Leaderboard:** Today and All-time views, showing the top 25 players, with your row highlighted.
- **Nicknames:** pick a nickname of up to 16 characters for the leaderboard. Please don't use your real name.
- **Light and dark mode:** follows the system setting automatically.
- **Mobile friendly:** responsive layout with safe-area padding for phones.

## How it works

Market Daily is a single self-contained HTML file with no build step and no dependencies. All of the HTML, CSS, JavaScript, and question data live in one file.

### Daily puzzle selection

The puzzle number is the number of days since the launch date (October 17, 2026). Today's five questions are picked from the bank by position:

```
index = ((puzzleNumber - 1) * 5 + i) mod questionCount     for i = 0..4
```

The date comes from the player's device clock in their local time zone, so the puzzle rolls over at local midnight.

### Data storage

| What | Where | Details |
| --- | --- | --- |
| Scores, streak, nickname | `localStorage` key `marketdaily1` | Per device and browser. Clearing site data resets your history and lets you replay today's puzzle. |
| Leaderboard | Shared database (`scores/{userId}`) | One document per player: `name`, `total`, `streak`, `lastDate`, `lastScore`. |

If the shared database isn't available (for example, when the page is opened outside its hosted environment), the app still works. Your scores are saved on your device only, and the leaderboard shows a notice saying so. Every storage call is wrapped in `try/catch`, so a blocked or empty store never breaks the game.

### Leaderboard

The leaderboard subscribes to the `scores` collection, ordered by all-time total with a limit of 100 documents. The Today tab filters to players whose last played date is today and sorts by that day's score. Scores are pushed to the database when you finish a puzzle and again, debounced, when you change your nickname. Players who lack write permission are skipped silently.

## Hosting

Because it's one static file, you can host it anywhere that serves HTML. The shared leaderboard only works where the page can reach the `window.claude` runtime with the `user` and `db` capabilities, such as a published Claude artifact. Elsewhere it runs in local-only mode.

## Adding or editing questions

Questions are stored in the `Q` array near the top of the script. Each entry has this shape:

```js
{
  c: "MARCH 2020",                         // category label shown above the question
  q: "Question text?",                     // the question
  o: ["Option A", "Option B", "Option C"], // exactly three answer choices
  a: 1,                                    // index of the correct option (0-based)
  e: "Explanation shown after answering."  // short explanation
}
```

Tips:

- Keep three options per question; the layout and scoring assume it.
- Vary the position of the correct answer so players can't guess by pattern.
- Check any dates and figures against a reliable source before adding them.
- Changing the number of questions changes which questions land on which date, so edit the bank before launch where possible.
- To change the launch date, edit `Date.UTC(2026,9,17)` in the puzzle-number calculation (months are zero-indexed, so `9` is October).

## Limitations

- The daily puzzle depends on the device's date and time, so changing the system clock changes the puzzle.
- Nothing stops a player from clearing site data to replay a day, and leaderboard scores are not verified, so this is a casual game rather than a competitive one.
- The questions are educational and not financial advice. Historical figures are approximate.

## Credits

Built as a lightweight, ad-free daily habit for learning how markets work.
