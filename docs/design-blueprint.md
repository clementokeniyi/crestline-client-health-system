> **Note on this document:** design document written before the build. The fictional company name has been changed to Crestline for this repo. Only the client-health flagging slice of this design was built and verified — see results.md. The traffic feed (GSC/GA4 stand-in), content-audit task, daily digest email, native booking form and extra properties (Client Stage, Service Tier, Audit Due Date) are design, not built. Section 11 of the original (an application send checklist) was removed.

# Crestline — Client Health & Segmentation Engine
## A new build from scratch, for a demo walkthrough

**Honest framing for the video — say this up front:**
> "This is a test build I made from scratch for this brief, with fictional data. It is not a client system, and I am not claiming a team has used it for a year. I built it to show how I would solve your slipping-client problem, end to end."

That framing matters. The brief says a live system is not required — a workflow, test copy, screenshots, diagram or documentation will do — and the job post cares about shipped, documented work, not polish. Do not present test numbers as real client results.

**Fictional company for all demo data:** Crestline. Crestline is a fictional company created for this demonstration — do not present it as a real client.

---

## 1. The build in one sentence

One HubSpot segmentation structure powers three things: who owns the client, whether the client is slipping, and whether the client is due a content audit — so reporting, routing and alerts never rely on three different manual lists.

## 2. How the design maps to the operations brief

The brief behind this design lists eight workstreams. The design covers the core of five of them, and names the rest as extensions rather than pretending to cover everything:

- **Project 2 — Standardise HubSpot segmentation, so reporting and routing run off one structure.** This is the foundation of the build. Every list below is derived from the same company properties.
- **Project 3 — Connect Google Search Console, GA4 and Merchant Center to Claude, so the account team gets analysis and alerts automatically.** The build uses a Google Sheet as a stand-in for those API feeds (see section 5). In production the sheet is swapped for API calls; the alert logic does not change. Say that explicitly in the video.
- **Project 4 — Automate repetitive work across client account management and sales.** The daily health check, task creation and digest replace a manual client-by-client check.
- **Project 1 — Migrate meeting booking onto native HubSpot forms and standardise forms.** One native HubSpot form is included: "Book a client check-in". It feeds the same owner and health fields, so a booking request is also a health signal.
- **Project 6 — Systematise content audits, so the standard lives in the process.** An At-Risk or Audit-Due client automatically gets a standard audit task with a checklist, instead of an audit starting from a blank page.
- **Not built here, named as next steps only:** Project 5 (lead emails, measured on clicks and replies), Project 7 (partnerships/JV operations), Project 8 (taking over a product). Do not claim these in the video.

It also answers the brief's own test — "our account managers cannot see when a client is slipping" — with a proposed build and a reason, rather than asking for a specification. And it matches the brief's working pattern: work out what should exist, build it, document it, and stay with it until it becomes how things are done.

## 3. Free tools only

- HubSpot free CRM — companies, custom properties, lists, tasks, native form
- Make free tier — daily scenario (well within the free operations limit for 12 test companies)
- Google Sheets — client snapshot, alert log, and the GSC/GA4 stand-in sheet
- Loom free — under 5 minutes, link set so anyone with it can watch without signing in

No HubSpot certification, paid tier, or real client account is needed to demo this.

## 4. HubSpot setup — build this first

### Company properties (all on the Company object)

| Property | Type | Purpose |
|---|---|---|
| Client Stage | Dropdown: Onboarding / Active / At Risk / Paused / Alumni | Single source for lifecycle reporting |
| Service Tier | Dropdown: Starter / Growth / Scale | Routing and priority, not a separate list |
| Account Owner | HubSpot owner | One owner per client, used by tasks and the digest |
| Last Content Published | Date | Activity signal — not Last Modified (see section 8) |
| Last Client Reply | Date | Responsiveness signal |
| Last Report Sent | Date | Account-management signal |
| Last Alert Sent | Date | Prevents duplicate alerts |
| Client Health | Dropdown: Healthy / Watch / At Risk | Written only by the automation, never by hand |
| Audit Due Date | Date | Drives the audit queue |

Rule to state on camera: **nobody edits Client Health by hand.** If a human can edit it, the list and the dashboard will drift apart. The automation owns it.

### Lists — all derived from the properties above, never maintained by hand

- **Active Clients:** Client Stage = Active
- **At-Risk Clients:** Client Health = At Risk
- **Watch List:** Client Health = Watch
- **Audit Queue:** Audit Due Date is in the past, or Client Health = At Risk
- **Needs Check-in Booking:** No booking request in 30 days and Client Health = Watch or At Risk

### Native form — "Book a client check-in"

- Fields: Company (or email, matched to company), Contact name, What do you want to cover? (dropdown), Preferred day
- On submit: create/update the contact and company, set the booking request date, create a task for the Account Owner, and do not create a duplicate company if the email domain already exists
- This is the Project 1 piece in miniature: one standard form, one routing rule, one owner.

### Test data — 12 fictional companies

Create 12 companies for Crestline, split across two fictional account managers. Vary them deliberately so the demo is not all-green or all-red:

- 7 Healthy, 2 Watch, 3 At Risk (adjust after your first real test run — report the actual result, not these targets)
- Mix tiers, mix owners, mix dates: some published yesterday, some 25+ days ago, one with no reply after a report, one with a traffic drop in the sheet below
- No real names, emails, domains or client data anywhere

## 5. Make scenario — the daily automation

Run daily at 9:00am. One scenario, in this order:

1. **Read the GSC/GA4 stand-in sheet.** Columns: Company, Clicks last 28 days, Clicks prior 28 days, Change %. Flag a traffic slip if Change % is below -30%. In production, this step becomes a Search Console / GA4 API call feeding the same columns. The rest of the scenario does not care where the row came from.
2. **Search HubSpot companies** where Client Stage = Active or Onboarding.
3. **Calculate, per company:**
   - Days since Last Content Published
   - Days since Last Client Reply
   - Days since Last Report Sent
   - Traffic slip: yes/no from step 1
4. **Apply the health rules (tune these in testing, then state the final numbers in the video):**
   - At Risk if: no publish for 21+ days, OR no reply for 7+ days after a report, OR no activity for 14+ days, OR traffic slip below -30%
   - Watch if: no publish for 14+ days, OR no reply for 4+ days after a report
   - Otherwise Healthy
5. **Deduplicate:** if Client Health is already At Risk and Last Alert Sent is within the last 7 days and there has been no new activity, skip the alert. Update the health field, but do not create another task or email.
6. **Act, only for new or worsened flags:**
   - Update Client Health in HubSpot
   - Create one HubSpot task for the Account Owner: "Check in with [Company] — [reason]"
   - If At Risk, also create the standard audit task (section 6)
   - Append one row to the Sheets Alert Log: date, company, owner, reason, old health, new health
7. **Send one daily digest** (email to the fictional account team) listing only Watch and At Risk clients, grouped by owner. No per-client emails.
8. **Error handling:** if a HubSpot update fails, write the failure to a Sheets Error Log and continue with the next company. The scenario must never fail silently and must never stop the whole run for one bad record.

### What was built vs. what came from elsewhere

Say this plainly in the video, because the brief asks for it:

- Built by you: the property and list design, the health thresholds, the deduplication rule, the digest format, the audit task checklist, and the test data.
- From a template / platform: HubSpot's native form, lists and tasks, and Make's HubSpot and Sheets modules.
- AI-assisted: use Claude to draft the first version of the date-difference and health-rule logic, then show what you changed — the deduplication guard and the decision to use activity dates instead of Last Modified (section 8). Do not hide the AI part; the brief expects AI-assisted work.

## 6. Content audit task — the standard lives in the process

When a client becomes At Risk or Audit Due, the automation creates a task with this checklist attached (as task notes or a linked doc):

1. Publish cadence vs. plan — last 4 publishes, gaps
2. Distribution check — where the last piece went live
3. Traffic check — GSC/GA4 change from the stand-in sheet
4. Client communication — last reply, last report, open questions
5. Next action and owner, with a date — no audit closes without this

The point to make on camera: the audit is not a document someone remembers to start. The health system starts it.

## 7. Sheets — three tabs, nothing more

- **Clients:** the 12 test companies and their activity dates (the working copy; HubSpot is the record, this is the view)
- **Alert Log:** every alert the scenario sent, with reason and owner — this is your evidence that it ran
- **Traffic Snapshot (GSC/GA4 stand-in):** company, clicks last 28 days, clicks prior 28 days, change % — label the tab "API stand-in for demo" so nobody mistakes it for a live feed

## 8. What broke — test for these two, then tell the true story

Do not invent a breakage. Run the scenario and expect these two; if a different one happens instead, tell that one.

**Break 1 — the silent one (lead with this).**
Your first dashboard can look fine while the logic is wrong: if you use HubSpot's Last Modified date as the activity signal, any edit — even fixing a typo — makes a slipping client look active. Nothing errors, the dashboard stays green, and the client keeps slipping. Fix: use Last Content Published, Last Client Reply and Last Report Sent only. Never Last Modified. This is the post's "fixed something silently broken" test, in miniature.

**Break 2 — the noisy one.**
The first run flags every company with an empty date field, and then re-alerts every day. Fix: treat an empty activity date as "needs data, not At Risk" until the field is filled, and add the Last Alert Sent deduplication guard in step 5. Show the before run (everyone flagged, duplicates) and the after run (only genuine flags, one alert each).

If you hit a third break in testing (for example a date-format mismatch between Sheets and HubSpot), add it as a one-line note. Do not stack three break stories in a 5-minute video.

## 9. Outcome — measure it, do not guess it

Run the test, then fill in these exact numbers before recording:

- Test companies: ___
- Flagged Watch: ___ / Flagged At Risk: ___
- Alerts sent (after deduplication): ___ / Duplicates prevented: ___
- Scenario run time: ___ seconds
- Manual check time: time yourself checking the same 12 companies by hand in Sheets/HubSpot: ___ minutes
- Digest: one email, grouped by owner, instead of ___ separate emails

Then give the rollout plan, because a new prototype has no adoption history to report:

- Week 1: pilot with one account manager, tune thresholds on real (fictional-for-demo) patterns, fix false positives
- Week 2: all account managers, digest becomes the Tuesday ops input, SOP shared in writing
- Week 3: thresholds frozen unless the Tuesday ops meeting agrees a change; one person owns the scenario and the SOP
- After that: swap the traffic sheet for live GSC/GA4 API calls, then add the lead-email and partnerships pieces as separate automations on the same segmentation structure

Close with one line: "One structure, one list, one digest — so an account manager opens one place and knows who is slipping, before the client tells us."

## 10. Under-5-minute video outline

- **0:00–0:45 — Problem.** "Account managers cannot see when a client is slipping until it is too late. Today that check is manual, spread across inboxes, and every team keeps its own list." Show the HubSpot At-Risk list, empty or manual, before the build. State the honest framing line from the top.
- **0:45–2:30 — Build.** Show the properties, the lists derived from them, the native booking form, then walk the Make scenario left to right. Name what you built, what came from a template, and what AI drafted vs. what you changed.
- **2:30–3:30 — What broke.** Show the Last Modified trap and the duplicate-alert run, then the fix. This is the section that proves operational thinking, not just configuration.
- **3:30–4:40 — Outcome.** Show the Alert Log, the digest, and your measured numbers from section 9. Then the 3-week rollout plan.
- **4:40–5:00 — Close.** One line (section 9), camera on, no new screen.
