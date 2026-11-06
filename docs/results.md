# Phase 8 — Results: short, test runs only

Status: Phase 5 was PARKED, then completed in the evening of October 28, 2026, by a clean run verified below. The provisional results above are kept, not overwritten, and the clean run is added as a new, dated section. No result here is a production business result. Every number is a test-environment result.

## Baseline versus flagged — the one comparison that is verified
- Phase 3 baseline, list TEST Baseline At Risk 21 Days, ID 17, size 5: Falcon Legal, Grove Hotels, Harbor Logistics, Juniper Travel, Kite Realty. Client Health blank on all 5 at baseline, no alert task associated.
- Slice 2 filter test, Run ID 070c18f86ff3432e8a5d12d606f9eef5: 11 bundles in, passing 5 exactly, the same 5 companies. Blocked 6: Alpine, Beacon, Cedar, Delta, Ember, Ivy. Ivy was blocked because a blank date is not earlier than October 5, 2026.
- Rule, approved October 26, 2026, Test Last Activity Date only, measured from October 26, 2026: At Risk, more than 21 days, 5; Watch, 14 to 20 days, 2, Delta and Ember; Healthy, under 14 days, 3, Alpine, Beacon, Cedar; Needs Data, blank date, 1, Ivy. Total 11. Harbor counted once.

## What the test system did, step by step, honestly
- Companies updated to At Risk: 5, in the timestamp-fix run, Run ID a8890e82000d42198eb2344ce40782a5, Status Success, 11 operations. HubSpot verified: exactly those 5 show Client Health At Risk and Last Alert Sent October 26, 2026. Other 6 test companies blank. In that same run, tasks 0, Slack alerts 0, Sheet rows 0, because downstream modules were unconnected. That run is a flagging update, not an end-to-end alert run, and it must not be presented as one.
- Crossed Router run, Run ID 3b989fe9125042b795b4f46b4dd6eaf4, Status Success, 32 operations: Sheet Alert Log 9 rows, all Date October 26, 2026, At Risk, with Juniper duplicated 5 times, Kite 1, Falcon 1, Grove 1, Harbor 1. Expected clean result was 5 rows, no duplicates. HubSpot task and Slack alert counts for that run were predicted, 8 tasks and 9 alerts, but were NOT checked directly, so they are not presented here as verified counts.
- Since that crossed run: Kite filter fixed and saved to the [1. Properties: Name] pill, Equal to, Kite Realty. Falcon route fixed and saved, Slack and Sheet sentences changed from Harbor content to Falcon content. Sheet Alert Log cleaned to 0 data rows, headers intact, Run Log tab untouched. Kite Slack verified correct, closed without changes.

## What is NOT yet a result
- No clean end-to-end run: no single run with 5 tasks, 5 Slack alerts, and 5 Sheet rows, 1 per company, no duplicates, verified together.
- No threshold-to-notification time from a clean run. No time is claimed here, because no clean run exists to measure it from.
- No heartbeat for a complete run, and no Sheet Run Log row for a complete run. The only Slack heartbeat in the channel is the Phase 1 test message, "TEST heartbeat check", from October 26, 2026, which is not a completion heartbeat.
- No manual time saved. That metric was cut first in the streamlined plan, because no calculation method was established. It is not estimated here.
- No adoption by real users. This is a test build. No account manager used it live, and no adoption number is claimed.

## Outcome number for the video — use this wording, provisionally
"In the test, the rule flagged 5 out of 11 test accounts as At Risk, matching the 5-account baseline, and the test system updated those 5 accounts to At Risk in HubSpot. End-to-end alerting for all 5 has not yet been verified in one clean run, and that fix is listed in the handover."

## Clean run verified — October 28, 2026, evening

- Run ID: 0abf6bb8536d4bc3815a41a5e2073e83, started October 29, 2026, 1:39:48 AM as shown in Make, Trigger Manual, Duration 4 seconds, Operations 21, Credits 21, Data size 24.6 KB.
- Before the run: Sheet Alert Log confirmed 0 data rows, headers only, so all rows after the run are new from this run only.
- Run breakdown, 1 + 5 + 5 + 5 + 5 = 21: Search for Companies 1; Update a Company 5; Create a Task 5 completed — Task 12 Falcon Legal, Task 21 Harbor Logistics, Task 45 Grove Hotels, Task 20 Kite Realty, Task 48 Juniper Travel; Slack Send a Message 5 completed in test-client-alerts only — Slack 33 Falcon, Slack 13 Harbor, Slack 16 Grove, Slack 46 Kite, Slack 36 Juniper; Google Sheets Add a Row 5 completed — Sheet 34 Falcon, Sheet 17 Harbor, Sheet 35 Grove, Sheet 47 Kite, Sheet 37 Juniper.
- Sheet Alert Log after the run: 0 rows to 5 rows, one per company, all with the correct sentence and New Health At Risk. Kite Realty Owner column blank, as designed, because Kite has no Test Owner and its sentence states Owner is unassigned.
- "The bundle did not pass through the filter" lines in the log are normal route filtering, not errors: each company is blocked on the 4 routes that are not its own, and completes on its own route only. Kite was not 0 in this run.
- After the run, filter restored and saved: At Risk filter condition 2 changed from Client Health Equal to At Risk back to Client Health Does not exist, date condition unchanged, Test Last Activity Date earlier than October 5, 2026, 6:29 PM. Scenario left Inactive, schedule not enabled, no second run.
- Heartbeat sent manually to test-client-alerts: "TEST heartbeat: Completion run complete, 11 accounts checked, 5 accounts alerted".
- Sheet Run Log row added: Date October 26, 2026, Accounts Checked 11, Accounts Flagged 5, Status Success.
- Clients tab empty is expected, not a failure: Clients was headers only from Phase 1, company data lives in HubSpot, and Make writes only to Alert Log and Run Log.
- Still not a result: no threshold-to-notification time measured from a single run in clock time, because the run duration shown is 4 seconds for the automation itself, not a business threshold-to-notification time, and no manual time saved, because no calculation method was established. No adoption by real users. No production business result.
- Crossed-run history above is kept. Old HubSpot tasks and Slack alerts from the crossed run remain, and clean-run counts use a timestamp cutoff for those two outputs. The Sheet count does not need a cutoff, because Alert Log was cleaned to 0 before the clean run.

## Outcome number for the video — use this wording now, clean run verified
"In the test, the rule flagged 5 out of 11 test accounts as At Risk, matching the 5-account baseline, and in one clean run the test system created 5 HubSpot tasks, sent 5 Slack alerts, and logged 5 Sheet rows, 1 per company, with no duplicates."

## If a clean run is verified later, update this file with
Run ID, 11 in, 5 passing with 1 per route, 5 tasks with correct sentences, 5 Slack alerts in test-client-alerts only, 5 Sheet Alert Log rows with no duplicates, threshold-to-notification time measured from that run only, filter restored to Client Health Does not exist, heartbeat text, and Run Log row. Do not overwrite the crossed-run history above. Add the clean run as a new, dated section. This has now been done for Run ID 0abf6bb8536d4bc3815a41a5e2073e83 above.
