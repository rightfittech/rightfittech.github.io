---
title: "The On-Call Survival Guide: Lessons From 7,300 Nights of Maybe Getting Paged"
description: "Twenty years on the pager, told as dark comedy — the go-bag, the 3 a.m. dread, the gloriously dumb false alarms, and the one lesson that outranks every runbook."
date: 2026-06-26 09:00:00 -0400
categories: [On-Call, Survival Guides]
tags: [on-call, sysadmin, incident-response, alert-fatigue, monitoring, pagerduty, burnout, war-stories]
---

It's 3:04 a.m. Nothing is wrong.

I know nothing is wrong because I've already checked — phone unlocked, email refreshed, dashboard squinted at through one open eye. Everything is green. And yet here I am, awake, staring at the ceiling, mildly furious at my own nervous system for booting me up like a cron job nobody scheduled.

Do the math: roughly 7,300 nights over twenty years. On the overwhelming majority of them, nothing paged me at all. That's the part nobody tells you when you sign up. The job isn't the 2 a.m. page. The job is the 7,290 nights you *might* get one — the standing dread, the volume cranked to max, the way you start sleeping like someone who's heard there might be a war.

This isn't a runbook. There are enough runbooks. This is a field guide to the *lifestyle* — the gear, the nights, and the people — from someone who has carried the pager long enough to find it funny. Mostly.

## The Bag

Every veteran has a bag. Mine lives by the door so I can grab it on the way out at an hour when I can't be trusted to find my own shoes. The current loadout:

- A **USB-to-serial console cable**, which is less a tool than a badge of generational trauma. If you know what this is for, you have suffered, and we are kin.
- A **tangle of USB cables** in every doomed standard humanity has ever invented, because the one I need is always the one I left in the other bag.
- A **6-foot CAT5e patch cable**, for the moment a "wireless problem" turns out to want a wire.
- A **charging brick**, because a dead phone on-call is just unemployment with extra steps.
- **Two flashlights** — a small one for reading labels in a rack, a big one for when the small one isn't enough and, occasionally, for morale.
- **Snacks, a water bottle, a multitool.** The essentials of any expedition.

I am equipped like I'm deploying to a disaster zone. Half the time the disaster is a switch that needs to be power-cycled by a guy who just drove 25 minutes to press a button. But the *one* time you need the console cable and don't have it is the story you'll be telling for fifteen years — so the bag stays packed.

And then there's the ritual. Every night, before I let myself sleep, I do a pass: email, text messages, app notifications. I'm not really reading them — I'm auditing them. I'm looking for the things that could go bump in the night, or the things that already hiccuped and are sitting there quietly getting ready to go bump. It's half superstition, half genuine triage, and it's saved me more than once. If something's yellow at 11 p.m., it is going to be red at 3.

## The Nights

This is where the dread economy lives. Every page begins as the same question, asked in the dark by a brain that is maybe 40% online: *is this real, or is the monitoring just lonely?*

Sometimes it's real. The worst one I ever caught was a regional power outage that knocked out a 24x6 manufacturing facility — during peak season, because of course it was during peak season. Nothing concentrates the mind like knowing the line is down, every minute has a dollar figure stapled to it, and there's a plant manager somewhere already learning your name for reasons you won't enjoy.

Sometimes it's gloriously, infuriatingly *not* real. At 1:30 one morning I got paged because a user "couldn't log in to check their email." I dragged myself online with the adrenaline of an impending auth outage. The root cause: they were typing the wrong username. That's the whole incident. A human being escalated their own typo, through three layers of process, all the way to my nightstand. I think about that person more often than they will ever think about me.

And sometimes it's real in the *worst* way — the kind where you fix one thing and find three more behind it. My personal favorite genre is the morning-after-patching surprise. We rolled updates across a VMware cluster, and I woke to the dreaded purple screen — a PSOD, VMware's version of a kernel faceplant. Then the gut-drop: it wasn't *one* host. It was more than one. Nothing teaches you to stagger a maintenance window quite like watching a "routine" patch take down hosts in formation, like dominoes you personally lined up the night before.

You learn to sleep defensively. Phone face-up, volume maxed, positioned on the nightstand for the fastest possible grab. You develop the phantom buzz — the leg twitch at a vibration that never happened. And years later you will flinch at a stranger's notification in a coffee shop, because it's the same tone your old alerting used, and your spinal cord never got the memo that you changed jobs.

## The People

For a while at an MSP, I had a reputation. The team didn't love it when I was holding the pager, because my rotations had a habit of going sideways — a P1 (something major down) or a P0 (multiple customers down) seemed to find me the way storms find a beach house. I'd like to tell you it was bad luck. It was probably bad luck. Probably.

Here's the one I tell at parties. One of our biggest customers lost their production SAP environment during routine maintenance — a Sunday night, naturally. I opened the incident bridge at 8:30 p.m. and we settled in for the long haul. Six hours later, somewhere past 2:30 a.m., with a dozen engineers grinding on the fix and nothing left for me to actually *do* but keep the bridge alive, I drifted off. On the call. Muted, thank God. I came to at 7 a.m. to the sound of my own name — the engineers were asking for me. My stomach dropped clean through the floor, right up until I understood *why* they were asking. They weren't asking because something had gone wrong. They were asking because it was *over*. We closed the bridge. I had slept through the back half of my own major incident and nobody was the wiser.

(The real lesson buried in that one: incident command is a *role*, not a person. An eleven-hour bridge needs relief on the desk. The commander does not have to remain conscious for the entire outage — and if you find out they did, you didn't staff it right.)

On-call doesn't stay at work, either. It follows you home and pulls up a chair at dinner. It's the second beer you don't order because you're holding the pager. It's the way a Slack ping at 6:45 p.m. makes you flinch mid-sentence and leave the conversation without getting up from your seat. The people around you learn the on-call face. They learn not to start the movie.

## The One I Don't Tell for Laughs

About fifteen years ago I was a senior sysadmin at a small logistics company when a major hurricane came through. We had no real generator — just two small portable units keeping the servers and the cooling alive. So my boss, the IT director, and I took shifts. For roughly 48 hours, until grid power came back, one of us was always awake to top off the fuel. I spent the worst of that storm asleep on an office couch, setting an alarm to get up and check fuel levels, while my young family rode it out at home without me.

We kept the servers up. I have never been less proud of an uptime number.

That was the night I learned the lesson that outranks every runbook in this post: **family comes first. Always.** No SLA is worth missing the storm with your kids. The infrastructure is going to have an outage with or without you bleeding for it. Your family only gets the one version of you.

I did do one useful thing on my way out the door at that company, though. I told them — loudly, repeatedly — to buy a *real* generator. Years later I ran into my old boss and learned they'd installed a 40kVA unit that could carry most of the building: server room, HVAC, the works. Nobody has had to sleep on that couch since.

And that's the whole guide, really. Pack the bag. Check the dashboard before bed. Stagger your patches. Learn to nap on a muted bridge. And when the storm gets bad enough that someone has to choose between the servers and their family — make sure you've already built the thing that lets them choose their family.

The tea's still cold. The pager's still quiet.

For now.
