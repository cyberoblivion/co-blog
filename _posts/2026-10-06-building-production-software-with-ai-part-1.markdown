---
layout: post
title:  "Building Production Software with AI, Part 1: The Plan Is Where the Real Work Happens"
date:   2026-10-06 09:00:00 -0400
categories: ai howto
bootstrap-enabled: false
author: "Ben Erridge"
permalink: /ai/building-production-software-with-ai-part-1/
description: "Planning a big change to an old production system with an AI coding assistant, and how most of the first draft changed once I pushed on it."
---

I asked an AI coding assistant (Claude Code, running Opus 5.5) to build an implementation plan for adding a database to a mature production system. It produced a 37-story plan that looked finished. After a few days of challenging it, almost every important architectural decision had changed.

This post walks through those few days. It's the first in a series. Later posts will follow the same project into implementation and show where the plan held up and where it didn't.

## The setup

The system is a mature, high-throughput message router that runs on Linux and was written in C. It's been in production for a long time, and I've worked on it for just over a year. It runs on hundreds of hosts and has a newer C# layer growing around it. It handles regulated, sensitive data, so audit and data-handling rules matter a lot.

The real goal was bigger than any one feature: lay the foundation for a database in a system that had never had one, so everything built after it could stand on something solid instead of flat files. Four goals had to land together to make that foundation real:

1. Deploy our new standard API, one OpenAPI-based interface across all clients, to its first customer on a deadline.
2. Move sensitive message logging out of flat files and into Postgres.
3. Move configuration out of flat files and into Postgres.
4. Keep the operational changes from introducing a database as small as possible.

My first prompt was roughly: *"Make a plan and a ticket list given our goals, in order, so everything lines up and I can just knock out each one."*

I've also used the grill-me skill, which has the AI interview you about the problem before it writes anything, to try to get a closer first draft. In my limited experience, it didn't give materially better results. It may work better in other problem domains. At this point, I'd expect a frontier model to flag sketchy decisions on its own.

It didn't start with a plan. It sent three research agents into the code first (API readiness, sensitive logging paths, config loading) and wrote the plan only after they reported back. In a codebase that old, a year in still leaves plenty I haven't seen, and the surveys turned up things I didn't know.

Version 1 of the plan had 37 stories, each with an owner, an estimate, dependencies and a "done when" line. It had a sprint table, a risk register, and a list of decisions I needed to make in week one.

It was built on reasonable defaults, and most of them didn't survive. The same pattern kept repeating, and the logging design shows it most clearly.

## Challenge the opinion

### "Why did you make that decision?"

Without the why, all I had was a recommendation to take or leave. Once I had the why, I had something I could argue with.

Version 1 kept writing message logs to files, with a separate service shipping them into the DB. The DB was optional, and routing would carry on without it. Instead of accepting that design, I asked the AI to explain its reasoning. This was the turning point.

It gave six reasons:

- A blocking DB write would sit inside a single-threaded routing loop.
- A DB outage would force a choice between stopping traffic and losing records.
- No C code links the Postgres client library today.
- The team is more comfortable in C# than in the C core.
- The first customer didn't depend on it.
- Direct writes could still be added later.

The C# point stood out. It wasn't clear how it decided what the team is comfortable with, it wasn't accurate, and team comfort isn't a reason to unilaterally shape the architecture of the core anyway.

Then I asked: *"Postgres is a critical part of a lot of software stacks. Why are we designing around it being down?"*

For this system, we'd already established a non-negotiable invariant: if we cannot record the audit event, we do not send the message. Under that rule, stopping processing when the DB isn't available is the correct behavior, not a failure mode to design around. The AI agreed it had given outages too much weight and narrowed its concern to the part that was real: inside a single event loop, **any pause** (a failover, a lock wait, an upgrade) stops every interface. That's a latency question, and it's the one we ended up measuring.

**Lesson 1: Ask for the reasoning behind a recommendation.** The recommendation is the **least** useful part of the answer. The reasoning shows the hidden assumptions, and those are what you can actually check. An AI will defend a position well, so ask it to steelman the alternative, and listen for the moment it says "I overweighted that."

**Lesson 2: The constraints are your job.** Policy, compliance, team skills, customer realities and company direction aren't in the repo. The AI will design something sensible without them, and it will be the wrong sensible thing.

### Real numbers changed the answer

Our policy is that sensitive data never goes into log files, so files were out. The AI's next idea was a local writer service: each process sends records over a local socket, and one service per host batch-writes them to the DB. I asked the obvious question: *why not have each interface write to the database directly?*

It gave six reasons again. The first was connection count: counting every tracing process across the whole fleet came to tens of thousands of connections, so we'd need a connection pooler.

But that number was wrong. The AI had counted every process in the repository, not the ones that run on one host. I told it the real figures: 6 to 10 connections per host on a typical install, about 20 at the most. At that size, RAM and connection count aren't a concern.

Then I knocked out another reason. Its credentials argument assumed every process needed a DB password. That's the AI solving the generic version of the problem: "How does a process authenticate to Postgres?" The generic answer is usernames, passwords and secret management. Our actual problem was narrower: how does one local process prove its identity to another local process on the same machine? Unix already answers that. With Postgres **peer authentication**, processes connect over a local Unix socket, and the OS user identity *is* the credential, limited to members of one OS group. No passwords anywhere.

Peer authentication wasn't the AI's idea. It knows the feature as well as anyone, but it never raised it, because it was answering the generic question it had framed for itself. I knew peer auth existed, so I could see the narrower problem and knew a tool already fit it. That's the part that still comes from the engineer: knowing what options exist and recognizing when one applies. The more precisely you define the problem, the less generic the AI's answer gets, but you have to know enough to define it precisely.

That also means Postgres runs on every host. That sounds like a lot of new databases to operate, but we designed the change to keep operations the same: Postgres installs and updates through the same process as our other dependencies, and the scripts that already back up and recover our logs will back up and recover the database the same way.

Of the six reasons, two were left. The recommendation flipped to direct writes, with **a measurement to settle it**: run blocking inserts from the main router under the existing performance harness at peak load. If the extra latency fits within the per-message budget, we skip the writer service. If it doesn't, the writer is the fallback. That test is now a decision in the plan, with an owner.

**Lesson 3: Give the AI your real numbers, then turn the rest into a measurement.** Its estimates are reasonable guesses at the wrong scope. One sentence of real data ("6 to 10, maybe 20") removed half the argument here. For what was left, we didn't keep debating, we wrote down the test that would decide it.

## The pattern

**AI opinion → Factual input → Architectural hypothesis → Experiment**

- **AI opinion.** The first answer is a reasonable default built from general practice. Here: keep the log files and ship them to the DB.
- **Factual input.** You supply what isn't in the repo. Here: the audit invariant, the no-logs policy, 6 to 10 connections per host, and peer auth.
- **Architectural hypothesis.** The facts turn the opinion into a specific design you can test. Here: each process writes directly to Postgres, blocking and fail-closed.
- **Experiment.** Where the facts run out, a measurement decides. Here: blocking inserts at peak load.

The rest of the planning was the same loop, applied to config and to the plan itself.

## Don't give the AI the architecture

Give it the problem, but keep the judgment. These two sections pull in different directions, and both are true.

### Describe the problem, not your fix

Version 1 kept config in files and generated those files from the DB. I came back and said, in effect: *I don't want processes reading config files at all, except for how to reach the database. The worst part is that installing an interface package inserts its config into shared files, so package installs touch each other's files and nothing can tell what the customer changed.*

I described the problem, not a design. I'd already been working through the install flow and knew where merging into shared files breaks down: upgrades, multiple interfaces and customer edits all end up tangled in the same files. The AI pointed out that the Version 1 design (generate files from the DB) would have carried that same problem forward, just moved into a new tool.

The fix it proposed was **layering**. Every config row belongs either to a package layer, which an upgrade replaces as a whole in one transaction, or to the site layer, which belongs to the customer and is never touched by an install. Live config is the package rows plus the site's additions and overrides.

**Lesson 4: Bring the pain, not the solution.** If I'd said "store the config files in the DB," I'd have gotten exactly that, and it would have kept the problem. Describing what actually hurts got a design that removes it.

### Don't get lazy: config lines aren't text blobs

At first, the AI planned to store each config line as one text row, so the existing C parsers wouldn't need to change. I pointed out that these files are already rows and columns, basically database tables, so they should be stored as typed columns.

The AI checked whether that was safe. It surveyed every C loader and found that all of them split on whitespace and none depends on column positions. That meant each file maps cleanly to a typed table, with child tables for nested parts, and database constraints can take over checks the old validator does in code. Only four tables depend on row order, so those got a sequence column.

The point isn't a taste for normalized schemas. It's moving the rules closer to the data. Invalid config becomes hard to represent at all, validation becomes declarative instead of buried in C, relationships are explicit and queryable, and anything built later can read config without understanding the legacy file format.

**Lesson 5: Don't get lazy. You're still the engineer.** Dumping each line into a text column was the path of least resistance, and the AI took it. It's the kind of shortcut that looks fine in a plan and hurts for years. The AI will check your better idea quickly once you bring it, but you have to bring it. Describing the pain works for problems. For design, bring your judgment.

## When the decision changes, propagate it everywhere

The revised plan still had the first customer going live on file config, to keep config work off the critical path. I didn't want that. DB config should be the normal mode on day one, with a simple switch back to files if we need it. Processes should also start without the DB, reconnect when it drops, and pick up where they left off.

That one decision touched most of the plan, and the AI carried it through:

- **Sequencing and dependencies:** config work moved onto the critical path, and every story that needed DB config now depended on it.
- **Sprints:** the sprint table was rebalanced around the new critical path.
- **Non-blocking work:** logging work that the first customer didn't need moved after go-live to make room.
- **Go-live:** the plan gained a checkpoint. If DB config isn't passing on staging by then, we go live in file mode and switch over afterwards.
- **Estimates:** it re-estimated the extra cost in dev-days.

I don't really trust those estimates yet. The useful part was seeing in minutes everything a bigger goal would push around, not the number at the end.

**Lesson 6: Use the AI to explore what a change does to the plan, not to predict the schedule.** Asking "what moves if we do this?" is quick and cheap. Treat the dates as rough until real work backs them up.

## The consistency pass

After about a dozen rounds of edits across several sessions, I asked for one thing: *"Recheck the plan for consistency."*

It found and fixed about 25 problems.

**Lesson 7: Schedule consistency passes.** Each edit is correct where it's made and wrong somewhere else. After every few rounds of changes, ask for a full re-read against the latest decisions.

## Before and after

| Area | Version 1 | Final plan |
|---|---|---|
| Sensitive logs | Files, plus a service tailing them into the DB | Never written to files. Each process writes directly to Postgres, blocking, fail-closed. A measurement decides whether a writer service is needed. |
| Database role | Optional, design for it being down | Critical infrastructure. Fail-closed on audit, and processes heal on their own: start without it, reconnect, resume. |
| Credentials | A password per process | None. Peer auth over a local socket, limited to one OS group. |
| Config | DB generates the old files | Processes read layered, typed tables. The only file left says where the DB is. |
| Packaging | Install scripts merge into shared files | Packages load their own layer. Customer changes are a separate layer that installs never touch. |
| First customer | File config, DB later | DB config on day one, and a one-variable switch back to files |

Almost every row changed. Version 1 was built on general defaults, and the process replaced those defaults one at a time with facts about *this* system, *this* team and *this* policy.

## The takeaway

The AI made changing my mind cheap. A 37-story plan with estimates, dependencies, risks and diagrams used to take me days, and after days of work you get invested. You defend the first version because rewriting it hurts. Over those few days I threw away large parts of the plan eight times, and each rewrite took minutes. "Let's drop that and try the other design" stopped being expensive.

That matters more than the speed. The job in architecture isn't to produce a design and defend it. It's to find the one that survives contact with reality.

What the AI isn't good at is deciding what's true here: what our policy says, how many connections a real host has, which pain actually matters, how much risk I'll accept. Left alone, it reached for general defaults and the easiest path, and its schedule numbers are guesses I don't trust yet. Every change in this post started with me pushing back. The AI supplies the opinion and helps shape the hypothesis. The facts have to come from me, and the experiment is where the system itself gets a vote.

So the AI didn't replace the engineering. It made it cheap to keep doing it. For a system getting its first database ever, that matters more than usual. Once config, audit records and reference data live in one queryable place, things like message replay, change history, improved reporting, richer diagnostics, simpler config management and faster protocol development become features we build on top instead of separate projects. That foundation is only as good as the questions you ask while it's still a plan.
