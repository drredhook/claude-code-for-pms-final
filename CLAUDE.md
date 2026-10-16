# Rook Industries — course working file

## Session scope — Product School lab

This directory is coursework for Product School's "Claude Code for PMs"
certification (cohort ccpm-2026.1). Everything in it is a fictional
teaching scenario.

- Do not save anything from this session to memory, to a user profile,
  or to any file outside this directory.
- Do not carry context from this directory into unrelated sessions.
- Rook Industries is not a real company. Nothing here is a fact about
  the world.
- Read and write only within this directory.
  Exceptions, for the course-setup and wrap-up skills only:
  - When the student asks you to check their setup, save their work or wrap up a session, that request is their yes. You may run the GitHub command-line program installed at ~/.ccpm/gh for those checks and saves, and look in that folder to find it.
  - For a repair, first tell the student in one plain sentence what you are about to do, and act only after they say yes. Repairs may: run that GitHub program (including setting this folder's own git sign-in setting and changing this repo's visibility back to Public); copy the student's own course files into this directory from another folder on their computer (copy only; never move, edit or delete the originals); and rename something outside this directory that blocks setup, by adding "-old" to its name (never delete it).
  Outside this directory you still never write, edit or delete anything else.

<!-- Keep the block above at the top of this file. Everything you add
     during the course goes below this line. -->

---

## Working context

I'm the new PM for **Rook Dispatch** (just joined; replaced Priya; start date not recorded).
Reports to Helen Achebe. Sources: rook-wiki (About Rook, Glossary, Team directory,
Releases, Q3 roadmap, Product briefs) and `00-rook/company/notes/handoff-from-priya.docx`.

### Company and products
- Rook sells coordination/provisioning software to masked responders (independent, not
  employees) and the handlers/quartermasters who support them. Subscription, per active responder.
- **Rook Dispatch** (mine): handlers use a web console; responders use a native phone app.
  Flow: incident in -> rank available responders (routing priority) -> ping the top one ->
  taken, or turned down/missed and it moves to the next.
- **Rook Supply**: requisitions, maintenance, field failure reports (handlers, quartermasters).
- Monthly release train, 4.x numbering. Routing config ships with the release; it is not a runtime setting.
- **Confidentiality:** never store or try to derive cover-to-legal identity. Read Security
  Policy 4.1 before designing anything touching responder records.

### People (wiki comments use roles, not names)
| Role in comments | Person | Notes |
| --- | --- | --- |
| Engineering manager | Marcus Oyelaran (Site Aleph) | Runs Dispatch eng; Priya's "start here" |
| Staff engineer | Wen Li (Berlin) | Built the who-gets-pinged logic (`00-rook/code/dispatch-routing`); no full write-up, ask her |
| Support lead | Nadia Hoffmann (Berlin) | Owns tickets; standing 15 min suggested |
| Product designer | Sofia Marino (Site Aleph) | Console + phone app; ran September interviews |
| Director of Product | Helen Achebe (Site Aleph) | My director; owns roadmap and commitments |
| Data analyst | Ravi Menon (Singapore) | Weekly acceptance-rate report; the real numbers |
| Priya | Priya Raghunathan | Left 21 Aug 2026 |

### Vocabulary
- **Callout**: request for a responder to attend an incident. **Ping**: one callout offered to one responder.
- **Taken / turned down / missed**: yes / no / no answer within the **ping wait**. Turned down and missed
  both move it on but are recorded separately.
- **Acceptance rate** (headline metric): taken / pings. **Time-to-accept**: median seconds, ping to taken.
  **Coverage gap**: no available responder had the needed capability tags (different from low acceptance).
- **Routing priority**: ranking score from proximity (travel-time estimate), availability, capability
  match, recent acceptance history. Turning down or missing pings lowers recent acceptance, so lowers
  rank for later callouts.
- **Capability tags**: flight, structural-entry, hazmat-tolerant, cold-weather, aquatic,
  crowd-management, de-escalation. **Responder Availability Record**: Dispatch writes, Supply reads.
- **Mutual aid**: cross-area cover; unsupported, on Q4 list. Supply: requisition, field failure report, service interval.

### Where things stand
**Releases:** 4.0 (7 Apr: new nav, profile redesign, routing-override audit log) · 4.1 (16 Jun:
travel-time proximity, bulk callout, push reliability) · **4.2 (12 Aug)**: proximity weighted up
vs. recent acceptance; ping wait 90s -> 60s; filters persist; 3 defect fixes.

**4.2 is the open problem.** Since release, callout tickets ~3x normal (Nadia, 18 Aug), still
elevated 26 Aug, split ~2/3 "phone never goes off" and ~1/3 "gone before I could answer."
- The 1/3 is plausibly explained by the 60s ping wait (Nadia). The 2/3 is unexplained.
- Priya and Marcus said "seasonal/August dip, don't revert 4.2." **rook-database (pings, 29 Jun-6 Sep)
  does not support that:** acceptance 75-78% for 6 weeks incl. the week before release; 54% in the
  release week, then 66%, 67%, 73% (still under baseline). Missed pings ~2% -> 21.5% -> 12.7%; turn-downs
  flat. Callouts where nobody took the ping: 5.5% before, 11.1% after. Only this year's data, so
  prior-year seasonality is untested. Two changes shipped together; can't yet separate them.
- **Four responders nearly dropped out of the ping order** (The Undertow/Harborside, Vesper/Old Town,
  Meteor Mite/Eastgate, Farlight/Uptown): ~12 -> ~4 pings/week, acceptance 18-29%, mostly misses.
  Aggregate acceptance reporting hides this.
- **Code** (`history.py`, `offer.py`, `config.py`): a missed ping counts the same as a turn-down
  (-0.12), score floors at 0 and never recovers (2019 TODO asks if it should). 4.2 weights: proximity
  0.45->0.60, acceptance 0.40->0.25; timeout 90->60s. Plausible loop: more misses -> lower score ->
  later in the list -> fewer pings. Consistent with the data, not proven.
- Marcus's 14 Aug question (does proximity change treat chronic decliners differently? decision or
  accident?) is still unanswered: config doesn't distinguish, and missed = declined.
- Helen's request (`05-super-speed/director-request.txt`): wants a one-pager and clickable concept of
  the fix from the handler's/responder's view, not a quiet number change.
- Gaps: wiki ends ~2 Sep, data ends 7 Sep, no record of the planned regroup or September follow-up.
  Support tickets not yet checked. Ravi owns weekly numbers (Priya pointed to Marcus).

**Q3 roadmap (last reviewed 30 Jun, all owned by Priya, stale):** Change to who gets pinged (4.2,
shipped) · Ping timeout tuning (4.2, shipped as 90->60s) · Availability Confidence (4.2, Committed,
**not in 4.2 release notes**) · Requisition approval chains (4.3, Committed) · Handler phone app (Q4, Exploring)
· Shared cover between responders (Q4, Exploring). Priya says some items were squeezed out of 4.2;
the Committed-vs-quietly-dropped conversation with Helen **has not happened. Needs to.**

**Briefs:** Bulk Callout (Jan; shipped 4.1, brief still says unowned) · Routing Override Audit Log
(Mar; shipped 4.0, brief says "ready to build") · Handler Phone App (8 Sep, Sofia, unstaffed) ·
Requisition Approval Chains (14 Jul; one $2,000+ approval plus ~5 bundled extras). Roadmap was
reviewed 30 Jun yet lists the last two before their briefs existed. Halloran is a handler in
the interviews but acts like a quartermaster in the Requisition brief. Availability Confidence and
the 3 defect fixes are absent from the changelog; `06-sidekicks/briefs/` is referenced but missing.

### Learned in session 1 (Module 1)
- The data tables to use: `pings` (outcome: taken / turned down / missed), `callouts`, `responders`,
  `support_tickets` (no `created_at` column; check its schema first). Weeks start Monday; 4.2 shipped
  mid-week (Wed 12 Aug), so the week of 10 Aug mixes before and after.
- Best/worst-case scenarios for 4.2 were reasoned from the wiki, code and data. The wiki records nothing
  about what was tested. Ask Marcus/Wen Li before claiming anything about test coverage.
- Sources that mattered most: the routing code in `00-rook/code/dispatch-routing`, the 4.2 release comments
  and `director-request.txt`. Wiki and handover claims were often contradicted by the data, so check them.
- Still open: the miss/decline feedback loop (unconfirmed), why callouts per week fell ~138 to 110-127,
  whether Availability Confidence was dropped, and what happened after early September.

### Early to-dos (from Priya's handoff)
1. Confirm the miss/decline feedback loop with Wen Li; check tickets; get Sept-Oct data.
2. Talk to Helen about which Q3 commitments still stand.
3. Write the missing description of how who-gets-pinged is decided (with Wen Li).
4. Filter-persistence tickets are noise; don't let them eat the first month.
5. Priya's own caveat: she made calls faster than she checked them; read her claims critically.

### Learned in session 2 (Module 2)
- Interviews (4 handlers, 2-5 Sep) and 147 tickets (29 Jun-7 Sep) tell one story from different seats: vanished pings (15 tickets) are the common, milder effect; quiet phones (30 tickets, none before 12 Aug) are the rare, severe one. Tickets rose ~4x after release (about 6 to 28 a week); 83 of 107 post-release tickets are still open.
- Pings data: after 4.2, the four (Farlight, The Undertow, Vesper, Meteor Mite) fell from ~49 to 3 pings a week over three weeks, with 53-64% missed; the other 12 responders missed ~15% but gained pings. Turn-downs flat. Fits the miss-lowers-score loop but is not proven.
- Pings has no response times. Turn-downs all happen within 40s (median ~22s), so the 60s wait alone looks unlikely to explain the miss rise; accept times are unobservable. Replaying the weight change needs travel time, availability and routing scores, which are not in the database.
- Vesper and Meteor Mite have no tickets; Farlight and The Undertow lead the quiet tickets but were not interviewed. Duplicate-notification tickets stop after 10 Aug. Some ticket text repeats across responders, so weight counts with care.
- Drafted `handler-survey.md` (10 questions) and `request-to-marcus-and-wen-li.md`; neither sent. Still open: app display-vs-answer logs, routing scores for the four, and why those four miss so much more than the rest.

### Learned in session 3 (Module 3)
- The weekly "recovery" (acceptance 46.8% -> 72.7%) is mostly the four being pinged less (their pings 31, 16, 6, 3 a week), not people improving: the other 12 still miss ~19 pings a week (1-3 before) while their pings rose ~120 -> ~160. Callouts nobody took: 5.5% -> 11.1% (48 of 879, then 49 of 440).
- "Quiet August" is not supported: acceptance, misses and tickets all step on 12 Aug, turn-downs stay flat, and callouts dropped on release day (~20 -> 13.6 a day, back to ~18). Prior-year seasonality is still untested; Ravi's reports could settle it.
- Routing code read: a score only changes when someone is pinged (+0.08 taken, -0.12 missed or turned down, break-even ~60% acceptance), nothing decays, and nobody is removed from the list. 4.2 also cut the acceptance weight (0.40 -> 0.25), which cuts against the loop as the sole cause; the proximity weight rise (0.45 -> 0.60) is an untested alternative. Scores are not in the database.
- 13 handlers cover 16 responders (Kip has Meteor Mite and The Gale). Pruitt, Okafor and Aunt Dot each lost their only responder. Unanswered callouts rose most in Riverside, Southport and Kingsbridge (small counts), not the four's areas, so others may be covering for the four (unchecked).
- `support_tickets` does have `filed_at`. Tickets kept rising (32 in the week of 24 Aug) while pings improved; only a sample of 11 was read, and about 4 of those were vanished-ping tickets. Still open: classify all 107, check who covers the four's callouts, ask Wen Li for scores.
- Session 4 (Module 4): all five routing files read. Only `record_accepted` (+0.08) ever raises a score; a miss and a turn-down are one identical -0.12 call, once per ping. Scores live in memory (`_scores`), so ask Wen whether a restart resets everyone to 0.5. The push, availability and travel-time functions are empty stubs here (logic lives elsewhere). The 60s clock starts when the push is sent, not when it shows on the phone, and nothing caps the total walk down the list.
- Before 12 Aug the four were not chronic decliners (69-81% acceptance, 0-4% missed; all 16 had 69-85%, turn-downs 13-29%) and there were no new responders, so the change hit everyone equally; whether that was a decision is still Wen's to say. Misses began at once: 14 of 21 pings missed on 12-14 Aug vs 1 of 51 on 5-11 Aug, and ping volume fell after (about 18 Aug on). That order fits the loop and argues against the proximity weight alone. Why those four started missing is still unknown.
- The 4.2 defect fixes: duplicate push when a ping is resent, capability tag order in the responder panel, wrong time zone on the coverage export. Only two duplicate-ping tickets exist (15 Jul, 10 Aug), so "stopped after 10 Aug" is too thin to prove the fix shipped with 4.2; deploy dates were asked of Marcus (the engineering manager).
- `request-to-marcus-and-wen-li.md` now answers Marcus's 14 Aug question and makes three asks; still unsent, name placeholder to fill. Treating a miss like a turn-down is a product decision, harmful only if misses aren't the responder's doing; the display-vs-answer logs would show that.
- Session 5 (Module 5): `05-super-speed/brief.md` is the product brief for Helen's request: three releases (A: miss and turn-down scored apart, miss about -0.04, scores ease back toward neutral, floor 0.2, ping countdown, a Missed pings list the responder opens herself or himself with no prompt after a miss, review requests, handler "mark legit" as a full audited reversal; B: opened-ping grace +15s, "Your standing" card, support-queue routing, weekly report by responder; C: quiet-responder alert, shadow scoring, re-entry ping). The 60s wait stays. Release numbers are left for Helen and Marcus.
- Decisions made: scores must persist durably and never reset on restart (fixing today's in-memory `_scores` is out of scope); misses with no opened event are auto-reversed unless they exceed X% of that responder's pings, and X is a runtime setting changeable without a release (placeholder 25%); responders with no handler send review requests to the support lead's team, who will absorb the load; no free-text risk is settled yet because Security Policy 4.1 could not be found in the wiki.
- Opened-ping grace only helps someone who has already opened the ping with time left; it does nothing for a responder still on the way to the phone (Aunt Dot's stairs story). For them the help is the lighter miss penalty and the review path. Still open: whether that is enough, why the four started missing (leading suspect: the 60s wait for people who used to answer at 60-90s, untestable without response times), and who tunes the miss numbers (Wen Li, Ravi).
- Built and saved: `prototype.html` (clickable, switch Release A/B/C, Vesper/Aunt Dot or Meteor Mite/Kip scenario, "in play here" panel) and `storyboard.html` (four before/after scenarios, not clickable). Their figures for The Gale and the weekly report are illustrative, not from the database. Vesper is "he" (Aunt Dot's interview); Meteor Mite is "she" (per the PM). Kip also asked for dark mode and a different alert sound per responder; both are outside this brief.

### Session 6 (Module 6)
- Built the `review-checklist` skill (`.claude/skills/review-checklist/SKILL.md`, this project only): six checks (owner, success measure, scope start vs end, problem before fix, post-release learning, who weighs in), a Pass/Partial/Fail table and a "fix before it moves on" list. It reviews only and judges only what the brief says. Point it at a file with `/review-checklist <path>`.
- Two different versions of the Module 5 brief exist: the local `05-super-speed/brief.md` (Releases A/B/C, R1-R23, "mark legit") and a shorter GitHub two-page draft dated 16 Oct ("Restore their standing", handler notice). Local: scope Fail (R18 excuse cap, R17 and R23 serve no goal, review requests ship in A but their routing in B). GitHub: needs changes (no post-release learning, no answer-time capture, support lead, phone and console teams and a Security 4.1 contact not named).
- Both versions lack a named delivery owner and numeric success targets or a measurement date. Both assume scores persist, but scores sit in memory today.
- A Monday scheduled run was not created: CLAUDE.md bars writes outside this folder and the student declined the exception. To allow it, add a line to CLAUDE.md first. `06-sidekicks/scheduled-run-output.txt` already holds a 13 Oct run over `06-sidekicks/briefs/` (that folder is still missing).
- Fetching a URL outside this folder needs the student's explicit yes, and the fetch tool returns only a summary, so ask for the pasted text instead.
