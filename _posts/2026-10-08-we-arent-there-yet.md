---
layout: post
date: 2026-10-08T21:50:30+02:00
title: We aren't there yet
categories:
  - software
tags:
  - blog
gist: >-
  <a href="/we-arent-there-yet">We aren't there yet</a>
img:
  src: office.png
  width: 300
---

In an ideal world, all one would have to do is take a task from a task management tool such as Linear or JIRA, give it to a coding agent and say "do this" - Maybe these tickets will already have a button with ✨ emoji and all you have to do is click it. The agent would then

  <ul>
    <li>Know which repo to work with</li>
    <li>Checks out that repo in a cloud development environment</li>
    <li>Verifies if it is an actual feature gap or a bug</li>
    <li>Performs the necessary code changes, writes meaningful tests</li>
    <li>Makes a PR, waits for the build to go green in CI and merges it</li>
    <li>Maybe even it watches over the deployment and runs a sanity check</li>
    <li>Updates the task as done and provides a summary for you to give in the stand-up</li>
  </ul>

We aren't there yet. Until then, to prove one's job worthiness, one needs to be the man in the middle and watch over all those steps and steer the agent as it does its job. The expertise the human brings to the table are, questions. Questions like:

1. Is the feature required? / Is this a bug?
2. How is the code structured? Is the agent making changes on the right part of the code.
3. Is the code up to the standards? - in readability, correctness, validation and execution
4. Did the code actually address the feature / bug it is meant to address?

Better yet, have a taste of what features to add, how the product should look and feel.

It is tempting to just do the first step (point the ticket's url to claude and say "make a pr for this ticket") and the last step (status update in stand-up). But thats no way to do your job. Such behavior gives me a lot of stress.
