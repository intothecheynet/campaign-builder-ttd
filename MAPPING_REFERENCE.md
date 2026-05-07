# TTD Bulk Upload — Field Mapping Reference

Cross-check this document against `mapper.py` to verify all rules are correct.
Update `defaults.json` to change default values without touching code.

**Legend**
- ✅ Mapped from source document
- 🟣 Pulled from `platform_defaults.json` (TTD technical settings)
- 🔵 Pulled from `defaults.json` (business defaults by LOB/channel)
- ⬜ Left blank — fill in manually post-export
- 🚫 Read Only — TTD populates, never written

**Default priority order (most specific wins):**
`platform_defaults` → `global` → `by_channel` → `by_lob` → `by_lob_and_channel`

---

## Campaign Sets tab

| TTD Field | Status | Source | Source Field | Rule |
|-----------|--------|--------|--------------|------|
| IO ID | ✅ | Media Brief | `Brief ID` | Direct copy. Blank if missing. |
| Campaign Set ID | 🚫 | — | — | Read Only |
| Campaign Set Name | ✅ | Trafficking Sheet | `Campaign` (first row) | First unique Campaign value. Fallback: LOB + Product/Service from Media Brief. |
| Advertiser | 🚫 | — | — | Read Only |

---

## Campaigns tab

| TTD Field | Status | Source | Source Field | Rule |
|-----------|--------|--------|--------------|------|
| Campaign ID | 🚫 | — | — | Read Only |
| Campaign Name | ✅ | Trafficking Sheet | `Campaign` | All unique Campaign values become separate campaign rows. Fallback: `LOB` + `Product/Service` from Media Brief. |
| Advertiser | 🚫 | — | — | Read Only |
| Description | ✅ | Media Brief | `Media Objectives` | Falls back to `Communications Objective` if blank. |
| Objective | 🔵 | defaults.json | `by_lob_and_channel` → `by_channel` → `by_lob` | Most specific LOB+Channel match wins. |
| Primary Channel | 🔵 | defaults.json | `by_channel[channel]["Primary Channel"]` | Derived from the first Channel in the Media Plan. |
| Goals | 🔵 | defaults.json | `by_lob[lob]["Goals"]` | LOB-level goal type. |
| Time Zone ID | 🔵 | defaults.json | `global["Time Zone ID"]` | Default: `Eastern Time (US & Canada)` |
| Pacing Mode | 🔵 | defaults.json | `global["Pacing Mode"]` | Default: `Even` |
| Manually Prioritize Ad Groups | 🔵 | defaults.json | `global` | Default: `No` |
| Comscore Settings | 🟣 | platform_defaults.json | `campaigns["Comscore Settings"]` | Blank by default. |
| Comscore CCR Settings | 🟣 | platform_defaults.json | `campaigns["Comscore CCR Settings"]` | Blank by default. |
| Nielsen Settings | 🟣 | platform_defaults.json | `campaigns["Nielsen Settings"]` | Blank by default. |
| Reporting and Attribution | 🟣 | platform_defaults.json | `campaigns["Reporting and Attribution"]` | Blank by default. |
| Custom CPA Calculation | 🟣 | platform_defaults.json | `campaigns["Custom CPA Calculation"]` | Blank by default. |
| Custom CPA Pixels and Weights | 🟣 | platform_defaults.json | `campaigns["Custom CPA Pixels and Weights"]` | Blank by default. |
| iSpot Settings | 🟣 | platform_defaults.json | `campaigns["iSpot Settings"]` | Blank by default. |
| IO Contract | ✅ | Media Brief | `Brief ID` | Same as IO ID. |
| Campaign PO # | ⬜ | — | — | Not in source documents — add manually post-export |
| Custom ROAS Type | 🟣 | platform_defaults.json | `campaigns["Custom ROAS Type"]` | Blank by default. |
| Custom ROAS Pixels and Weights | 🟣 | platform_defaults.json | `campaigns["Custom ROAS Pixels and Weights"]` | Blank by default. |
| Frequency Group | 🚫 | — | — | Read Only |
| Innovid Settings | 🟣 | platform_defaults.json | `campaigns["Innovid Settings"]` | Blank by default. |
| Realytics Settings | 🟣 | platform_defaults.json | `campaigns["Realytics Settings"]` | Blank by default. |
| Campaign Seed | 🟣 | platform_defaults.json | `campaigns["Campaign Seed"]` | Blank by default. |

---

## Ad Groups tab

| TTD Field | Status | Source | Source Field | Rule |
|-----------|--------|--------|--------------|------|
| Ad Group ID | 🚫 | — | — | Read Only |
| Ad Group Name | ✅ | Media Plan / Trafficking | `Campaign \| Channel \| Partner/Tactic` | Pipe-delimited: Campaign + Channel + Tactic. |
| Campaign | 🚫 | — | — | Read Only (links to Campaign tab) |
| Description | ✅ | Trafficking Sheet | `Tactic` | Tactic value used as description. |
| Status | 🚫 | — | — | Read Only |
| Channel | ✅ | Media Plan / Trafficking | `Channel` | Normalised via `CHANNEL_MAP`. |
| Labels | 🟣 | platform_defaults.json | `ad_groups["Labels"]` | Blank by default. |
| Goal Type | 🔵 | defaults.json | `by_lob_and_channel` → `by_channel` → `by_lob` | E.g. Auto + CTV = VCR. |
| Goal Value | 🔵 | defaults.json | `by_lob_and_channel` → `by_channel` | Numeric target value. |
| GRP Overall Frequency | 🟣 | platform_defaults.json | `ad_groups["GRP Overall Frequency"]` | Blank by default. |
| Base Bid | 🔵 | defaults.json | `by_lob_and_channel` → `by_channel` | CPM dollar amount. |
| Max Bid | 🔵 | defaults.json | `by_lob_and_channel` → `by_channel` | CPM dollar amount. |
| Priority | 🔵 | defaults.json | `global["Priority"]` | Default: `Medium` |
| Predictive Clearing Enabled | 🔵 | defaults.json | `global` | Default: `Yes` |
| Auto Enable Upcoming Features | 🔵 | defaults.json | `global` | Default: `No` |
| Marketplace | 🔵 | defaults.json | `by_channel[channel]["Marketplace"]` | CTV/DOOH default to Private Marketplace. Others default to Open Exchange. |
| Audience | ✅ | Audience Matrix + Media Plan | `Segment Description` (activation rows) | Activation segments for TTD platform, semi-colon separated. Media Plan `Audience` field used if populated. |
| Audience Excluder | ✅ | Audience Matrix | `Segment Description` (suppression rows) | Suppression segments for TTD platform. |
| Cross Device | 🟣 | platform_defaults.json | `ad_groups["Cross Device"]` | Blank by default. |
| Creatives | ✅ | Trafficking Sheet | `Creative` | Creative name from trafficking row. |
| KOA Optimizations | 🟣 | platform_defaults.json | `ad_groups["KOA Optimizations"]` | Blank by default. |
| Funnel Location | 🟣 | platform_defaults.json | `ad_groups["Funnel Location"]` | Blank by default. |
| Viewability Suite (Quality Alliance) | 🟣 | platform_defaults.json | `ad_groups["Viewability Suite (Quality Alliance)"]` | Blank by default. |

---

## Budget Flights tab

| TTD Field | Status | Source | Source Field | Rule |
|-----------|--------|--------|--------------|------|
| Flight ID | 🚫 | — | — | Read Only |
| Campaign | ✅ | Trafficking / Media Plan | `Campaign` | Links flight to campaign by name. |
| Ad Group | ✅ | Trafficking / Media Plan | derived | Matches the Ad Group Name built above. |
| Flight Budget (in advertiser currency) | ✅ | Media Plan | `Budget` | Total flight budget. Fallback: `Est Media Cost`. |
| [Kokai Only] Ad Group Allocated Budget | ⬜ | — | — | Not used |
| Daily Spend Cap (in advertiser currency) | ⬜ | — | — | Not in source documents — calculated manually. |
| Impression Budget | ✅ | Media Plan | `Planned Impressions` | Direct copy. |
| Daily Impression Cap | ⬜ | — | — | Not in source documents |
| Start Date Inclusive UTC | ✅ | Media Plan | `Flight` (start) | Format: `M/D/YYYY - M/D/YYYY` e.g. `12/1/2025 - 12/31/2025`. Fallback: Trafficking `Creative Flight Date`. Output: `YYYY-MM-DD 00:00:00` |
| End Date Exclusive UTC | ✅ | Media Plan | `Flight` (end) | Same parsing. |
| Action | 🔵 | defaults.json | `global["Action"]` | Always `New` for new line items. |
| Geography Targets | 🚫 | — | — | Read Only |
| Channel | 🚫 | — | — | Read Only |

---

## Campaign Fees tab

| TTD Field | Status | Source | Rule |
|-----------|--------|--------|------|
| Fee ID, Fee Card ID, Campaign ID, Campaign Name, Start Date (UTC), Status, Fee Type | 🚫 | — | Read Only |
| Fee Name, Value | ⬜ | — | Not in source documents — add manually. |

---

## Ad Group Fees tab

| TTD Field | Status | Source | Rule |
|-----------|--------|--------|------|
| Ad Group Fee ID, Card ID, Ad Group ID, Name, Start Date, Status, Type | 🚫 | — | Read Only |
| Ad Group Fee Name, Value | ⬜ | — | Not in source documents — add manually. |

---

## Channel Aliases (CHANNEL_MAP)

| Source Value | → TTD Channel | Notes |
|---|---|---|
| Video CTV, CTV, Connected TV, Streaming TV | CTV | |
| Connected Home | Audio | Connected audio devices |
| Video OLV, OLV, Online Video, Pre-roll | OLV | |
| Display, Banner | Display | |
| Native | Native | |
| Audio Streaming Audio, Streaming Audio, Audio Podcasts, Audio | Audio | |
| Connected Car | Audio | In-car audio / connected vehicle |
| DOOH, Out of Home, Digital OOH | DOOH | |

> If a channel value isn't in this list it is passed through unchanged. Add entries to `CHANNEL_MAP` in `mapper.py` as needed.

---

## LOB Detection Order

1. Media Brief → `LOB:` field
2. Media Brief → `LOB/Corporate Function` field
3. Trafficking Sheet → `Campaign Key` column
4. Blank if none found

---

## Fields NOT in any source document (require manual entry post-export)

- Campaign PO #
- Daily Spend Cap
- Daily Impression Cap
- Frequency Group
- Campaign Fees (Fee Name, Value)
- Ad Group Fees (Ad Group Fee Name, Value)

---

## Media Plan DSP filter

Only rows where the `DSP` column matches (case-insensitive):

| DSP column value | Routes to |
|---|---|
| `TTD` | The Trade Desk bulk sheet |

All other rows are ignored.
