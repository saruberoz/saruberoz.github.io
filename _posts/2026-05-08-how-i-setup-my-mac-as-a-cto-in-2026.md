---
layout: post
title: "How I Setup my Mac as a CTO in 2026"
description: "A walkthrough of the apps, tools, and AI-native workflow I run on my Mac as a CTO in 2026"
modified: 2026-05-08 00:00:00 -0700
tags: [mac, setup, cto, productivity, ai, claude, cursor, warp, obs]
image:
  feature:
  credit:
  creditlink:
comments: true
share: true
---

Its been a long 12 year from my last post. As time changed and environment changed, I wanted to share how my setup as CTO are. The most interesting is AI, how does AI impact a CTO's setup and workflow. *AI is now part of the toolchain* — not a side experiment anymore.

This post is my version of that walkthrough. Sometimes I ship code that works in a different system, especially the automation / ML  part, but I also run a team, do a lot of architecture sharing, recording, and switching contexts multiple times a day. The setup reflects that.

> *Inspired from [How I set up my MacBook Pro as an ML engineer in 2022][medium-2022].*

[medium-2022]: https://medium.com/data-science/how-i-set-up-my-macbook-pro-as-a-ml-engineer-in-2022-88226f08bde2

![Desktop overview]({{ site.url }}/images/mac-setup-2026/desktop-overview.png)
*My usual layout — Warp on the 2nd Desktop, Cursor/VSCode w/ Github Copilot / Claude, and everything else stacked in 1st Desktop.*

## What's different from a 202 setup

Before the tool-by-tool walkthrough, the *why* — because the difference between a 2022 and a 2026 setup isn't the apps, it's the philosophy.

1. **AI is in the loop, not in a tab.** In 2014, there is no AI in 2022, you opened ChatGPT in a browser tab when you got stuck. In 2026, Claude is in the terminal (Claude Code), in the editor (Cursor), and on the desktop as a thinking partner. The tab is the fallback, not the entry point.
2. **Metrics**. I still track metrics, but now not only Technical metrics like uptime, issue, but financial business metrics; Eg. How many new Clients, how many Churn, whats the GMV, EBITDA
3.. **Python tooling consolidated.** `pyenv` + `pip` + `poetry` + `virtualenv` collapsed into [`uv`][uv]. Installs are 10–100× faster and the mental model is simpler.
4. **Containers got lighter.** Docker Desktop has been replaced by [OrbStack][orbstack] for most folks I know — fast, native, no fan spin.
5. **The terminal became AI-native.** Warp replaced iTerm2 as the default in a lot of setups, and the AI command suggestions earn their keep.
6. **Apple Silicon is fully mature.** No more Rosetta workarounds, no more "does this work on M-series?" googling. Everything just runs.
7. **The CTO hat changes the optimization.** As an IC engineer, you optimize for *flow*. As a CTO, you optimize for *fast context switches* — between code, meetings, hiring, product roadmap strategy docs, demos,and most important *ROI*. The setup below is built for that.

[uv]: https://github.com/astral-sh/uv
[orbstack]: https://orbstack.dev/

## Hardware

I run a MacBook Pro with Apple Silicon. 
The exact specs you want depend on what you do day-to-day, but for a CTO workload — code + meetings + recording + occasional local model inference — I'd target:

- 36GB+ RAM (32GB is the floor; you'll regret 16GB the first time you run a local LLM)
- 1TB+ storage (containers, source code, dependency,  AI models, Technical Docs,  — they add up fast)
- An external monitor + good webcam + a great microphone (you're on mic a lot more than you think)

A clean Mac out of the box. Now to make it useful.

## macOS baseline

I make a small set of system tweaks before installing anything else. These have stayed roughly the same for years:

- **Trackpad → Tap to click**, three-finger drag enabled (Accessibility settings)
- **Keyboard → Key repeat: Fast, Delay: Short** — non-negotiable for terminal work
- **Finder → Show all filename extensions, show hidden files** (`Cmd + Shift + .`)
- **Dock → auto-hide, magnification off, position left** (more vertical screen real estate)
- **Hot corners → top-right for desktop, bottom-right for screen saver**

If you want a one-shot script: there's a community-maintained [macOS defaults gist][macos-defaults] floating around; I cherry-pick from it.

[macos-defaults]: https://github.com/mathiasbynens/dotfiles/blob/main/.macos

## Homebrew & a reproducible setup

[Homebrew][brew] is the foundation. Install it first:

{% highlight bash %}
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
{% endhighlight %}

Then everything else gets declared in a `Brewfile` so the next Mac is one command away. This is the single biggest reproducibility win I made over the years — moving from "remember to `brew install` each thing" to `brew bundle`.

`~/Brewfile`:

{% highlight ruby %}
# CLI tools
brew "git"
brew "gh"           # GitHub CLI
brew "jq"
brew "ripgrep"
brew "fzf"
brew "bat"
brew "eza"          # modern `ls`
brew "tmux"
brew "uv"           # Python package + env manager
brew "mise"         # polyglot version manager (also handles JDKs)
brew "pnpm"
brew "go"
brew "gradle"       # JVM build tool
brew "bun"          # fast JS runtime + test runner
brew "deno"         # secure-by-default JS/TS runtime
cask "temurin"      # Eclipse Temurin (OpenJDK)
cask "google-cloud-sdk"   # provides the `bq` CLI for BigQuery

# Apps
cask "warp"
cask "visual-studio-code"
cask "cursor"
cask "claude"
cask "google-chrome"
cask "raycast"
cask "nordpass"
cask "whatsapp"
# Outline (getoutline.com) and Google Chat are web-based — install as PWAs
cask "linear-linear"
cask "zoom"
cask "figma"
cask "tableplus"
cask "orbstack"
cask "obs"
cask "keycastr"

# Fonts
cask "font-jetbrains-mono"
{% endhighlight %}

Run it with:

{% highlight bash %}
brew bundle --file=~/Brewfile
{% endhighlight %}

*One command, full machine setup.*

[brew]: https://brew.sh/

## Terminal — Warp

I switched from iTerm2 to [Warp][warp] about two years ago and haven't looked back.

What earns its keep:

- **AI command suggestions** — typing the description of what I want and getting the `awk`/`jq`/`gh api` incantation drafted is a daily time-saver
- **Blocks** — every command + its output is a separate, copyable, shareable unit. Pasting a block into a PR comment instead of a wall of text is small but lovely.
- **Workflows** — common runbooks (deploy, restart, check logs) saved as parameterized commands

![Warp terminal]({{ site.url }}/images/mac-setup-2026/warp-terminal.png)
*Warp's AI suggestion turning "show me processes listening on port 3000" into the actual command.*

I run zsh inside Warp with [Starship][starship] for the prompt, plus the usuals: `zsh-autosuggestions`, `zsh-syntax-highlighting`, and `fzf` keybindings.

[warp]: https://www.warp.dev/
[starship]: https://starship.rs/

## Apps - Most of my apps to improve my working efficiency

As i work with different project, the easier it is to switch context the better it is, so **Shortcuts everywhere*, I mostly use [Rambox][rambox] for my communication and context tools. [flycut][flycut] and [boring.notch][boring.notch] to help me when I code.

And above all [oc][openclaw] as my personal assistant, reminder, question for issue, bug, team KPI/metrics tracker.

### Rambox

A workspace browser that runs all my web apps in one window — Google Chat, WhatsApp Web, Outline, Linear, Gmail — each as its own pane behind its own keyboard shortcut. Instead of ten browser tabs and twelve dock icons, I get one window with named workspaces I flip through. I keep separate profiles for **Work**, **Founder**, and **Advisory**, so each context has its own logged-in accounts and notification rules. It's the closest thing to "tmux for the web" I've found, and the single biggest reason my dock isn't a graveyard of Electron apps.

### Flycut

A minimal, open-source clipboard manager. **Cmd + Shift + V** opens a stack of recent copies; arrow keys to pick, Enter to paste. Sounds trivial until you notice how many times a day you copy something, then copy something else, then need the first thing back. Flycut turns "I lost it" into a non-event. No telemetry, no upsell, no AI feature trying to summarize my clipboard — just a stack that remembers.

### boring.notch

Reclaims the MacBook notch as useful UI. Media controls, AirDrop progress, calendar peek, Pomodoro timer — all surfacing around the camera cutout instead of the notch being dead pixels. The small win is that I look at the top of my screen many times an hour, and giving that real estate a *job* compounds. Hover gestures + customizable widgets make it feel like a hardware feature Apple should have shipped.

### OpenClaw

The piece that ties the workflow together. [oc][openclaw] is my personal AI assistant — reminders, issue queries, bug triage, and team KPI / metrics tracking — all in one surface instead of jumping between Linear, the BigQuery console, and my notes app. Patterns I lean on daily:

- *"Remind me when X happens"* — natural-language reminders that fire on the right signal, not just at a wall-clock time
- *"What's the status of issue #432?"* — pulls Linear context without me opening it
- *"Where are we on Q2 churn?"* — runs the dashboard query against BigQuery and answers in line
- *"Draft a 1:1 agenda for $person"* — pulls recent Outline notes + Linear assignments + calendar history

It's the assistant most aligned with how I actually work — issues, KPIs, reminders — rather than a generic chatbot trying to be everything.

[rambox]: https://rambox.app/
[flycut]: https://flycut.macupdate.com/
[boring.notch]: https://github.com/TheBoredTeam/boring.notch
[openclaw]: https://openclaw.ai

## Editors — VSCode and Cursor

I use both. The split:

- **Cursor** for any task where I want AI agency in the editor — refactors that touch many files, exploring an unfamiliar codebase, writing tests against existing implementation. Cursor's agent mode is good enough now that a non-trivial percentage of code changes start there.
- **VSCode** for everything else — quick edits, reviewing diffs, working with tools that have first-party VSCode extensions but not Cursor ones, pair-debug sessions where I want a stable, non-suggestive editor.

Both have settings sync turned on, so a fresh machine inherits my keybindings, theme (One Dark Pro), and font (JetBrains Mono, ligatures on) automatically.

Extensions I install on day one:

- GitLens
- ESLint / Prettier
- Python + Pylance
- Go
- Java
- Docker
- Error Lens
- Thunder Client (light API testing)

![Cursor with AI]({{ site.url }}/images/mac-setup-2026/cursor-claude.png)
*Cursor's agent panel mid-edit. This is the single biggest workflow change from 2022.*

## AI in the toolchain — the 2026 difference

This is the section that makes a 2026 setup post different from a 2022 one. I use three AI surfaces, and each has a distinct job.

### Claude desktop app — the thinking partner

The Claude app sits open on my second screen most of the workday. I use it for:

- **Drafting** — strategy memos, hiring rubrics, customer emails, this very post
- **Code review** — paste a diff in, ask "what would you push back on?", and let it argue
- **Architecture sketching** — describe a system, ask Claude to poke holes, iterate

It's less about generating final output and more about *thinking out loud with someone who reads at infinite speed*.

![Claude desktop]({{ site.url }}/images/mac-setup-2026/claude-desktop.png)
*Claude with a project loaded — the desktop app's project feature is genuinely useful for keeping context.*

### Claude Code — the terminal agent

[Claude Code][claude-code] is what I reach for when the work is "do a thing in this repo" rather than "help me think." It runs in the terminal, has access to my tools, and is unreasonably good at the kind of tasks that used to take a dedicated afternoon — writing test scaffolding, doing bulk refactors, threading a small change across many files.

I run it from Warp, in the project directory. The fact that it lives where my code lives is the whole point.

[claude-code]: https://claude.com/claude-code

### Cursor — the inline editor

When I'm already in the code, Cursor's inline edit (`Cmd + K`) is faster than switching contexts to the desktop app. It's the right tool for changes scoped to a few files, where the model needs to *see* the surrounding code.

### How I pick

A rough decision tree:

- *Thinking, drafting, no code involved* → Claude desktop
- *In the editor, small-to-medium scoped change* → Cursor
- *Multi-file refactor or anything that needs shell access* → Claude Code

### Where AI doesn't help (yet)

It's worth being honest. I still write the prompts, name the abstractions, decide what good looks like. AI is bad at:

- Knowing what *not* to build
- Pushing back on a bad product decision
- Reading the room in a hard conversation

The CTO job is mostly judgement, and judgement still doesn't auto-complete.

## Agentic tools

The bigger shift in 2026 isn't AI *assistants* — it's AI *agents*: tools that take actions, run for minutes or hours, and come back with a result. As a CTO, this is where the time savings compound, because I can hand off a task and stay in a meeting.

My top 3:

1. **Claude Code** — covered in the AI section above, but worth re-anchoring here: it's the agent I rely on most. Shell access, edits files, runs tests, makes commits. A common pattern: kick off a refactor before a 1:1, review the diff after.
2. **Cursor Composer / Agent mode** — agentic inside the editor. Multi-file edits where the model decides which files to touch. Best when the change is scoped to one repo and I want to see each edit before accepting.
3. **n8n with AI nodes** — workflow automation where some steps are LLM calls. I use it for things like summarizing new GitHub issues into Linear, or drafting a daily standup digest from Slack. Not coding, but agentic in the workflow sense.

Honorable mentions: **Replit Agent** for spinning up prototypes, the **Computer Use API** for browser-driven research, and **scheduled Claude Code routines** for anything that should run on a cron.

The CTO framing: agentic tools work best when the task is *bounded and verifiable* — "implement this, tests must pass" — and you can step away. They're worse for fuzzy work where you need taste in the loop. Use them where the answer can be checked, not where it needs to be felt out.

## Browser — Chrome with profiles

The single best Chrome trick is **profiles** and **tabs**. I run multiple of those, will share the tabs groupings:

1. **sec** — company Google account, work bookmarks, work-only extensions
2. **shareholder** — investor decks, board materials, recruiting
3. **settlement** — current task im focusing
4. **perf review** — full company perf review from business and product side. i run it twice yearly as input to the team

Each has its own group color. Switching contexts is a single click.

![Chrome profiles]({{ site.url }}/images/mac-setup-2026/chrome-profiles.png)
*multiple grouped contexts / tabs, no cross-contamination.*

Extensions: NordPass, uBlock Origin, React/Vue devtools, and a privacy extension or two.

## Dev runtimes

### Top 3 languages I reach for

1. **Python** — my default. Backend services, scripts, AI/ML workflows. The big shift since 2022 is [`uv`][uv] replacing `pip` + `pyenv` + `virtualenv` + most of `poetry`. Installs that took a minute take a second.
2. **Go** — for services, CLIs, and anything where I want a static binary and predictable performance. Still the gold standard for "boring tech that scales."
3. **Java** — for enterprise integrations, high-throughput backend services, Android, and anything in the Kafka / Spark / Elastic / Cassandra ecosystem where the JVM client is the first-class citizen. My stack:
   - **JDK:** [Eclipse Temurin][temurin], versioned per-project with `mise`
   - **Build:** [Gradle][gradle] (Kotlin DSL where I have the choice)
   - **Backend framework:** [Spring][spring] — still the most boring-and-correct answer for a JVM service
   - **Async / reactive:** [RxJava][rxjava] for anything where the natural shape is streams + composable async
   - **DI:** [Dagger][dagger] — compile-time, no reflection, the right call when startup time and predictability matter
   - **UI:** [JavaFX][javafx] for desktop UI when I need a real native client (rare, but when it's right, it's right)

**Honorable mention — JavaScript / TypeScript.** Not in my daily top 3 the way Python or Go is, but essential for anything user-facing or anywhere I'm reading the npm ecosystem. The runtime layer got interesting in 2026:

- **[Bun][bun]** — fast iteration on side projects, and the built-in test runner alone is worth it
- **[Deno][deno]** — secure-by-default, TypeScript without a build step, great for one-file scripts you actually want to share
- **Node** — still the right call when ecosystem coverage and long-term stability matter most

Worth keeping all three fluent — they each shine for a different shape of problem.

A typical setup snippet on a fresh machine:

{% highlight bash %}
# Python
uv python install 3.12
uv venv && uv add fastapi pytest

# Go
go install ...

# Java — JDK via mise, build with Gradle
mise use java@temurin-21
gradle -v

# JavaScript / TypeScript (honorable mention) — Bun, Deno, or Node
mise use node@22 && pnpm install
bun init
deno run --allow-net script.ts
{% endhighlight %}

[temurin]: https://adoptium.net/temurin/
[gradle]: https://gradle.org/
[spring]: https://spring.io/
[rxjava]: https://github.com/ReactiveX/RxJava
[dagger]: https://dagger.dev/
[javafx]: https://openjfx.io/
[bun]: https://bun.sh/
[deno]: https://deno.com/

### Top 3 databases

1. **PostgreSQL** — the default. With [`pgvector`][pgvector], Postgres now doubles as the vector DB for RAG, which removes a whole category of "do we add a separate vector store?" decisions.
2. **Redis** — caching, sessions, rate limiting, lightweight queues. Still the right tool the moment you need sub-millisecond reads.
3. **SQLite** — local dev, edge, and increasingly production (with [Litestream][litestream]. The "boring tech that punches above its weight" winner of the last few years.

For analytics and big data, **[BigQuery][bigquery]** is what I reach for at scale — serverless, separation of storage and compute, and the `bq` CLI makes it easy to drop into a normal terminal workflow alongside Postgres.

Containers — **OrbStack** instead of Docker Desktop. Lighter, faster, native.

Database client: [postico][postico]. API testing: I've moved off Postman to either Bruno or just `curl` + `jq`.

[postico]: https://eggerapps.at/postico/
[pgvector]: https://github.com/pgvector/pgvector
[litestream]: https://litestream.io/
[bigquery]: https://cloud.google.com/bigquery

## CTO workflow tools

This is what makes the post a *CTO* setup rather than an engineer setup. The team and async layer matters as much as the editor.

- **Linear** — issue tracking. Fast, opinionated, beats Jira for small/mid teams.
- **[Outline][outline]** — strategy docs, 1:1 templates, decision logs, runbooks. Open-source-friendly knowledge base; I prefer it to Notion when I want my team's wiki to feel less like a database and more like a wiki.
- **[Google Chat][gchat] + [WhatsApp][whatsapp]** — internal team comms in Google Chat (Workspace-native, less ceremony than Slack), WhatsApp for partners, vendors, and anyone outside the company. DND most of the day, notifications scoped per-channel.
- **Granola** — AI meeting notes; I stopped trying to write notes during calls and my conversations got better
- **[NordPass][nordpass]** — team vaults for shared infra credentials. Browser extension is the daily driver.
- **Raycast** — launcher, clipboard history, snippet expansion. Replaces Spotlight + Alfred + half of my muscle memory.
- **[OpenClaw][openclaw] + Google Calendar** — OpenClaw as the AI assistant front-end, Google Calendar as the source of truth. The combo lets me ask "what's my next free 90-minute block?" and book it without leaving the keyboard.

The big workflow point: I batch IC coding into 2–3 hour blocks early in the day, then flip into management mode. The setup supports that mode-switch (chat collapses, Linear opens, calendar surfaces).

[outline]: https://www.getoutline.com/
[gchat]: https://chat.google.com/
[whatsapp]: https://www.whatsapp.com/
[nordpass]: https://nordpass.com/

## Content creation & recording

As of my 2026 goal, I wanted to share my community what I did, so i plan to start content creation. The plan to use it for remote work, and start content creation along the way.

### OBS

[OBS Studio][obs] for anything that's not a quick async clip. I keep three scenes:

1. **Cam only** — for talking-head intros / outros
2. **Screen/Ipad + cam** — the workhorse, screen with a corner camera bubble; i use ipad when i need to draw stuff like architecture and explaining
3. **Screen/Ipad only** — when the demo is the focus

![OBS scene setup]({{ site.url }}/images/mac-setup-2026/obs-scene.png)
*Three scenes, hotkey switchable.*

### KeyCastr

[KeyCastr][keycastr] surfaces your keystrokes on screen. Indispensable for any recording where you're showing keyboard-driven work — terminal demos, IDE walkthroughs, anything where the viewer needs to see the shortcut.

{% highlight bash %}
brew install --cask keycastr
{% endhighlight %}

![KeyCastr in a recording]({{ site.url }}/images/mac-setup-2026/keycastr-demo.png)
*KeyCastr's overlay*



For quick async, [OBS][OBS] is still the lightest path. For edited content, Descript is the one I plan to use

[OBS]: https://obsproject.com/
[keycastr]: https://github.com/keycastr/keycastr

## Dotfiles & reproducibility

Everything I customize lives in a dotfiles repo, version-controlled. New Mac → clone, run install script, walk away. The script:

1. Installs Homebrew
2. Runs `brew bundle`
3. Symlinks dotfiles
4. Sets macOS defaults
5. Triggers VSCode / Cursor settings sync sign-in

That's the whole bootstrap. Twenty minutes from a clean install to a working environment.

## Closing thoughts

The biggest shift from my 2014 is AI, as it *empower my everyday workflow*. The editor, the terminal, and the desktop all have an AI surface, and I use each for a different shape of task.

The second-biggest shift is reproducibility. A `Brewfile` plus a dotfiles repo plus settings sync means the *setup itself* is a few commands, not a weekend.

I'll probably revisit this post later. Local model inference, on-device agents, and whatever Apple does with the AI side of macOS are all moving fast enough that something here will look quaint by 2027/2028.

If you set up your Mac differently — especially the AI part — I'd love to hear what you do. Comment below.
