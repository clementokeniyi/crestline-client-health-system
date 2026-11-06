# Claude explanation step — manual, for flagged accounts only

Test build only. Rules decide who is flagged. Claude only writes the reason.
This is a manual Claude step, not a Claude API call, because the free test scope has no paid API key. In production, this prompt would replace the manual step with an API call, with the same output rules.

## Version 1 (AI draft)

Write a reason why this account is flagged. Use the company name, owner, and last activity date. Make it sound helpful for an account manager.

## Version 2 (my edit — proposed edit for C to adopt before recording)

Write one plain-language sentence, under 30 words, using only Company Name, Test Owner, and Test Last Activity Date.
Say the account has had no recorded activity since [date], which is [days] days ago, and that it is flagged At Risk.
If Test Owner is blank, say Owner is unassigned, so this flag goes to the unassigned queue.
Do not guess why the client is quiet. Do not invent a publish date, a reply date, a contact, or a reason for the silence. If Test Last Activity Date is blank, say insufficient data instead of guessing.

Changes from Version 1 to Version 2, and why:
- Added a one-sentence, under-30-word limit, because an alert must be read at a glance.
- Added the exact fields allowed, because no other data exists in the test set.
- Added the unassigned-owner wording, because Kite Realty has no owner and must not disappear.
- Added a ban on guessing, because the test data contains no reason for silence.
- Added the insufficient-data rule, because a blank date must go to Needs Data, not to At Risk.

## Reasons generated with Version 2, for the 5 flagged accounts only

Measured from October 26, 2026. Test Last Activity Date only. No other reason is claimed.

1. Falcon Legal — Test Owner Sam Patel — October 3, 2026 — 23 days ago
   "Falcon Legal has had no recorded activity since October 3, 2026, which is 23 days ago, so it is flagged At Risk. Owner Sam Patel should check in with the client."

2. Grove Hotels — Test Owner Alex Morgan — September 28, 2026 — 28 days ago
   "Grove Hotels has had no recorded activity since September 28, 2026, which is 28 days ago, so it is flagged At Risk. Owner Alex Morgan should check in with the client."

3. Harbor Logistics — Test Owner Sam Patel — September 24, 2026 — 32 days ago
   "Harbor Logistics has had no recorded activity since September 24, 2026, which is 32 days ago, so it is flagged At Risk. Owner Sam Patel should check in with the client."

4. Juniper Travel — Test Owner Sam Patel — October 3, 2026 — 23 days ago
   "Juniper Travel has had no recorded activity since October 3, 2026, which is 23 days ago, so it is flagged At Risk. Owner Sam Patel should check in with the client."

5. Kite Realty — Test Owner blank — August 27, 2026 — 60 days ago
   "Kite Realty has had no recorded activity since August 27, 2026, which is 60 days ago, so it is flagged At Risk. Owner is unassigned, so this flag goes to the unassigned queue."

No reason was generated for Alpine Outfitters, Beacon Bakery, Cedar Dental, Delta Fitness, Ember Coffee, or Ivy Salon, because none of those accounts passed the At Risk filter.
