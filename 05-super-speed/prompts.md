# 05 · Super Speed — prompts

**Context:** You joined Rook two weeks ago as PM on Dispatch.
Release 4.2 shipped on 12 August, just before you arrived, and
landed badly. You've spent three sessions finding out what went
wrong: the two piles of feedback that didn't agree, the numbers that
hid four people inside an average, and the code that settled it —
the shorter timeout is what did it.

At the end of the session, type wrap up, and Claude Code saves the
prompts you wrote yourself below — not the starter prompt — updates
CLAUDE.md, and saves your work to GitHub. By Module 6 this file is a
prompt library built from your own questions.

---

### 1.

We're going to explore possible solutions for the long-standing issue where once a responder's score drops, it is very unlikely they will ever get enough calls to raise it again. Let me know if there's anything in the problem space you don't understand. Review project documentation of 04 and earlier if helpful.

We are going to first spitball solutions and scope at a high level, then write a product brief together. We will not move on to a prototype built from this brief until I say so.

Engineering could quietly revert the timeout and call it handled. Helen wants something that still works for the responder even with the faster window, built from their point of view. I think there could also be input from the handler at times - one example being to weigh in on any misses a responder had, e.g. "Oh that's legit and they shouldn't be punished for missing." Also we should work in a differentiation of turn downs vs misses.

If you have no questions here, start proposing possible features that are scoped for just the responder, for the responder and handler, and any data/logging improvements. Give me high level t-shirt sizes using anything you know based on what you might know about the working teams.

### 2.

Your questions-
1 - mark it out of scope for this but I don't want a restart to ever reset scores, regardless of what Wen Li says. "Scores should be rebuilt from the ping log" what? No. If we want scores to persist they should persist, not be rebuilt from some other resource.
2 - Do we have any way to actually know this, without introducing complex work? I'm open to ideas. You mention "opened-ping grace" and perhaps that's what you mean here?
3 - Full reversal.
4 - Yes some responders have no handlers. Perhaps those responders can submit requests with explanations on missed ones.

Keep numbering questions to make it easy to refer to them. Okay to reuse numbers with each new response.

I like what you've outlined overall. Keep in mind Helen seems to want to do more work, so we don't want anyone to see the first "no new UI" changes and make us stop before stuff for responders and handlers goes in.

### 3.

Everything sounds good except for one thing - I don't want the app to do a missed-ping follow-up, I want the responder to have to seek it out. I don't mean hide it or make it difficult, but it should be up to the responder to put the effort in, take responsibility.

1  - Yeah support lead's team. I say so.
2 - Yes comfortable.
3 - Leave for Helen/Marcus
4 - Fine in release A.

One last ask, if we can do anything real small (but possibly flashy) in the first release just to create some confidence that it's not all boring backend, let's consider that.

If you have everything you need, let's write the brief and lay all the other supporting stuff out. We'll move on to the prototype once I have a chance to review.

### 4.

One thing about auto-reversal. "misses with no opened event are reversed automatically." UNLESS they happen more than X% of all pings. I want X configurable at any point after the release without needing a release.

2 - move ahead with the miss penalty numbers you've chosen for now.
3 - I don't care about support's capacity. They will figure it out.

"Why did the four start missing in the first place?" - did we not figure this out already? If not do we need to before we move ahead with this work?

If we're good with everything, save the brief exactly as it stands now as 05-super-speed/brief.md. Show me the file when it's done.

### 5.

Generate the rest of the releases for the prototype, being sure it's clear which release is being viewed, being able to switch releases, and marking clearly or listing what changes per release. All in one prototype.html

### 6.

Make the release notes section expandable because I don't want it in my face all the time. Make the items on the list appear dynamically next to the phone or handler so we can see which are in play in each workflow.

### 7.

Show me this happening to Meteor Mite specifically, using her name, the way Kip would see it.

### 8.

Let's do another visual. I want to see a journey map of before and after for a few difference scenarios, also showing screenshots of the app or console or queue where appropriate. Not a clickable, a storyboard type approach

### 9.

I don't understand this "He is still on the stairs when it hits 0. Because he had already opened the ping, he gets 15 more seconds." if he is racing up the stairs to get his phone, how has he already answered the ping for it to get 15 more seconds?
