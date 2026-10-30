# Prospect Intelligence (prototype v0.2, aligned to Dev Spec v0.2)

A reviewable queue of accounts with evidence-backed buying signals. Growth approves every recommendation; **nothing is ever sent automatically.** One page (`index.html`) plus a Google Sheet as the record.

## Files
- `index.html` – GitHub Pages page. Tabs: Review queue, Approved, Parked, Weekly digest, How it works. Runs on sample data until you add sheet links.
- `prospect-intel-sheet-template.xlsx` – import into Google Sheets. Tabs: Merchants, Partners, Playbooks, Lists, Read me.

## What changed from v0.1 (per the spec)
- **Separate merchant and partner ICPs**, each with its own 100-point rubric (spec section 6). Merchant: ICP fit 25, Solution fit 20, Buying intent 30, Relationship path 15, Commercial viability 10. Partner: Audience alignment 25, Decision influence 20, Complementary services 15, Opportunity signals 25, Relationship access 15.
- **Ratings are dropdowns** (Strong / Partial / Weak / None / Unknown). The intent / opportunity category is derived from the Signal: verified = full signal points, inferred = capped at 10. **Unknown is labelled Unknown, not scored as negative.**
- **Bands** A 85–100, B 70–84, C 50–69, D 0–49. Band A also needs a verified, high-confidence trigger (merchant intent at least 20/30), otherwise it shows as **B\***. The spec's example (86/100, Medium confidence) lands exactly there.
- **Confidence is separate**: evidence type + verified + second source + excerpt captured. High ≥ 85, Medium ≥ 55.
- **Hard gates run before scoring**: existing owner / open deal / current customer, suppression / opt-out, duplicates (by normalized domain), disqualifiers (unsupported category, tax-filing or remittance expectation, guaranteed processor approval, dispensary) and "route to another TOT module". Gated accounts get **no score** and no outreach.
- **Signal taxonomy and routing** from spec section 8 (signal → next action → team), in the CONFIG block, not hard-coded in logic.
- **Playbook registry** (Playbooks tab). Only an **Approved** ID and version can be recommended; otherwise the card says "No approved playbook — route to Growth". Registry IDs in the sample are `SAMPLE-…` placeholders.
- **Review actions**: Approve, Needs research, Revise, Defer, Reject. A reviewer name is required, and the decision date and reviewer are recorded. Rejected or deferred accounts only return through an explicit "Re-open" confirmation. Suppressed accounts have no re-open.
- **Named assignee** is required before an approved account is execution-ready. Approved accounts track outcome (conversation → Sales-accepted opportunity → proposal → won) and won MRR.
- **Weekly digest** with top accounts, funnel, and a pilot scorecard (acceptance rate, duplicate / suppression / ownership catches).
- Cards show the spec's reviewer fields: score and confidence with category breakdown, signal, evidence URL / excerpt / observed date, offer, action (channel + playbook ID/version), approach, why now, owner team, prerequisites, if-blocked fallback, timing, status.

## Put it on GitHub Pages
1. Create a repo and add `index.html` to the root.
2. Settings → Pages → deploy from the main branch, root folder.
**Pages sites are public by default.** Keep the sample data on a public page. Restricting a Pages site to your org generally needs GitHub Enterprise Cloud; check who owns our GitHub plan.

## Connect your Google Sheet
1. File → Import → upload the `.xlsx` → "Replace spreadsheet".
2. For each of Merchants, Partners and Playbooks: File → Share → Publish to web → choose that tab → CSV → Publish. Copy the links.
3. In `index.html`, fill in `SHEET_CSV_URLS = { merchant: "…", partner: "…", playbooks: "…" }`.
**Anyone with a link can read that tab.** Public business information only; no personal contact details, no CRM exports. If loading fails, the page shows an error and falls back to sample data. I tested the loader with mocked CSV exports of the template, not against a real published sheet.

## Not in this prototype (from the spec)
- No Pipedrive lookups: CRM status, owner and suppression are **entered** in the sheet, so the gates are only as good as that entry. The real check needs the read-only Pipedrive integration Dev is scoping.
- Reading is manual; the page doesn't discover or enrich anything. The existing lead-site-scan could fill Platform later.
- The page is read-only: decisions are saved in that browser only, with a "Copy changes" button to update the sheet. Decisions persisting "across refreshes and imports" needs the sheet or CRM to be the writable record.
- Spec fields not modeled yet: CRM entity IDs, refresh date, fallback due date, capture channel vs acquisition source, data retention, access control.
- Time saved is not measured.

## Assumptions to confirm (all are hypotheses, not validated)
- All weights and bands (from the Phase 1 audit via the spec). Austin must approve numerical ranking as a review aid.
- Rating fractions: Strong 100%, Partial 50%, Weak 20%.
- Signal point values per signal, and the partner A-band minimum (17 of 25, my 2/3 analogue of 20 of 30).
- Confidence point split and the 85 / 55 cut-offs, the 180-day stale limit, and the "2 business days" timing (copied from the spec's example).
- Product boundaries are shown only as "confirm with Product" prerequisites (non-Shopify migration is premium-assisted; checkout enforcement is documented for US vape/nicotine only). Nothing here is an external claim.
- Contact permission is a Compliance decision; this tool does not decide it.

## Keeping the page and the sheet in sync
Lists, point values and signals live in the `Lists` tab and in the CONFIG block near the top of `index.html`. If you change one, change the other. I checked that the page and the sheet formulas give the same score, confidence, band and gate for all 18 sample rows.
