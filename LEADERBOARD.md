# Online leaderboard

Out of the box, Scooper keeps each device's top 10 runs on that device. To share one leaderboard between everyone who plays, connect a free Firebase project. It takes about five minutes and needs a Google account.

## Set it up

1. Go to <https://console.firebase.google.com>, sign in, and create a project (for example "Scooper"). You can turn Google Analytics off.
2. In the project, open **Build → Firestore Database** and click **Create database**. Pick a location near your players and start in **production mode**.
3. Open the **Rules** tab, replace everything there with the contents of [`firestore.rules`](firestore.rules), and click **Publish**.
4. Open **Project settings** (the gear icon). Under **Your apps**, add a **Web** app (the `</>` icon). Give it any nickname and skip Hosting. Firebase then shows a config snippet: copy its `projectId` and `apiKey`.
5. In `index.html`, fill those two values into `LEADERBOARD` near the top of the script and push to `main`:

   ```js
   const LEADERBOARD={
     firebaseProject:'scooper-12ab3',
     apiKey:'AIza...',
     top:25
   };
   ```

The project ID and API key are meant to be public. What anyone can write is decided by the rules from step 3, not by the key.

## How it works

- Each device makes a random ID on its first visit and owns one row in the `scores` collection. After a run that beats its best, the game posts that run with the player's name. Players set their name on the game-over screen or in the trophy panel.
- The rules allow anyone to read the board. A row can only be created with a name (1 to 12 characters), a score, the stack height, the multiplier and the game version, and a row's score can only go up. Rows cannot be deleted from the game.
- If a post fails (for example while offline), the game tries again on the next game over or visit.

## Looking after it

- **Remove a row:** Firestore Database → Data → `scores` → pick the row → delete.
- **Reset the board:** delete the `scores` collection.
- **Free limits:** the free plan allows 50,000 reads and 20,000 writes a day. Opening the leaderboard costs one read per row shown (25 by default), so that is about 2,000 opens a day.
- **Cheating:** like any leaderboard in a browser game, someone determined can post a made-up score straight to the database. The rules keep entries to a sane shape, and you can delete anything odd by hand.
