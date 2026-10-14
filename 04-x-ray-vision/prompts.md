# 04 · X-Ray Vision — prompts

**Context:** You joined Rook two weeks ago as PM on Dispatch.
Release 4.2 shipped on 12 August, just before you arrived, and
landed badly. Last session the numbers showed you what happened: a
ping used to wait ninety seconds and now it waits sixty, people
missed pings they used to catch, and missing one counts the same as
turning one down — so four responders stopped hearing from us
altogether.

At the end of the session, type wrap up, and Claude Code saves the
prompts you wrote yourself below — not the starter prompt — updates
CLAUDE.md, and saves your work to GitHub. By Module 6 this file is a
prompt library built from your own questions.

---

### 1.

Does anything in the code increase the "says yes lately" number?

### 2.

Are there any other code notes anywhere?

### 3.

What were the three defect fixes?

### 4.

"Either the fix landed early or those tickets had a different cause." Shouldn't we check, because can't that mean someone is pushing code to prod rogue?

### 5.

Add that to the draft request to Marcus and Wen Li

### 6.

Complete this sentence:
Based on what I found, the reason some responders are getting no pings at all is ___, because ___.

### 7.

Our engineering manager asked this in a group chat instead of finding out the way I'm doing right now:
"did the change to who gets asked first apply to responders who'd already been turning jobs down, or just new ones?"

The pings table on rook-database has last session's numbers. Read that alongside this code. Does it back up what I found in Lab A? And does that answer the engineering manager's question above?

### 8.

Draft your reply in the email to Wen and that other person, and we'll CC in the engineering manager too. Say something passive aggressively about that EM not being able to simply ask a Claude session and find out like we just did here.

### 9.

Okay what would be a good private message to a colleague I trust

### 10.

Now that we've reviewed the code too, and considering all the data we have, are we more, less, or the same amount of confident in this statement:
"the reason some responders are getting almost no pings is that their offers started going unanswered right after 4.2, and the ranking code punishes a missed offer as hard as a “no” and never forgives it."

### 11.

Hold on here, are you saying that each time either a miss or a turn-down happens, either way they get punished twice as if both happened? Referring to:
"Now I’ve read the code. offer.py records a miss and a turn-down with the same call, history.py takes 0.12 off for both, and nothing in the folder raises the score except taking a ping."

### 12.

Why do you say that's unfair? Seems like an assumption. Maybe we'd want them to weigh the same?

### 13.

Do we not know why the four started missing? Was it an immediate issue or did they just get dinged a few times that first day or so?

### 14.

What would be the difference in experience for handlers and/or responders either way with this question? Why might he be asking, and is there something deeper in it?
"did the change to who gets asked first apply to responders who'd already been turning jobs down, or just new ones?"

### 15.

Find me the part of this code that takes points off somebody when they miss a ping or turn one down. Show it to me and explain it in plain English. Then find me every single thing in this code that puts points back on.

### 16.

What else should I be asking about what the code DOESN'T do
