# Verdant — Plant Care Reminder

A single-file web application that generates timely reminders for plant-care
activities: **watering, fertilizing and pruning**. It runs entirely in the
browser — no backend, no build step, no dependencies.

## What it does

- **Tracks a garden.** Each plant carries its own care schedule with an interval
  (in days) and a last-done timestamp for watering, fertilizing and pruning.
- **Computes due dates deterministically.** Next-due = last-done + interval.
  Tasks are classified as *overdue*, *due today*, *soon* (within 2 days) or
  *on track*, with a progress bar showing how far through each interval the
  plant is.
- **Runs a reminder engine.** A scanner sweeps the whole garden every 30 seconds
  (and once on load). Any task that has come due or gone overdue is announced —
  **once per task per day**, so it never spams you.
- **Dispatches notifications two ways:**
  - **In-app toasts** — always available, no permission needed.
  - **Web Notifications** — native browser notifications via the Notification
    API, once you grant permission.
- **Keeps a reminder feed** — a running log of every reminder generated, with a
  timestamp, so you can see the engine working.

## Using it

1. Open the site.
2. Click **Add plant**, or **Load sample garden** to populate five plants with
   staggered schedules so reminders trigger immediately.
3. Click **Enable browser notifications** to allow native notifications.
4. Use the **Water / Fertilize / Prune** buttons on any plant to log care — the
   schedule resets and the reminder re-arms for the next interval.
5. **Check now** forces an immediate scan.

All data is stored in the browser's `localStorage`; nothing is sent anywhere.

## How the reminder mechanism works

```
last-done + interval  ──►  next-due
next-due − today      ──►  daysUntil

daysUntil  < 0  →  overdue   (warn)
daysUntil == 0  →  due today (announce)
daysUntil  > 0  →  wait
```

The engine keeps a `plantId:taskType → YYYY-MM-DD` map of what it has already
announced, guaranteeing at most one reminder per task per day. Marking a task
done deletes its entry so the next interval can announce afresh.

## Files

| File | Purpose |
|------|---------|
| `index.html` | The entire application — markup, styles and the reminder engine. |
| `README.md` | This document. |

## Notes

- Web Notifications require a secure context (`https://` or `localhost`).
  GitHub Pages serves over HTTPS, so notifications work there.
- Everything degrades gracefully: if notifications are blocked or unsupported,
  in-app toasts and the reminder feed still work.

## License

MIT.
