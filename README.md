# My planner

A personal daily to-do planner that runs as a single web page. Each working day is one row, and each task inside it has its own category, color, and start and end time.

It is built for one person's private use. The site is hosted on GitHub Pages, and tasks are stored in Firebase, so only the owner's Google account can open them.

## Features

- **One row per day.** Each row shows the day name and the date as day/month/year, with as many tasks inside it as needed.
- **Automatic dates.** "Add day" continues from the last row and only adds working days, Sunday to Thursday. After Thursday comes Sunday.
- **Catch-up button.** "Add days up to today" fills in every missing working day in one click.
- **Categories with colors.** Each task has a category dropdown, and the task row takes that category's color. Categories can be added, renamed, recolored, or deleted from "Edit categories".
- **Automatic stand-up.** Every Sunday, Tuesday, and Thursday row starts with a stand-up from 11:30 AM to 12:00 PM.
- **12-hour times.** Type a time loosely, such as `230` or `9`, and it becomes `2:30 PM` or `9:00 AM`.
- **Checkboxes.** Tick a task to mark it done.
- **Drag to move.** Drag a task by its handle to reorder it or move it to another day.
- **Export to Excel.** Downloads the whole planner as a colored `.xlsx` file.

## Default categories

| Category | Used for |
|---|---|
| Meeting | Team and planning meetings |
| Pair programming | Working sessions with a teammate |
| Learning | Tutorials, reading, and learning new things |
| Technical task | Hands-on development work |
| Stand-up | The recurring stand-up, added automatically |

## How it works

The whole app is one file, `index.html`, with no build step.

- **Hosting:** GitHub Pages serves the file.
- **Sign-in:** Firebase Authentication with Google.
- **Storage:** Cloud Firestore, with one document per day under the signed-in user.
- **Excel export:** the ExcelJS library, loaded only when the export button is clicked.

If the Firebase config in `index.html` is left empty, the planner runs in local mode and saves in the browser only.

## Privacy

The Firebase config inside `index.html` is not a secret. It only identifies the Firebase project, and every browser that opens the site receives it.

The tasks are protected by the Firestore security rules, which allow reading and writing only for the owner's Google account. Anyone else can reach the sign-in screen but cannot see or change any data.

## Using this for yourself

1. Copy `index.html` into your own repository.
2. Create a Firebase project on the free Spark plan.
3. Create a Firestore database in production mode.
4. Enable Google under Authentication > Sign-in method.
5. Register a web app in the project and replace the `firebaseConfig` values near the top of the script in `index.html` with your own.
6. Publish these Firestore rules, with your own email address:

   ```
   rules_version = '2';
   service cloud.firestore {
     match /databases/{database}/documents {
       match /users/{uid}/{document=**} {
         allow read, write: if request.auth != null
           && request.auth.uid == uid
           && request.auth.token.email == "YOUR_EMAIL@gmail.com";
       }
     }
   }
   ```

7. Enable GitHub Pages for the repository (Settings > Pages > Deploy from a branch).
8. Add your `username.github.io` address under Authentication > Settings > Authorized domains.

## Changing the defaults

These settings are constants near the top of the script in `index.html`:

| Constant | What it controls |
|---|---|
| `START_DATE` | The first day of an empty planner |
| `WORKDAYS` | Which days of the week get rows (0 is Sunday) |
| `STANDUP` | The stand-up's days, start time, end time, and text |
| `DEFAULT_CATEGORIES` | The categories and colors a new planner starts with |
