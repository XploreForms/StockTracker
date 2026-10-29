# Stock Tracker

A mobile web app for field technicians to log every piece of equipment they receive, deploy, transfer, send on RMA or recycle. Each tech keeps their own record on their phone, backed up to their own Google Sheet. At quarterly counts, that record shows where every unit went.

**Live site:** https://xploreforms.github.io/StockTracker/

## Why it exists

Quarterly inventory counts come up about 5% short on equipment such as routers, fibre drops and wireless radios. Most of that loss comes from provisioning failures, not lost gear. Stock Tracker gives each tech a dated record of every unit that passed through their hands. When inventory flags something missing, the tech can show what happened to it.

## How it works

- **No server, no database.** Everything is saved on the tech's phone, in the browser's storage for this site.
- **Google Sheets backup.** During setup each tech connects a Google account made with their @xplore.ca email. Every entry syncs to a spreadsheet in that tech's own Google Drive. On a new phone, connecting the same account brings everything back.
- **Works offline.** Once the app has been opened once, it loads without signal. Entries made offline sync when the phone is back online.
- **Install it like an app.** On iPhone, open the site in Safari and tap Share › Add to Home Screen. On Android, open it in Chrome and tap ⋮ › Add to Home screen or Install app.

## First-time setup (for techs)

1. **Your details.** Type your name, SAP dealer name, SAP account # (6 digits), Salesforce account # (7 digits) and shipping address. The app checks the format of each field but never fills them in for you.
2. **Google backup.** Connect the Google account made with your @xplore.ca email. The setup screen explains why this is required and walks you through creating the account if you don't have one. Connecting a personal account shows a warning.

## Tabs

### Logs
Logs every movement of stock. Each entry records the date and time and, when the phone allows it, GPS location.

| Action | What it records |
|---|---|
| **Deploy** | One item at a time to a customer account. The account stays filled in so you can add the rest of the job (radio, router, drop, NID). |
| **Receive** | Several items at once. Pick where they came from: Warehouse, Recovery (asks for the account), Transfer (asks for the dealer) or Other. |
| **Transfer** | Items going to another dealer, handed over in person or shipped (with an optional tracking number). Drafts an email to xninventory@xplore.ca, which the tech must send. |
| **RMA** | Items going back on RMA. An RMA number is required, and there's a link to the RMA Request Form. |
| **Recycle** | Items being recycled. The tech confirms they checked the inventory system first. |

Serials can be scanned with the camera, scanned from a photo, or typed. The app recognizes the item from the serial number in most cases. If you scan a MAC address barcode by mistake, it tells you to scan the serial instead.

### Stock
What's on hand right now: serialized units by item, plus quantities of non-serialized items. You can search it.

### History
Every entry, newest first, with search. Tap an entry to see its details or delete it. Deleting asks you to confirm first, and the deletion also syncs to your Google Sheet.

### Count
For quarterly counts.

1. **Upload Count List.** Pick the Excel count list from the inventory team.
2. **Scan everything** in your truck and storage. The app shows what's found, what's still missing, and anything you have that isn't on the list. Scanning a unit's MAC barcode also works for units on the list.
3. **Missing units** each get a comment box. If your history shows the unit was deployed, transferred, sent on RMA or recycled, the comment fills in automatically. Use **Have It** for units whose labels won't scan.
4. **Non-serialized items.** Type how many you counted. Matching counts are marked Y. Different counts are marked N and ask for a comment.
5. **Export Final Count to Excel.** Saves the inventory team's own list with Y/N, counts and comments filled in. Units not on the list go under Additional Units. The file is named `Count_<Dealer>_<SAP#>_<date>.xlsx`.

You can also count without a list, which compares your scans against your own records.

### Admin
- **Ordering & RMAs:** Manual Order Request, the RMA Request Form, and the Dealer Portal.
- **Your Details:** change your name, dealer, account numbers or shipping address.
- **Appearance:** System, Light or Dark.
- **Google Sheets:** sync status, Sync Now, Open Sheet, Switch Account.
- **Export to Excel:** a copy of your history and on-hand stock for your own reference.

### Manual Order Request
Replaces the Manual Inventory Order Jotform. Pick items and quantities (up to 20 each, or 10 for PRO MAX and J-arm mounts), enter a reason and optional special instructions, and the app drafts an email to your manager. You type your manager's email once and it's saved. Your shipping details come from your setup, and the order is kept until you clear it.

## Files

| File | Purpose |
|---|---|
| `index.html` | The whole app: HTML, CSS and JavaScript in one file. |
| `sw.js` | Service worker. Caches the app so it opens offline. |
| `manifest.json` | Lets phones install the site as an app. |
| `icon-192.png`, `icon-512.png`, `apple-touch-icon.png` | App icons. |
| `privacy.html` | Privacy policy, linked from the app and required for Google sign-in. |
| `README.md` | This file. |

Outside libraries load from public CDNs and are cached for offline use: ZXing (barcode scanning), SheetJS (Excel) and Google Identity Services (sign-in).

## Updating the site

1. Upload the changed files to the repo, replacing the old ones.
2. **Bump the version in `sw.js`** (for example `techstock-v45` to `techstock-v46`) every time `index.html` changes. Phones then pick up the new version.
3. GitHub Pages can take a few minutes to publish. Close and reopen the app to load the new version.

To check which version is live, open `https://xploreforms.github.io/StockTracker/sw.js` and look at the `VERSION` line.

## Google Sheets setup (Google Cloud)

Sign-in uses an OAuth client ID (Web application) from Google Cloud. The client ID is set in `index.html` as `GOOGLE_CLIENT_ID`.

- **Authorized JavaScript origins** must include `https://xploreforms.github.io`. If the site moves, add the new origin, or sign-in fails with `origin_mismatch`.
- **Scopes:** `drive.file` (the app can only see the one spreadsheet it creates) and `userinfo.email` (to show which account is connected).
- The OAuth consent screen links to `privacy.html`.
- **Never put the OAuth client secret in this repo.** The app doesn't use it.

Each tech's spreadsheet has two tabs: **History** (every entry) and **On hand** (current stock). The app finds the right sheet again by a hidden tag, even if it gets renamed.

## Privacy

- No data goes to a server or to the developer. Entries stay on the phone and in the tech's own Google Drive.
- Tech details are typed by each tech. No staff list, account numbers or addresses are built into the app.
- See `privacy.html` for the full policy.

## Data stored on the phone

| Key | Contents |
|---|---|
| `techstock.events` | Every logged entry |
| `techstock.me` | The tech's details |
| `techstock.gs` | Google Sheets connection |
| `techstock.deleted` | IDs of deleted entries, so deletions sync to Google Sheets |
| `techstock.count` | Count in progress |
| `techstock.order` | Manual order draft |
| `techstock.manager` | Manager's email |
| `techstock.theme` | Light or dark mode |
| `techstock.lastBackup`, `techstock.backupCount` | When the last Excel export was made |

Clearing the site's data in the browser removes all of these. Anything already synced stays in Google Sheets.
