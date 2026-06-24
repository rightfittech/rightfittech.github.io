---
title: "The Day I Ran rm -rf / on a Production Server (With No Backups)"
date: 2026-06-24 09:00:00 -0500
categories: [War Stories, Post-Mortems]
tags: [linux, ubuntu, backups, disaster-recovery, lvm, sysadmin, incident-response]
description: "A long-time Linux user, a physical production server with no backups, and one command that wasn't quite the one everyone fears. Here's the outage I caused — and how we clawed it back."
pin: true
---

The cursor is blinking. I've just hit Enter. And for one merciful second, nothing happens.

Then the errors start scrolling — faster than I can read them, faster than I can think — and somewhere in the back of my skull a very calm voice says: *that was the production server.*

It's still running. That's the part nobody warns you about. The box doesn't die screaming. It keeps humming along like everything's fine, services still answering, while I sit there knowing I've just pulled the floor out from under it and it simply hasn't finished falling yet.

## A routine Tuesday

Let me back up. (Ha.)

By this point I wasn't new to Linux. I'd been running Debian variants as my daily driver since the mid-2000s — comfortable enough on a command line that I'd stopped being scared of it. That, it turns out, is exactly the problem. Nobody nukes a server in their first month. You do it years in, when the commands have become muscle memory and muscle memory has quietly replaced reading.

The machine was a physical Ubuntu LTS box — 18.04, the freshest long-term release at the time — sitting in a university server room and quietly doing important work for one of the research teams. My job that afternoon was unglamorous: update some packages, tidy a few things up. Routine maintenance, routine window.

Here's the detail that turns this from an anecdote into a horror story: it was a *physical* server. We backed up our VMs religiously — snapshots, the works. But we'd never bought the licensing to back up bare-metal boxes. So this one machine, the one I was about to operate on, was the single server in the building with no safety net. I knew that, in the abstract — the way you know where the fire exits are in a building you'll obviously never need to evacuate.

## The command everyone thinks I ran

If you type a bare `sudo rm -rf /` into a modern Ubuntu box, nothing happens. It stops you:

> rm: it is dangerous to operate recursively on '/'
> rm: use --no-preserve-root to override this failsafe

That failsafe has shipped on by default since 2006, precisely because too many of us learned this lesson the hard way before it existed. So no — I didn't run the command from the t-shirts. I ran its uglier, sneakier cousin, the one the failsafe doesn't catch:

```bash
sudo rm -rf /*
```

That little asterisk is the whole story. The shell expands `/*` into every top-level directory — `/bin`, `/boot`, `/etc`, `/lib`, `/usr`, `/var`, all of them — and hands them to `rm` one at a time. `rm` never sees a literal `/`, so the guardrail never trips. It just gets to work, alphabetically, deleting the operating system out from under itself.

I'd meant to be working somewhere far more specific. I was not where I thought I was. The slash did the rest.

## The server that wouldn't die

And yet — it kept running.

This is the surreal part. A Linux system that's already booted lives largely in memory. The kernel's loaded, running processes are holding their open file handles, shared libraries are already mapped into RAM. So when you delete the files those programs came from, the programs don't immediately notice. The server kept answering. Services kept serving. For a few minutes it looked, impossibly, fine.

But it was a dead machine walking. Every binary I'd need to *fix* it was gone. I couldn't install anything, because the tools that install things were gone. And I couldn't reboot — that was the one thing I knew for certain — because the moment that box went down, there was no `/boot`, no init, nothing left to bring it back up. It would never see a login prompt again.

So I did the hardest thing in the whole story: I left it running, took my hands off the keyboard, and went to tell some people the truth.

## The longest walk in IT

There's a fork in the road in every incident like this, and it shows up early. You can start quietly trying to fix it yourself and hope nobody asks. Or you can walk down the hall and say the words out loud.

I walked down the hall.

I told the IT Director and the head of the research department the same thing, in plain language: here's exactly what I did, here's the current state of the server, here's what I've *stopped* doing so I don't make it worse, and here's who I think can help. No hedging. No "well, technically the system experienced an unexpected event." I broke it. I said so.

I'll be honest — that walk is the part I'd most like to skip in the retelling, and it's also the part I'm proudest of. Because the cover-up is always more expensive than the outage. Every hour you spend hiding a problem is an hour nobody's spending solving it, and trust, once you've spent it that way, doesn't come back at par.

## Calling in someone smarter

Then I did the second-hardest thing: I admitted the limits of what I knew.

I was good with Linux. I was not, on that particular afternoon, good enough to resurrect a server I'd just disemboweled. But I knew someone who was — a sysadmin on the university's central OIT team who was a genuine Linux wizard, the kind of person who's seen everything twice. I called him.

Here's where past-me had accidentally done something right. The server's storage was split: the operating system lived on one logical volume, and the research team's data lived on a *second, separate* logical volume. That separation is the only reason this is a funny story today instead of a very short chapter in someone's dissertation.

We poked at filesystem recovery for a while — there are tools that can claw deleted files back if you move fast and don't write over them. But we made a colder, smarter call: the OS was a write-off, and trying to stitch it back together would leave us with a Frankenstein box we'd never fully trust. Cleaner to start fresh. So we did a full reinstall of the operating system, remounted the data volume — intact, right where we'd left it — and brought the research team back online on a machine that was, ironically, healthier than the one I'd destroyed.

## The damage report

The research team lost time — real time, the kind measured in a workday or two of outage and the slow tail of double-checking that nothing else had quietly shifted. There were conversations afterward that I didn't enjoy. I'd caused a genuine disruption to people doing real work, and no amount of clean recovery erases that.

I'm not going to pretend it landed softer than it did. The machine came back; the afternoon still cost people something. Both of those are true, and the second one is the part worth sitting with.

## What twenty years taught me in one afternoon

**"The VMs are backed up" is not "everything is backed up."** I *knew* that physical box was outside our backup scope. I let it stay an abstraction instead of a problem with a deadline. Know your coverage gaps in writing, and treat every "we'll get to it" server as the one that's about to need it.

**An untested backup is a rumor.** We didn't even have one here, which is worse — but the broader lesson holds. A backup you've never restored from is a hope, not a plan. Restore from it on purpose, before the universe makes you do it under fire.

**Separate the OS from the data.** That single architectural choice — operating system on one volume, data on another — is the entire reason this story has a happy ending. When you design a system, build in a seam that can hold when everything on one side of it is gone.

**Respect the blast radius of one command.** `rm -rf` with a wildcard and a wrong assumption about where you're standing is all it takes. Check `pwd` before anything destructive. Be paranoid about globs. Know your failsafes — `--preserve-root` is the default for a reason, and tools like `trash-cli` exist so "delete" doesn't always have to mean "forever."

**Disclose fast, ask for help faster.** The two best decisions I made that day were telling the truth immediately and admitting I couldn't fix it alone. Ego is the single most expensive thing you can carry into a server room.

## One half-second, twenty years long

The server's been fine ever since. I, on the other hand, have never been quite the same — and I mean that as a compliment to the experience.

To this day there's a half-second pause before I hit Enter on any command with `rm` and a slash anywhere near it. A little involuntary flinch. Muscle memory, finally pointed in the right direction.

The tea's still cold. But I read the command line now.
