# 🥖 PPKI Bakery – Firebase version (with teacher PIN)
 
The second option for the bread system. It looks and works like the Google Sheets version, but stores data in **Firebase Firestore**, which is faster and updates every device live. Some screens are locked behind a **4-digit teacher PIN**.
 
| | Google Sheets version | Firebase version |
|---|---|---|
| Speed | About 1–3 s per action | Usually well under 1 s |
| Live updates across devices | No (refresh on action) | Yes, instantly |
| Overselling protection | Lock in script | Atomic transactions |
| View data in a spreadsheet | Yes | No (Firebase console only) |
| Teacher PIN | No | Yes |
| Pauses when unused | No | No |
 
## What the PIN locks
 
| Tab | Who can open it |
|-----|-----------------|
| **Sell** | Everyone (students) |
| **Add bread** (stock, prices, remove stock) | Teacher PIN |
| **Sales today** (sales list, totals, cancel sale) | Teacher PIN |
| **Report** (sales graph) | Teacher PIN |
 
- Tapping a locked tab opens a PIN keypad. Phones use the on-screen keypad. Laptops can also type the digits.
- After unlocking, a **🔒 Lock** button appears at the top. Tap it to lock again.
- It locks automatically after **10 minutes** without touching the screen, and a reload locks it too.
## Setup (about 15 minutes)
 
You need a Google account. No credit card is needed for the free **Spark** plan.
 
1. **Create a project.** Go to [console.firebase.google.com](https://console.firebase.google.com), click **Add project**, name it (e.g. `ppki-bakery`), and turn Google Analytics **off**.
2. **Create the database.** Open **Build → Firestore Database → Create database**. Choose a nearby location such as **asia-southeast1 (Singapore)**. Start in **production mode**.
3. **Set the rules.** In Firestore, open the **Rules** tab, replace everything with the contents of `firestore.rules`, and click **Publish**. Without this step the app can't read or write.
4. **Get your config.** Click ⚙️ **Project settings → General → Your apps → Web (`</>`)**, register an app (skip Firebase Hosting), and copy the `firebaseConfig` values.
5. **Edit `index.html`.** Near the top of the script, paste your values into `firebaseConfig`, and set your PIN:
```js
   const firebaseConfig = { apiKey: "...", authDomain: "...", projectId: "...", appId: "..." };
   const ADMIN_PIN = "1234", LOCK_MINUTES = 10;
```
6. **Host it.** Put the file in your GitHub repo in a folder named `firebase` (so it opens at `https://YOUR-USERNAME.github.io/ppki-bakery/firebase/`). GitHub Pages serves it the same way as the first version.
7. **Test.** Open the page, tap **Add bread**, enter your PIN, add a test bread, then make a test sale on **Sell**.
## School logo
 
The page looks for `logo.jpeg` in the **repository root** (one level above the `firebase` folder), so the same logo file serves both versions. Until it's uploaded, no logo is shown.
 
## Everyday use
 
It works the same as the Sheets version (see the main README). The only difference is that the teacher taps a locked tab and enters the PIN first.
 
- **Change the PIN:** edit `ADMIN_PIN` in `index.html` and commit.
- **See raw data:** Firebase console → Firestore Database → `items` (stock per bread per day) and `sales` (one document per sale).
- **Wipe test data:** in the console, click the ⋮ next to the `items` collection → **Delete collection**, and do the same for `sales`.
## Free plan limits
 
The Spark plan includes about 50,000 reads and 20,000 writes per day and 1 GiB of storage, with no card needed. A class selling bread will use a small fraction of this. Check [firebase.google.com/pricing](https://firebase.google.com/pricing) if you ever expect heavy use.
 
## Limitations (read this)
 
- **The PIN is a screen lock, not real security.** It is checked in the browser, and the PIN is in the page's source code. It stops students from wandering into the teacher screens, but a technical person could bypass it.
- **The database rules are open.** Anyone who finds your site and knows how to use Firebase could read or write the data. That fits the "doesn't have to be secure" goal, but don't store anything sensitive.
- **No spreadsheet.** Use the Firebase console to view data. A "download sales as CSV" button can be added if you want one.
- **Daily stock only**, same as the Sheets version.