> **Note on this document:** handover written before the clean run of October 28, 2026. Its “PARKED, NOT COMPLETE” status and fix list were superseded by that run — see results.md. The filter has been restored to Client Health Does not exist, the heartbeat was sent, the Run Log row was added, and the scenario remains Inactive. Old HubSpot tasks and Slack alerts from the crossed run remain, and clean-run counts use a timestamp cutoff for those two outputs.

# Phase 7 — Handover: half page

## What it does
This test system checks 11 HubSpot test companies each day in Make. A rule decides who is flagged At Risk. For flagged accounts only, it updates Client Health to At Risk and Last Alert Sent, creates a HubSpot task for the owner, sends a Slack alert to the private test-client-alerts channel, and logs a row in the TEST Client Health Log Sheet, Alert Log tab. No real client data is used. All companies, properties, lists, tasks, channels, and sheets are labelled TEST.

## Who receives alerts
The owner named in Test Owner, as text in the Slack alert and in the HubSpot task note. If Test Owner is blank, as for Kite Realty, the alert goes to the unassigned queue, and the task is assigned to C Okeniyi in HubSpot, because test-label owners are not HubSpot users.

## What to do on an alert
1. Read the Slack alert and the HubSpot task. Both carry the same sentence: company, last activity date, days since activity, and owner.
2. Check in with the client that day. The rule is a prompt to act, not proof that the client will leave.
3. Update Client Health in HubSpot after the check-in, and note the outcome on the task. Do not leave the task open with no outcome.
4. If the data is wrong, for example a blank date or a wrong owner, fix the HubSpot test fields, not the rule, and note the fix on the task.

## Who owns it
Owner: the person who runs the Make scenario, TEST Client Health Flagging, ID 8198010, folder TEST Client Health. No one else should edit the scenario while a run or a fix is in progress, because parallel edits caused the route crossing in October 2026.

## How to pause it
The scenario is Inactive and the schedule is not enabled. That is the safe parked state. Do not enable the schedule until the Phase 5 fix list below is complete and a clean run is verified. To pause a live version, set the scenario to Inactive in Make. Do not delete the scenario, the HubSpot test companies, or the Sheet to pause it.

## Known limits — read before you use it
- PARKED, NOT COMPLETE: Grove route has no Task. Kite, Juniper, and Harbor modules are unverified. There is no clean end-to-end run yet, no heartbeat for a complete run, and no Sheet Run Log row for a complete run.
- Filter is TEMPORARY: Client Health Equal to At Risk AND Test Last Activity Date earlier than October 5, 2026, 6:29 PM. It must be restored to Client Health Does not exist, keeping the date condition, only after a clean verified run. A fixed date does not roll forward in a real daily run. Production needs a rolling date calculation.
- Task company association is not settable in Make. The association field offers only Category and Type ID, no target Company ID. Tasks therefore carry the company in the title and notes, but are not associated to the company record in HubSpot.
- Claude step is manual, not an API call. The exact Claude prompt is a document, and the explanation is run as a manual step for flagged accounts only. Production would replace it with an API call. No Claude API output is faked in this build.
- Empty route stubs: the canvas shows 9 route stubs, 1st to 5th populated, 6th to 9th empty. Empty stubs do not run. Delete them if the editor allows it, otherwise do not add modules to them.
- Old crossed-run outputs remain: old HubSpot TEST alert tasks and Slack TEST alert messages from the crossed run were not cleaned up, because cleanup hit sign-in walls. Any clean run verification must use a timestamp cutoff and count only new outputs, until those old outputs are cleaned up with signed-in sessions.
- Test-label owners are not HubSpot users, so tasks assign to C Okeniyi. Test Last Activity Date is used, not the default Last Activity Date, because the default property cannot be selected in import.

## Workflow naming convention
- HubSpot: TEST + object type + purpose, for example TEST Baseline At Risk 21 Days, TEST Crestline.
- Properties: Test + field name for test-only fields, for example Test Last Activity Date, Test Owner. System properties keep their system names: Client Health, Last Alert Sent.
- Make: TEST + purpose, for example TEST Client Health Flagging, TEST Client Health, TEST HubSpot Connection Check.
- Slack: test-client-alerts, private, no one invited except the Make bot.
- Sheets: TEST Client Health Log, tabs Clients, Alert Log, Run Log.

## Phase 5 fix list — do this before you enable anything
1. Add a new Grove Task on the Grove route, with the Grove sentence, Assigned To C Okeniyi, Due Date 1793059200000.
2. Verify and fix Kite Task, Slack, and Sheet on the Kite route, with the Kite sentence. Kite filter is already fixed and saved to the [1. Properties: Name] pill, Equal to, Kite Realty.
3. Verify and fix Juniper Task, Slack, and Sheet on the Juniper route, with the Juniper sentence, Notes Date October 3, 2026.
4. Verify and fix Harbor Task, Slack, and Sheet on the Harbor route, with the Harbor sentence.
5. Fix Falcon Slack and Sheet sentences to Falcon, if not already saved. Falcon Task 12 is verified correct.
6. Delete empty 6th to 9th route stubs if the editor allows it. Verify exactly 5 populated routes in the Order routes dialog, not by visual position.
7. Clean up old crossed HubSpot tasks and Slack alerts with signed-in sessions, or use a timestamp cutoff and state that old outputs remain.
8. Run once under the temporary filter, verify 11 in, 5 passing with 1 per route, 5 tasks, 5 alerts, 5 Sheet rows, no duplicates.
9. Restore the filter to Client Health Does not exist, Save, leave Inactive, then post the heartbeat and the Run Log row only after that clean run is verified.

## Reviewer questions — 3, ready to use, no feedback waited for or claimed
1. Is a fixed 21-day threshold the right starting point, or should the threshold be calibrated on real activity data before this is used live, and how would you calibrate it?
2. When an account is flagged but the owner is unassigned, as with Kite Realty, who should own the check-in, and what should happen if no one picks it up within one day?
3. The Claude step is manual in this test build. What controls would you require before replacing it with an API call, so that an explanation never changes who is flagged, and never invents activity that did not happen?
