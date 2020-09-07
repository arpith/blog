---
layout: post
date: 2020-09-07 12:00:00 PT
title: "My note taking process at work"
---

# My note taking process at work
I take a lot of notes at work! There are roughly three different goals - to figure out what I want to do on a particular day, to find out what happened a while back and to reflect on the week. Here's how I approach taking notes for these goals.

## What I want to do today
At the start of the day I take a look at my open tickets and pick the most urgent / important. I write a brief list of what I'm going to do that day and what I did the previous day. Looking back at the previous day is a convenient way to catch broader issues that need to be addressed. For example, if I'm getting requests outside the team's process for taking on work it sometimes indicates that there's a gap in our process or that I should spend some time spinning other folks up on this area.


## Find out what happened a while back
I try to document my thoughts somewhere that I can find later (eg) meeting notes, incident reports / debugging issues, one pagers / ideas, peer reviews / self reviews / brag docs etc all end up in folders in dropbox paper, longer design docs usually in google docs and internal documentation ends up in confluence - I've also found detailed pull request descriptions very helpful if the change is not very intuitive.

### Meeting notes
I jot down a bullet list of what people say during the meeting and spend about ten minutes after the meeting writing a quick summary. I send out the notes to the teams who were involved in the meeting and cc the project mailing list.

### Debugging issues
When debugging issues or writing incident reports I use an approach that I learnt from a colleague. I start by making a detailed timeline with links to actions taken, graphs that changed, relevant logs. I use this timeline to figure out what happened, primarily by asking 'why' for each line. Once I have an understanding on what happened and how it triggered the issue I spend some time thinking about how we can address this.


### Ideas
I usually start notes on ideas, one pagers and design docs by documenting why we need something. Once I have a reasonably detailed explanation of why a change is required I talk about how we can address the need and what we'll have to do. I usually also try to document roughly how much work is involved. I typically then share the document with stakeholders and explicitly request feedback.


### Feedback
For performance related feedback I usually start with what work was done and what the associated impact was. As an example, I maintain a high level brag documents to document the broader work I've been involved in over the last half. I usually frame this in the context of my goals for the half or longer term goals. I use this document to write my self review and share it with my manager and anyone who's writing a peer review for me.

When writing a peer review for a colleague I write down how we interacted and the impact they had on the project. I typically also ask them if they have a brag document so I can frame this in the context of their other work and goals.


### Internal documentation
I have a few approaches to writing internal documentation. I sometimes find myself looking up something that does not exist yet, so when I figure it out I add it to the place I looked (eg the user guide / team runbook). If it's a project I worked on or was involved with I try to explain it to someone else and use my notes to generate documentation. If I wrote a design doc for it I just pull it into the internal wiki. I've also found diagrams very helpful!


### Code documentation
I've found detailed pull request descriptions very helpful if the change is not very intuitive. I usually start with why it's unintuitive and try to provide as much detail as possible for future-me. I'll sometimes also summarize the explanation as a comment in the code.

## Weekly notes
I write quick notes every week on what I did and learned by looking at my pull requests, sent emails, meeting notes, jira tickets and documents I read. This practice grew out of a weekly email I used to send about progress on a project I was involved in. For project related weekly notes I usually start by asking other folks working on the project how things are going.

I've found these notes very handy for other processes in the company - for example, the engineers responsible for projects in my broader team have a weekly meeting where we chat about how things are going, various parts of the organization will invariably have some way of tracking all the projects related to a larger effort, my subteam has a weekly meeting where we discuss what we're up to. I usually copy my updates in all these places with minor tweaks to the format.

