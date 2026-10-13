**To:** Marcus Oyelaran, Wen Li
**Subject:** Two data requests to find out why pings are being missed since 4.2

Hi Marcus, Wen,

I'm digging into the 4.2 problem (tickets about 3x normal, split between "phone never goes off" and "gone before I could answer"). I have what the `pings` table can tell me, and I've hit the limit of it. Could you help with two things?

**1. Response logs from the phone app (Marcus, or whoever owns the app)**
For each ping from 29 Jun to now: when the ping was *displayed* on the phone and when the responder *responded* (taken / turned down), plus whether the push was delivered at all.

Why: `pings` only has when a ping was sent. I can see that every turn-down happens within 40s, so the 60s wait seems long enough for people who answer, but I can't see how long accepts take, or whether the missed pings were ever shown. Display vs. answer times would settle whether the 90s to 60s cut matters.

**2. Routing scores for four responders (Wen)**
For The Undertow, Farlight, Vesper and Meteor Mite: their recent-acceptance score (`history.py`), week by week from early Aug to now, and their rank position on the callouts they were pinged for. If you can share the same for two or three responders who weren't affected, that gives me a comparison.

Why: these four went from about 12 pings a week to 1–6, with misses at 53–64% against about 15% for everyone else. I suspect a miss lowers the score by 0.12, the score floors at 0 and never recovers, and the 4.2 weight change (acceptance 0.40 to 0.25, proximity 0.45 to 0.60) makes it worse. I'd rather check than assume.

If either is hard to pull, even a sample or a couple of days would help. And if you already know why those four miss so much more than everyone else, I'd like to hear it.

Thanks,
[your name]

---
*Open question for the same conversation:* Marcus asked on 14 Aug whether the proximity change treats chronic decliners differently on purpose. I'd like to answer it once the scores are in.
