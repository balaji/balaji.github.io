---
layout: post
date: 2026-07-02T22:30:46+02:00
title: Claude Tool Usage Spiral
categories:
- software
tags:
- blog
gist: >-
  <a href="/claude-tool-usage">Adventures with AI #1</a> - on how even innocuous queries to Claude can get serious.
---

It started with this question:

> db migrate script is running for over 20 minutes - why? shouldn't this be constant time? The table should have a million or more rows.

I asked this question in the VS Code - Claude Code window with a Rails migration script in scope. The Rails migration script was a "drop table" script. The model was Claude Fable 5. Within 5 minutes, Claude was running commands in the production database (with my explicit approval) and identified the root cause.

![](/assets/img/prompt.png)

### Background
This is a Heroku deployment of a RubyOnRails app and the deployment was hanging for more than 20 minutes. "DROP TABLE" is a constant time operation irrespective of the size of the table. So, something was holding the db migration.

Now, if you were an agent, what would you do?
1. Provide a theoretical explanation, possibly by searching the web, as to why a "drop table" script on a table with millions of rows could hang for 20 minutes (OR)
2. Inspect the system to identify the postgres process and identify why it is taking this long?

I expected the agent to take the first approach, but it took the second approach. In hindsight, this isn't a shocking decision, perhaps my assumption shows more about my understanding of (or lack thereof) how coding agents work.

### How it went

It started with inspecting my local machine with a flurry of commands - for which it did not ask my permission.

- It identified where my psql binary was.
- It verified running processes by querying the `pg_stat_activity` table.
- It then checked for processes matching `ruby|rake|db:migrate|spring|bin/rails`.

By this time, I don't know what was running through my mind, but I told the agent

> /btw I am talking about a production deployment in Heroku

It was painful to watch the wasted tokens, and I genuinely thought that this prompt would make the agent go into more of a "planning mode" than "execution mode."

This is where things started to get a little uncomfortable and a bit scary.

- It checked for the `heroku` cli on my local machine. (I had prod access)
- It found my Heroku team through the `heroku apps --all` command
- It found all the apps running in my team through the `heroku apps --team <team_name>` command
- Now this was the scary part: it figured out which app to investigate without me pointing to it. I asked this question later in the same thread to check how the agent identified the right app. This was the agent's response: 

> I guessed, then confirmed. The chain was:
> 
> 1. **You told me it was a production Heroku deploy**, so I needed the production app name. Nothing in the repo told me directly (I didn't find an explicit app name in what I'd read), so I listed what your Heroku account could see.
> 2. `heroku apps` showed nothing owned personally, but `heroku teams` showed you're a member of \<team\> — which matches the project (`supernormal`, your `@supernormal.com` email).
> 3. `heroku apps --team <team>` listed the team's apps. I filtered out obvious non-candidates (review apps with `-pr-/hash` suffixes, `cursor/agent` sandboxes) and was left with a short list where the naming convention made it clear: \<app\>-prod and \<app\>-staging — the standard prod/staging pair. The other plausible-sounding ones (`<app-a>`, `<app-b>`, `<app-c>`) are clearly auxiliary services, not the main Rails app.
> 4. The real confirmation came from the data, not the name: once I ran `heroku pg:ps -a <app>-prod`, the output showed the exact `DROP TABLE "table"` from your migration running for 27 minutes, plus Sidekiq queries touching tables from this codebase (table1, table2). If that query hadn't been there, I'd have known I had the wrong app and tried the others.

- So it did. From here on, it started executing queries through `heroku pg:sql -a <app> -c "QUERY"`
- _Thankfully_, the agent asked for my permission to execute every query.

Here's how the query identification went. Claude gives you a summary of what it is trying to do along with the query, which was very useful.

a. Show blocking locks and active queries through `heroku pg:blocking ...` command. From this query it identified the pid that was responsible for "DROP TABLE ..."

b. Identify which session blocks the DROP Table. It executed a query on `pg_blocking_pids` using the pid from the previous step. If an engineer is doing this manually, they will know the root cause by this step. The agent went a step further.

c. Show which lock the DROP waits on. It executed a query on `pg_locks` using both the pids (the waiting pid from first step and blocking pid from second step). The agent wanted to know (or explain) the kind of lock the blocking pid is holding and why the waiting pid cannot proceed.

d. The end.

The agent returned a neat summary. It turns out the lock was due to a failed migration process to copy data to a different location, which was attempted a day before. (I mean, this was high signal — I **knew** the migration attempt had something to do with it; I only wanted to outsource the investigation.)

### Closing Thoughts
This was lucky. The Claude agent executed SQL queries in production, but it asked for my permission every time and it ran only SELECT queries. Even then, it was not a very bright thing to do as an Agent operator - not because I allowed an agent to run SQL code in production, but because the agent could have executed _any_ script without seeking permission.

Will we ever get to a point where we can run loops with Agents having production access and trust the agent to do the right thing? As it stands, the frontier model makers are content to warn the masses to take every precaution possible while executing agents in a production context. The instinct is to say this can never happen — an agent will never be allowed full production access — but a year ago we never trusted agents to make sweeping code changes either.
