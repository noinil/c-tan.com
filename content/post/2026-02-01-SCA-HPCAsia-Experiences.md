+++ 
title = "SCA/HPCAsia: My First Time Computational Conference"
description = "Low labor cost, high performance computing, and a lot of random parameters."
date = "2026-02-01T23:00:00"
categories = ["technique"]
tags = ["HPC", "OpenACC"]

slug = "sca-hpcasia-2026"
summary = "Notes from the SCA/HPCAsia 2026 in Osaka. My reflections on being a halfway developer, the technical density of small quantum firms, and finding identity among black T-shirts."
+++ 

Even though I often feel my identity cannot be simply simplified as a "developer", I cannot deny that I am currently working in a research center that is absolutely categorized as Computer Science.

That being said, I had never attended a CS conference before.  This weird situation finally broke recently.  Since my institute happened to be the host, I attended a big meeting in Asia: the combined SCA/HPCAsia 2026, held in Osaka from Jan 26 to Jan 29.  From the scale, it was a giant success.  My colleague remembered that last year in Hsinchu, Taiwan, there were only about 100 people.  This year? The number jumped to over 2000.  I must admire the "calling power" of the organizers.

## OpenACC

Currently, I am working on accelerating CG (coarse-grained) simulations using OpenACC.  I've got some "not bad" results, which will be summarized into a small paper in the near future.  It happened that the first day of the conference had a whole-day OpenACC session, so I arrived early.

To be honest, I didn't realize until I arrived that one of the organizers was Jeff Larkin, the chair of the OpenACC technical committee, the "Rule Maker".  I have a natural sense of alienation toward people with such status.  Like Eugene Garfield for citation index, Christopher Sholes for QWERTY keyboard, and Sid Meier for Civilization.  The rules these people made do not exist because they are "optimal", but because they are "earliest" or "to solve some problem that has already disappeared".  Eventually, they become an unshakeable shackle.

What makes me annoyed (but I have to accept) is that within these artificial, biased, or even arrogant rule systems, a huge, precise, and addictive world can actually grow.  When you are deep inside, racking your brain for some boring and tiny goals, and you occasionally look at the clock on the wall, you get a sense of absurdity: "Why should I be led by the nose by your few random parameters?"  Anyway, this is just some off-topic nonsense...

Back to the technology.  The performance of OpenACC is indeed amazing.  With very low effort, you can get 85% efficiency compared to CUDA code.  It is "half the work, double the effect".  My RIKEN colleague, Nishizawa-san, gave a talk about their software SCALE.  Many of his development tips matched mine perfectly.  For a "halfway-house" developer like me, this is very important external info.  It makes me believe my decisions were not just my own illusions.

## Cross-field is like a mountain

On the 12th floor of the Osaka Convention Center, there is a round hall.  It looks like a place rented by some "fashionable" companies for parties.  On the second day, there was a session about using AI to predict and prevent the next pandemic.  Again, because I didn't do my homework, I didn't realize the speakers included Martin Steinegger, the developer of ColabFold and one of the AlphaFold authors.

However, it is a bit sad that even for this level of research, it did not enter the "mainstream vision" in an HPC conference.  The huge hall had only a few people.  But this gave me a luxury chance to ask questions directly.  After the session, I talked with some colleagues and found they didn't even realize there was a "heavyweight" report about protein structure prediction here.

The Keynote talks were also great, but "different fields are like different mountains".  The content I could understand was very few.  I could only shallowly feel some trends and future directions.  

Unlike pure academic meetings, there were many industry reports here.  I used to have no "direct feeling" about how big a company needs to be to support a technology.  This time I was surprised to find that a small company with only dozens of people can already independently design the bottom-level implementation of quantum computers.  This explosion of technology density is truly shocking.

## Tiny accidental gains

The "inner soul" of a developer is usually shown by black T-shirts and jeans, or maybe naive stickers on the laptop shell.  In Biophysics or Protein Society annual meetings, I am often shy to take out my laptop to show things.  But this time, I finally felt a "sense of identity" -- I fell into the right crowd.

Every company booth gave away small gifts, usually including one or two stickers.  Besides that, there were many boring small things, like water cups and storage bags.  These things are hard to find in scientific academic meetings.  Sponsors are indeed "Super Big Companies."
