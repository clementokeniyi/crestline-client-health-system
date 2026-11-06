> **Note on this document:** written October 28, 2026, before the clean run. The crossed run it documents is kept as history, not hidden — see results.md for the clean run that followed.

# Phase 6 — What broke: note only

Status: Phase 5 is PARKED, not complete. This note uses existing evidence only. No new live break tests were run for Phase 6, and none are claimed here.

## The test system in one line
Account managers cannot see when a client is slipping, so they find out when the client leaves. The test system checks HubSpot test companies daily in Make, applies a rule, and, for flagged accounts only, alerts in Slack, creates a HubSpot task, and logs a row in Google Sheets.

## What broke, in the order it was found

1. Phase 2 import, owner not found (10 errors)
   - What happened: the first 12-row import created 22 records and 11 associations, but 10 rows failed with "Could not find owner".
   - Lesson: test data must carry a test owner field that HubSpot can actually use. Fix used: Test Owner as single-line text, matched by Domain, second import 11 rows, 0 errors.

2. Phase 2 import, date left unmapped
   - What happened: the default Last Activity Date property cannot be selected in import, so the whole column was left unmapped and every company had a blank date.
   - Lesson: a default field is not always an importable field. Fix used: Test Last Activity Date as a new Date picker property.

3. Harbor duplicate deduped by HubSpot
   - What happened: two Harbor rows with the same domain were deduped to one survivor, Harbor Logistics, with a duplicate note on its record.
   - Lesson: domain deduping must be checked before counting companies. Total is 11, not 12. Harbor is counted once.

4. Kite missing owner, Ivy blank date
   - What happened: Kite Realty has no Test Owner, and Ivy Salon has no Test Last Activity Date.
   - Result in the design: Kite is still flagged, shown as Unassigned. Ivy is excluded, because a blank date is not earlier than October 5, 2026, and must not be At Risk. This is Needs Data, not At Risk.

5. Make module ID ordering
   - What happened: a module can only reference variables from modules with a lower ID number, regardless of flow position. A Tools module with ID 5 could not be referenced by Task ID 3 or Slack ID 4.
   - Lesson: build in dependency order, or the flow will look connected and still fail to pass values.

6. Unconnected downstream modules
   - What happened: a run succeeded with 11 operations and 5 companies updated, but tasks 0, Slack alerts 0, Sheet rows 0, because Task, Slack, and Sheets were on the canvas but unconnected. The flow ended at the Tools module.
   - Lesson: a success status does not prove the whole flow ran. Count tasks, alerts, and rows, not just the run status.

7. Date string versus timestamp
   - What happened: HubSpot rejected Last Alert Sent as "10/26/2026 was not a valid long". It expects a Unix timestamp in milliseconds.
   - Fix used: 1792972800000 for October 26, 2026. This is a hard rule for this system: never put a date string back into Last Alert Sent.

8. Missing Due Date
   - What happened: HubSpot Create a Task failed because Due Date is required.
   - Fix used: 1793059200000 for October 27, 2026.

9. Slack channel_not_found
   - What happened: Slack failed because the Make bot was not a member of the private test-client-alerts channel at that time.
   - Fix used: the bot was added to the channel in person. Lesson: a private channel alert is only as good as its membership.

10. Tools Set Variable emitting raw formula text
    - What happened: a Tools Set Variable with a nested if() formula output raw formula text in a HubSpot task note, with the company name substituted into its conditions, not the evaluated sentence. Re-typing the formula did not fix evaluation in that module.
    - Fix used: drop the Tools variable approach and use a Router, with one route per company and a static, non-variable sentence per route. Rules still decide who is flagged. AI, in this build the Claude prompt, only writes the explanation.

11. Two-Save trap
    - What happened: saving a module dialog does not save the scenario. The scenario toolbar Save is a second, separate step.
    - Lesson: a dialog Save without a toolbar Save can be lost if the tab is closed or reloaded.

12. Filter Value box not accepting automated typing, and filter variable as plain text
    - What happened: the Router filter Value box did not accept typed input through the browser agent, and the Kite filter variable was plain text "Company Name", not the [1. Properties: Name] pill. A literal "Company Name" never equals "Kite Realty", so Kite passed 0 bundles in the crossed run.
    - Fix used, and now saved: Kite filter variable is the [1. Properties: Name] pill, operator Equal to, Value Kite Realty, fallback No, Label Kite Realty. This was verified by a direct pill test that passed, then set for real and saved.

13. Route membership does not follow visual drag position
    - What happened: dragging a Task toward a different route changed only its visual position, not its route membership. The crossed run therefore ran Grove content on the Kite route, Kite content on the Juniper route, and Juniper content on a filterless 6th route, once per bundle.
    - Crossed run evidence, Run ID 3b989fe9125042b795b4f46b4dd6eaf4: Status Success, 32 operations, Sheet Alert Log 9 rows, all Date October 26, 2026, At Risk, with Juniper duplicated 5 times, Kite 1, Falcon 1, Grove 1, Harbor 1. Expected clean result was 5 rows, no duplicates.
    - Lesson: verify route membership in the Order routes dialog, not by where a box looks on screen. Delete and rebuild by membership, do not drag to fix a crossing.

14. Extra empty routes
    - What happened: clicking the plus inside the Router box adds a whole new route, not a module. The canvas now shows 9 route stubs: 1st to 5th populated, 6th to 9th empty. The 6th route modules were deleted and saved. The 7th to 9th stubs are empty placeholders that do not run, and their deletion was not attempted in the last run.
    - Lesson for the later fix: delete empty stubs if the editor allows it, otherwise record them as empty and do not add modules to them.

15. Browser agent canvas targeting failure (latest, Phase 5 parked)
    - What happened: in the last 45-minute continuation, the agent spent over 16 minutes trying to add a Grove Task. The canvas is zoomed out with no zoom control, plus buttons are tiny, and clicks landed on nearby filter chips. Visual targeting confidence was 0.42 to 0.55, ambiguous. One Task dialog did open, but configuring it needs 5 or more fields with pill insertions, about 10 minutes per module, so it was cancelled to try a duplicate approach, which also failed on targeting.
    - Lesson: this is a browser-agent canvas limit, not an account or platform rejection. A human can target those plus buttons far more reliably. This is why Phase 5 is parked for a human fix, not abandoned.

## What this note does not claim
- No new break tests were run in Phase 6.
- No clean end-to-end run is claimed. The crossed run above is a documented failure, not a clean result.
- No heartbeat was posted for a complete run, because no complete run was verified.

## Later fix list (Phase 5, parked)
Grove Task to add; Kite, Juniper, and Harbor modules to verify and fix; empty stubs to delete if possible; old HubSpot and Slack outputs to clean up with signed-in sessions; then a clean re-run, then filter restore. See Phase 7 for the full list.
