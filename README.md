# zabaan — Contributor Form

A privacy-first language preservation form. Contributors record voice notes and enter phrase translations. Audio goes to Cloudinary, metadata goes to Google Sheets.

---

## Setup (15 minutes)

### Step 1 — Cloudinary (audio storage)

1. Log in to your Cloudinary account at cloudinary.com
2. Go to **Settings → Upload → Upload presets**
3. Click **Add upload preset**
4. Set **Signing mode** to `Unsigned`
5. Give it a name like `zabaan_audio`
6. Save
7. Copy your **Cloud name** from the top of the dashboard

Open `index.html` and replace:
```
const CLOUDINARY_CLOUD_NAME = 'YOUR_CLOUD_NAME';
const CLOUDINARY_UPLOAD_PRESET = 'YOUR_UPLOAD_PRESET';
```

---

### Step 2 — Google Sheets (metadata storage)

1. Create a new Google Sheet
2. Add these column headers in row 1:
   ```
   Timestamp | Name | Fluency | Age | Region | Want Credit | Phrase # | Kashmiri | Translation | Category | Context | Notes | Audio URL
   ```
3. Go to **Extensions → Apps Script**
4. Paste this code:

```javascript
function doPost(e) {
  const sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
  const data = JSON.parse(e.postData.contents);
  const { contributor, phrases } = data;

  phrases.forEach((phrase, i) => {
    sheet.appendRow([
      contributor.timestamp,
      contributor.name,
      contributor.fluency,
      contributor.age,
      contributor.region,
      contributor.wantCredit ? 'Yes' : 'No',
      i + 1,
      phrase.kashmiri,
      phrase.translation,
      phrase.category,
      phrase.context,
      phrase.notes,
      phrase.audioUrl
    ]);
  });

  return ContentService
    .createTextOutput(JSON.stringify({ status: 'ok' }))
    .setMimeType(ContentService.MimeType.JSON);
}
```

5. Click **Deploy → New deployment**
6. Type: **Web app**
7. Execute as: **Me**
8. Who has access: **Anyone**
9. Click Deploy and copy the Web App URL

Open `index.html` and replace:
```
const SHEETS_ENDPOINT = 'YOUR_GOOGLE_SHEETS_WEBAPP_URL';
```

---

### Step 3 — Deploy to GitHub Pages

1. Create a new GitHub repository (e.g. `zabaan-contributor`)
2. Upload `index.html` to the repo
3. Go to **Settings → Pages**
4. Source: **Deploy from a branch → main → / (root)**
5. Your form will be live at `https://yourusername.github.io/zabaan-contributor`

Share that URL in your WhatsApp group.

---

## What gets collected

| Field | Where stored |
|-------|-------------|
| Voice recording | Cloudinary (private, secure URL) |
| Kashmiri text | Google Sheets |
| Translation | Google Sheets |
| Category & context | Google Sheets |
| Contributor info | Google Sheets |
| Audio URL | Google Sheets (links to Cloudinary) |

---

## Privacy notes

- Audio files are stored in your private Cloudinary account
- Only you have access to the Google Sheet
- Contributors are clearly informed before submitting
- No data is shared with third parties

---

## For the research paper

Your Google Sheet is your pilot dataset. Once you have 50+ entries, export it as CSV for analysis. Each row is one phrase from one contributor — exactly the format needed for a parallel corpus.
