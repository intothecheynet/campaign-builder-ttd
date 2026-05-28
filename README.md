# Campaign Builder TTD

FastAPI web app that converts 4 agency input files into a **The Trade Desk bulk upload sheet** using Claude AI.

TTD only. For a multi-platform version (TTD + DV360 + Amazon DSP), see the [Campaign Builder web app](https://github.com/intothecheynet/ttd-campaign-builder).

---

## What It Does

Upload your 4 standard Excel inputs → get back a ready-to-upload TTD bulk sheet.

**Input files:**
1. Media Brief — objectives, KPIs, targeting, brand guidelines
2. Media Plan — channel breakdowns, flight dates, budgets, impressions, CPMs
3. Audience Matrix — audience segments, activation/suppression indicators
4. Trafficking Sheet — campaign structure, creatives, flight info, geo targeting

**Output:** TTD Excel bulk upload file with 6 tabs — CampaignSets, Campaigns, Ad Groups, Budget Flights, Campaign Fees, Ad Group Fees.

---

## File Map

```
campaign-builder-ttd/
│
├── app.py                  FastAPI server. All routes live here.
│                           Uses Claude AI to map inputs → TTD format.
│                           Saves human corrections as feedback rules.
│
├── mapper.py               Rule-based TTD mapper (no Claude required).
│                           Used as a fallback or for auditable mappings.
│                           Produces the same output format as the AI path.
│
├── defaults.json           Business defaults by channel + LOB.
│                           Controls Goal Type, Base Bid, Max Bid,
│                           Marketplace, Objective, Pacing Mode, etc.
│                           Edit this to match your account's standards.
│
├── platform_defaults.json  TTD technical field defaults.
│                           Applied as the base layer under all other defaults.
│                           Contains account-level fields that don't appear
│                           in source documents (measurement settings, etc.).
│
├── MAPPING_REFERENCE.md    Field-by-field mapping guide — which source field
│                           maps to which TTD field, and why. Rendered in
│                           the app at /mapping.
│
├── requirements.txt        Python dependencies.
│
└── templates/
    ├── index.html          Main upload UI — file inputs, generate + export.
    └── knowledge.html      Shows loaded defaults and saved feedback rules.
                            Route: /knowledge
```

---

## Routes

| Method | Route | What it does |
|---|---|---|
| GET | `/` | Main upload UI |
| POST | `/generate` | Upload 4 files → Claude maps them → returns TTD JSON preview |
| POST | `/revise` | Flag an issue → Claude re-maps with the correction → saves feedback rule |
| POST | `/export` | Write TTD JSON into the Excel bulk upload template → download |
| GET | `/knowledge` | View loaded defaults and saved feedback rules |
| GET | `/mapping` | Render MAPPING_REFERENCE.md in browser |

---

## How It Works

```
Upload 4 Excel files
        │
        ▼
POST /generate
  Claude reads all 4 files and maps them to TTD bulk upload format.
  Applies defaults from defaults.json and platform_defaults.json.
  Applies any saved feedback rules from previous corrections.
  Returns a JSON preview of the mapped data.
        │
        ▼
Review the preview in the UI.
If something is wrong → POST /revise with a plain-English correction.
  Claude re-maps with the fix applied.
  The correction is saved as a feedback rule for future campaigns.
        │
        ▼
POST /export
  Mapped data is written into ~/Downloads/TTD BULKSHEET.xlsx template.
  Browser downloads the completed bulk upload file.
```

---

## Defaults Priority

Applied most-specific-wins:

```
platform_defaults.json    ← base layer (TTD technical fields)
       ↓
global                    ← applies to everything
       ↓
by_channel                ← applies when channel is known (CTV, OLV, Display…)
       ↓
by_lob                    ← applies when Line of Business is known
       ↓
by_lob_and_channel        ← most specific, overrides all others
```

Edit `defaults.json` to change business defaults. Edit `platform_defaults.json` to change TTD account-level technical fields.

---

## Running Locally

```bash
pip install -r requirements.txt
uvicorn app:app --reload
# Open http://localhost:8000
```

Requires a TTD bulk upload template at `~/Downloads/TTD BULKSHEET.xlsx`. The template sheet names must match those defined in `app.py` (CampaignSets, Campaigns, Ad Groups, Budget Flights, Campaign Fees, Ad Group Fees).

Requires an Anthropic API key:

```bash
export ANTHROPIC_API_KEY=your_key_here
```
