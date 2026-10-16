# Product brief — Getting quiet responders back into the ping order

**Product:** Rook Dispatch · **Author:** Dispatch PM · **For:** Helen Achebe (Director of Product)
**Status:** DRAFT for PM review. No prototype yet. Not shared.
**Answers:** `director-request.txt` (Helen asked for a one-pager built from the point of view of the person it happens to, something a handler like Kip would notice, and something a quiet responder would feel differently about).

---

## 1. One-pager

**The problem, in one line.** Since 4.2, some responders get pinged far less, miss most of what they do get, and have no way back. Their score drops, the score pushes them later in the list, later in the list means fewer pings, and fewer pings means no chance to raise it.

**What we know (from our own data).**
- The week of 4.2, acceptance fell from 75–78% to 54%. It recovered to 73%, but the recovery is mostly four responders (Farlight, The Undertow, Vesper, Meteor Mite) being pinged less (about 49 pings a week down to about 3), not people improving. The other 12 responders still miss about 15% of pings.
- A missed ping and a turned-down ping are treated as one identical penalty (−0.12). Only a "taken" ping (+0.08) raises a score. Nothing decays, and the floor is 0. A 2019 code comment asks whether scores should ease back; nobody answered.
- Callouts that nobody took rose from 5.5% to 11.1%. Callout tickets rose about 4x.
- This fits a feedback loop but the loop is **not proven**. We have no response times and no stored routing scores. Two changes (proximity weight and 60s wait) shipped together.

**Who this is for.**
- **Responders who have gone quiet** (Vesper is the example): independent, not employees, with a phone that rarely rings and, when it does, a ping that vanishes.
- **Handlers** (Kip, Aunt Dot) who see only a quiet phone and a worried responder, with no tool to tell the system "that miss wasn't their fault".

**What changes for them.**
- *For the responder:* a ping shows how long is left. Misses are judged differently from turn-downs. A scored-down responder can recover. A responder can look at their own recent misses and ask for one to be reviewed.
- *For the handler:* the console shows their responder's missed pings and lets them mark one "legit", which fully reverses it. It is logged.
- *For both:* the 60s ping wait stays. Nobody has to revert 4.2.

**What it deliberately does not do.** It does not revert the 60s wait. It does not remove anyone from the list or guarantee any responder a number of pings. It does not map a cover identity to a legal one. It does not add mutual aid. It does not push the responder to explain misses; they come to it themselves.

**What Helen is being asked for.** Commit to the three-release sequence in section 7, not only the first. The first release alone looks complete and isn't.

---

## 2. Background and evidence

| Claim | Basis | Confidence |
|---|---|---|
| Acceptance, misses and tickets all stepped on 12 Aug | rook-database `pings`, `support_tickets` | High |
| Turn-downs stayed flat while misses jumped (about 2% to 21.5%, then 12.7%) | `pings` | High |
| Four responders fell from about 49 to about 3 pings a week, 53–64% missed | `pings` | High |
| The four were not chronic decliners before 12 Aug (69–81% acceptance, 0–4% missed) | `pings` | High |
| A miss and a turn-down are the same −0.12 call | `offer.py`, `history.py` | High (read the code) |
| Nothing raises a score except `record_accepted` (+0.08) | `history.py` | High |
| The miss-lowers-score loop is the main cause | Consistent with data; the acceptance weight was also cut 0.40 → 0.25, which cuts against it | **Unproven** |
| The 60s wait explains the "gone before I could answer" tickets | Support lead's hypothesis; turn-downs all happen within 40s, accept times unobservable | **Unproven** |
| Why those four started missing | Unknown | Open |

Voice of the person it happens to: Aunt Dot (handler for Vesper, 3 Sep): her responder runs down the stairs, "by the time he's actually got a thumb on the screen — it's gone," and there are also "stretches where the phone just sits there." She also asked for something that tells her, not just him, when a ping has come in.

---

## 3. Goals and non-goals

**Goals**
1. A responder who has been scored down can climb back without needing a lucky ping.
2. A miss is no longer treated as a refusal.
3. A responder can still answer a ping inside the faster 60s window, and can see that window.
4. A handler, or support where there is no handler, can undo a miss that wasn't the responder's fault.
5. We can see, per responder, how many pings they get, when they were opened and how they were answered.

**Non-goals (deliberate)**
- Revert or lengthen the 60s ping wait. (Opened-ping grace in Release B adds time only when a ping has been opened.)
- Guarantee a minimum number of pings for anyone.
- Remove anyone from the ping order, or add a penalty for chronic decliners. (Marcus's 14 Aug question about chronic decliners stays a separate decision.)
- Build mutual aid ("Shared cover between responders") or change the Responder Availability Record.
- Identity of any kind: no mapping between cover identity and legal identity (Security Policy 4.1; see section 9).
- Push the responder to explain misses. Review is responder-initiated, by design.
- Fix the in-memory `_scores` store itself. See the requirement in section 5, which is a constraint, not a work item for this project.

---

## 4. Users and scenarios

**Vesper (responder, scored down).** Pinged about 3 times a week. Her handler, Aunt Dot, is the only handler for her. When a ping arrives she is slow to the phone.
- *Now:* the ping goes by, counts as a −0.12, she drops further, and the quiet continues.
- *After:* the ping screen shows time left. If she opens it late and still misses, it's recorded as a miss, not a refusal, and costs far less. A bad month eases off on its own. If one miss wasn't her doing, she opens "Missed pings" in the app and asks for it to be reviewed.

**Kip (handler for Meteor Mite and The Gale).**
- *Now:* he sees a quiet responder and can't say why.
- *After:* the console shows each responder's recent missed pings and whether each was opened. He can mark one "legit", which reverses it completely.

**A responder with no handler.** Submits the review request herself. It goes to the support lead's team.

**Responder who simply does not answer.** Keeps being penalised, at the lower rate for misses, and keeps the same path back as everyone else. Turn-downs keep the full penalty.

---

## 5. Requirements

### Scoring and routing
- **R1.** Record **missed** and **turned down** as separate outcomes in the scoring path. (Both are already separate in `pings`; `history.py` flattens them.)
- **R2.** A miss costs less than a turn-down. *Starting hypothesis, to be tuned by Wen Li and Ravi using shadow scores:* miss ≈ −0.04, turn-down stays −0.12. Not a final number.
- **R3.** Scores recover without needing a ping: ease toward neutral (0.5) over time. Rate to be set; this answers the 2019 TODO. Design note: Wen's "against" case (somebody who stopped taking work shouldn't recover on their own) is handled by easing only up to neutral, never above.
- **R4.** Raise the score floor above 0 (hypothesis: 0.2).
- **R5.** Scores are held in **durable storage and are never reset by a restart or deploy.** This is a constraint on the design. Fixing today's in-memory store is outside this project.
- **R6.** The scoring changes are *config + code* in the release, consistent with how routing config ships today. The single exception is the auto-reversal threshold X (R11), which is a runtime setting.

### Responder (phone app)
- **R7.** The ping screen shows a **countdown** of the time left on the ping wait (the server clock, so a ping opened at 20s shows 40s).
- **R8.** The app records an **opened** event when the ping screen renders (not when the app launches).
- **R9.** A **"Missed pings"** list is available in the app where the responder can find it. The app does not prompt after a miss. The responder chooses to open it. It is easy to find, not hidden.
- **R10.** From that list the responder can **request a review** of any miss: a fixed reason (didn't see it / wasn't available / phone problem / too far / other), plus an optional short note.
- **R11.** A miss with **no opened event** is reversed automatically, and shows in the list as "Reversed: the ping never reached your screen". **Unless** such misses exceed **X%** of that responder's pings (rolling window; initial X and window to be set by Wen Li and Ravi, working placeholder 25%). Above X, new no-opened misses are no longer auto-reversed and follow the normal review path (R10, R15, R16). **X must be changeable at any time after release without a release** (a runtime setting, unlike the rest of routing config). Changes to X are logged with who and when.
- **R12.** *(Release B)* **Opened-ping grace:** if the ping screen was opened before time ran out, add 15–20s to answer.
- **R13.** *(Release B)* A plain-language **"Your standing"** card: what changed, and what gets you back.

### Handler (web console)
- **R14.** The console lists each responder's recent misses with opened/not opened.
- **R15.** The handler can mark a miss **legit**, which fully reverses the penalty. The action is written to the existing routing-override audit log (shipped 4.0).
- **R16.** *(Release B)* Review requests from a responder go to their handler first. If the responder has no handler, they go to the support lead's team.
- **R17.** *(Release C)* A **quiet-responder alert**: pings per week for a responder fall sharply.
- **R18.** *(Guardrail)* A weekly cap on excuses per handler, and the pattern is visible to reviewers.

### Data and logging
- **R19.** Per ping: sent, opened, answered timestamps. The score before and after, the rank position, and the version of the weights.
- **R20.** Excuse and review events, with reason and who decided.
- **R21.** A **per-responder** weekly report (pings per week, taken/turned down/missed, opened), not only the aggregate. For Ravi.
- **R22.** *(Release C)* **Shadow scoring:** compute the proposed score next to the live one and log both.

### Re-entry
- **R23.** *(Release C)* **Re-entry slot:** responders far down the list occasionally get a ping when they are near and capable. This changes who is asked and needs Marcus and Wen's agreement.

---

## 6. The small, visible piece in Release A

**The ping countdown (R7).** It is small. It is the first thing a responder sees change, and it speaks to the 60s complaint directly: they can see how long they have. It needs the opened event (R8), which we need anyway. It is honest: it shows the time actually left, not a restart.

Alternatives considered, not chosen:
- A live "Pinged just now" notice on the handler console. This is Aunt Dot's request. It is a good candidate for a later release.
- A softer "standing" card in A. Too early: the scoring rules are not stable yet.

---

## 7. Release plan (release numbers left for Helen and Marcus)

| | Release A | Release B | Release C |
|---|---|---|---|
| **Engine** | R1–R6: miss/turn-down split, recovery, floor, durable scores | R12 opened-ping grace | R22 shadow scoring, R23 re-entry slot |
| **Responder sees** | R7 countdown, R9 Missed pings list, R10 review request, R11 auto-reversal | R13 Your standing card | |
| **Handler sees** | R14 misses list, R15 mark legit | R16 review routing (handler, else support) | R17 quiet-responder alert |
| **Data** | R8, R19, R20 | R21 | R22 |

**Why one sequence.** The engine change is small enough that engineering could ship it in an afternoon. If A ships alone it looks like the whole answer, and Availability Confidence has already slipped out of 4.2 without a record. B and C need a named owner and target release on the roadmap.

**T-shirt sizes (first guess, no capacity confirmed).** A: M–L. B: M. C: M–L. Dependencies: Wen Li (routing), phone app team (opened event, countdown, list), console team, support lead's team (review queue), Ravi (report, shadow data), Sofia (design).

---

## 8. Success measures

**Primary**
- Pings per week for the lowest-scoring responders return toward their pre-4.2 level (the four: about 49 a week before, about 3 now).
- Callouts where nobody took the ping return toward 5.5% from 11.1%.

**Secondary**
- Share of misses with an opened event (a sign of how many are genuinely unseen).
- Count of review requests and excuses, and what share were reversed.
- Tickets of the "phone never goes off" type fall; weekly ticket volume falls from the post-4.2 level (about 28 a week).

**Guardrails (must not get worse)**
- Time-to-accept.
- Coverage gaps.
- Acceptance rate excluding reversed misses.
- Excuses per handler (cap in R18).

**Baseline caveat.** We have only this year's data. Prior-year seasonality is untested; Ravi's reports could settle it.

---

## 9. Risks and open questions

| # | Item | Owner | Notes |
|---|---|---|---|
| 1 | Does the loop actually cause the quiet? | Wen Li, Marcus | Scores not in the database. Shadow scoring (R22) and per-ping score logging (R19) will show it. |
| 2 | Why did the four start missing in the first place? | Wen Li, Marcus | Still unknown, and not a blocker. Before 12 Aug they were not chronic decliners (0–4% missed at the 90s wait), and misses began at once (14 of 21 pings missed 12–14 Aug vs 1 of 51 the week before). That points at something in 4.2 (the 60s wait is the leading suspect, for responders who used to answer between 60 and 90s) but we have no response times to test it. Every requirement here works whichever cause is true. The opened and answered timestamps (R8, R19) will answer it; if the app already logs display-vs-answer times, asking for them sooner is cheap. |
| 3 | Do restarts reset scores today? | Wen Li | Out of scope here; R5 says never. |
| 4 | Miss and recovery numbers | Wen Li, Ravi | R2–R4 are the working numbers for now (decided), to be tuned later with shadow data. |
| 5 | Review queue capacity | Support lead | Support's team reviews; the PM has decided they will work out capacity. |
| 6 | Free-text note in R10 | Security / Policy 4.1 | I have not been able to read Security Policy 4.1 (the wiki search found nothing). Notes could drift toward identifying details. Check before design, or make notes fixed-choice only. |
| 7 | Handler excuses defeating the score | PM, Marcus | Mitigated by R15 audit log and R18 cap. |
| 8 | Responders gaming "never opened" auto-reversal | PM | Mitigated by the X% threshold (R11), which can be tightened at any time without a release. Also watch share of misses with no opened event (section 8). |
| 9 | "Opened" definition | Phone app team | Ping screen rendered, not app launched. |
| 10 | Release numbers | Helen, Marcus | Left blank. |
| 11 | Re-entry slot (R23) changes who is asked and could slow callouts | Marcus, Wen Li | Needs agreement; it could be a coverage tradeoff. |
| 12 | The 60s clock starts at push, not at screen | Phone app team | The countdown (R7) exposes this honestly; Release B grace partly addresses it. |

---

## 10. Decisions log (this session)

- Do not revert the 60s wait.
- Missed and turned-down are separate; a miss costs less.
- Scores persist durably and never reset on restart (out of scope to fix here).
- Handler excuse is a **full** reversal, audited.
- Responders with no handler submit review requests; the **support lead's team** reviews.
- Misses with no opened event are auto-reversed, unless they exceed X% of that responder's pings. X is a runtime setting, changeable without a release.
- Miss penalty numbers (R2–R4) stand as the working numbers.
- Support's capacity for review requests is not a concern for this brief.
- **No app-initiated follow-up after a miss.** The responder seeks out the Missed pings list and requests review, which is their effort and their responsibility. The list is easy to find, not hidden.
- Miss/turn-down split and recovery ship in Release A.
- Release numbers left for Helen and Marcus.
- One small, visible piece in Release A: the ping countdown.
- Prototype waits until the PM has reviewed this brief.
