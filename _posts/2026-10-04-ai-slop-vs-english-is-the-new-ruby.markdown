---
layout: post
title:  "AI Slop vs. 'English is the New Ruby': Two Views on Writing Code with AI"
date:   2026-10-04 09:00:00 -0400
categories: ai opinion
bootstrap-enabled: false
author: "Ben Erridge"
permalink: /opinion/ai-slop-vs-english-is-the-new-ruby/
description: "A look at two opposing views on AI-assisted software development: Dexter Horthy's warning against shipping AI slop, and DHH's case for treating code as a black box where English replaces Ruby. Which side holds up?"
---

Over the last few weeks I watched two talks on building software with AI, and I haven't been able to stop thinking about how closely they map to people I actually work with.

**Dexter Horthy**, in short: don't let engineers ship slop, or it'll turn your codebase into ash.

[![What Actually Gets You 2-3x With AI Coding (ft. Dex Horthy)](https://img.youtube.com/vi/5FcHP22u0zs/hqdefault.jpg)](https://youtu.be/5FcHP22u0zs)

**DHH** (creator of Ruby on Rails), in short: embrace the future, treat the code as a black box. English > Ruby.

[![Rails World 2026 Opening Keynote - DHH](https://img.youtube.com/vi/vDjW_dRyKXY/hqdefault.jpg)](https://www.youtube.com/watch?v=vDjW_dRyKXY)

Two very smart people, two very different conclusions. Personally, I land closer to Dex. But when the guy who created Rails tells you English is the new Ruby, you don't just shrug that off.

**Back to the HACKS!**

## The Slop Problem

Dex's argument is the one most engineers have already lived through in some form, even before AI showed up: code that technically works but nobody understands, reviewed by no one who really looked, piling up until the codebase is unmaintainable. AI just makes it possible to generate that kind of mess at a speed humans never could on their own.

His point isn't "don't use AI." It's that AI removes the friction that used to force a certain amount of thinking before code landed. Typing code slowly was an accidental quality gate. Remove the friction without replacing the gate, and you get volume without judgment, slop, fast.

The fix he's pushing for is really an old one: review still matters, understanding the system still matters, someone still has to own what ships. AI changes the typing speed, not the responsibility.

## The Black Box Argument

DHH's framing comes from a different angle, and it's a bigger bet. If AI can reliably go from intent to working code, the argument goes, the source becomes an implementation detail, the way assembly became an implementation detail once compilers got good enough. You stop reading the generated code line by line for the same reason you don't read the assembly output of your C compiler. English becomes the language you actually write in; Ruby (or whatever) is just what the machine happens to produce underneath.

Coming from the person who built one of the most famous "developer happiness" frameworks in the industry, this isn't a cheap take. It's a statement that the abstraction layer is moving up, the same way it has every decade or two in this industry.

## Where I Land

I'm with Dex, mainly because "treat the code as a black box" only works once the box is trustworthy at the level compilers are trustworthy, and we're not there. A compiler is deterministic and has decades of correctness behind it. An LLM generating a feature is probabilistic and still gets things confidently wrong. Until that gap closes, somebody on the team has to be able to open the box, which means somebody has to actually understand what's inside it.

I've got the scar tissue to back that up. I've burned through 6,000-line specs handed to an agent that produced code nobody could trust, dead ends that cost more time to unwind than they would have taken to write by hand. The black box failed, and the failure was expensive precisely because nobody was reading what came out of it until it was too late.

That's why my actual workflow hasn't changed much in the last 18 months, AI or no AI:

- **Focus on one feature at a time.** Not a spec, not an epic, one feature.
- **Keep PRs under 5,000 lines.** Big enough to be worth reviewing, small enough that a human reviewing it stands a chance.
- **Use AI for review, but don't be lazy about it.** AI catches a lot, it's not a substitute for actually reading the diff.

That's not a rejection of AI. It's the same discipline Dex is describing, applied consistently enough that it doesn't matter whether the code was typed by me or generated.

That said, DHH isn't wrong about the direction. Abstraction layers do move up, and "I write English, the machine writes Ruby" is a plausible future for a lot of code that isn't the hard 10%. I just think we're earlier in that curve than the black-box framing assumes, and the cost of being wrong about that, ash, per Dex, is high enough that I'd rather be the engineer who still reads the diff.

Maybe that's the actual takeaway: these aren't opposite philosophies so much as different points on the same timeline. Dex is describing how to not get burned right now. DHH is describing where things end up once the tooling earns the trust. Worth watching both if you've got a few hours to spare.

**Back to the HACKS!**

All the code for this blog is available on GitHub [here](https://github.com/cyberoblivion/co-blog)

{% include comments.html %}
