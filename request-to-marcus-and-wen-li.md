**To:** Marcus Oyelaran, Wen Li
**Subject:** Three data requests to find out why pings are being missed since 4.2

Hi Marcus, Wen,

I'm digging into the 4.2 problem (tickets about 3x normal, split between "phone never goes off" and "gone before I could answer"). I have what the `pings` table can tell me, and I've hit the limit of it. I have a few things for you, starting with an answer to Marcus's question.

**First, Marcus, an answer to your question** about whether the change to who gets asked first applied to responders who'd already been turning jobs down, or just new ones. I checked it against the code and the `pings` table:
- It applied to everyone equally. The weights in `config.py` are global and `routing.py` runs the same formula for every responder.
- There were no new responders in the data (all 16 have at least 39 pings before 12 Aug), and no chronic decliners either. Before 4.2, turn-downs were 13-29% for everyone and acceptance was 69-85%.
- The four who nearly dropped out (The Undertow, Vesper, Farlight, Meteor Mite) were ordinary before the release, at 69-81% acceptance and 0-4% missed. After it, misses were 53-64%.

So the premise doesn't quite match what happened. What I can't tell is whether applying it to everyone was a decision or just how it fell out. Wen, that one is yours when you have a minute.

Three things would help me go further:

**1. Response logs from the phone app (Marcus, or whoever owns the app)**
For each ping from 29 Jun to now: when the ping was *displayed* on the phone and when the responder *responded* (taken / turned down), plus whether the push was delivered at all.

Why: `pings` only has when a ping was sent. I can see that every turn-down happens within 40s, so the 60s wait seems long enough for people who answer, but I can't see how long accepts take, or whether the missed pings were ever shown. Display vs. answer times would settle whether the 90s to 60s cut matters.

**2. Routing scores for four responders (Wen)**
For The Undertow, Farlight, Vesper and Meteor Mite: their recent-acceptance score (`history.py`), week by week from early Aug to now, and their rank position on the callouts they were pinged for. If you can share the same for two or three responders who weren't affected, that gives me a comparison.

Why: these four went from about 12 pings a week to 1–6, with misses at 53–64% against about 15% for everyone else. I suspect a miss lowers the score by 0.12, the score floors at 0 and never recovers, and the 4.2 weight change (acceptance 0.40 to 0.25, proximity 0.45 to 0.60) makes it worse. I'd rather check than assume.

**3. Deploy history for the duplicate-ping fix (Marcus)**
The 4.2 notes list a fix for a duplicate push notification when a ping is sent again. Could you tell me the date that fix was merged and the date it reached production, plus any deploys to the push-notification service between 10 and 12 Aug?

Why: the last two duplicate-ping tickets I can find were filed 15 Jul and 10 Aug, and none since. That may just mean the fix worked after 4.2 shipped on 12 Aug, but two tickets is a thin base and I'd like to confirm the fix went out with the release and not before it. I'm only trying to line the ticket dates up with the release record.

If either of the first two is hard to pull, even a sample or a couple of days would help. And if you already know why those four miss so much more than everyone else, I'd like to hear it.

Thanks,
[your name]

---
*Note to self:* the 14 Aug question is now partly answered in the email above (everyone, equally, and no one fit either description). What is still open is whether that was intended.
