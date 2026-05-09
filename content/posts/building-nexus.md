---
title: 'Building Nexus: A Multi-Agent AI Orchestration System'
date: 2026-05-09
draft: false
description: 'How I built a multi-agent AI system that coordinates specialized agents across content, development, and infrastructure, running 24/7 on hardware I own.'
tags:
  - ai
  - agents
  - infrastructure
  - osint
  - automation
---

i spent the last few weeks building a multi-agent ai system in my basement. not a chatbot wrapper. not another "ai agent framework" that's really just a prompt chain. an actual operating system for coordinating specialized agents across content, development, and infrastructure.

here's what i built, why, and the decisions that mattered.

<!--more-->

## the problem

most "ai agent" setups i see are single-threaded. one model, one context window, one shot at getting it right. if you want to do multiple things (write code, run tests, monitor infrastructure, or publish content), you either cram it all into one agent or you manually context-switch between sessions.

that's not an agent system. that's a fancier chat interface.

i wanted something where specialized agents exist permanently, each with its own domain, model, and memory. an orchestrator that decomposes requests. a pipeline that routes work to the right agent. escalation paths when things fail. all running 24/7 on hardware i own.

## the architecture

hub and spoke. one orchestrator agent receives every input, classifies urgency, decomposes tasks, and dispatches to domain-specific workers. workers report back. the orchestrator synthesizes and returns.

urgency is tiered t0 through t5. a disk filling up is t0, immediate action. a blog post draft is t3, get to it within 24 hours. system health logs are t5, informational only.

this prevents the system from being paralyzed by trivia. a compliance flag on a content draft doesn't interrupt a running code build. it gets queued at its tier and handled when resources are available.

## tech stack

the server is an amd machine with an nvidia gpu, ░░░░ gb ram, and ░░░░ tb of nvme storage running linux. local inference runs through ollama with a mix of models for different capability tiers. the gateway layer is openclaw ░░░░, which handles routing, session management, and channel integration.

cloud apis are used selectively, only for the tasks where local models aren't sufficient. cloud inference costs ░░░░ per month at current usage. the entire local inference layer costs zero marginal dollars.

agent frameworks are langgraph for state management and crewai for domain pipeline orchestration. secrets live in hashicorp vault. the human interface is a discord bot. all vault content is version-controlled in git and pushed to a remote hourly.

## the models

not all models are equal. local models handle the volume. cloud models handle the edge cases.

the workhorse is a ░░░░-parameter local model that handles content writing, code generation, and compliance review. a smaller local model runs the always-on monitors and the watch agent. the orchestrator runs on a cloud api for reliability.

the fallback chain is critical. if the primary model is down, the system degrades to the next available tier, not to zero. local models fall back to other local models. cloud models fall back to cheaper cloud models.

## phase 1: core agent system

the first build was the skeleton. orchestrator, synthesis agent, urgency classification, and the agent definition format.

every agent is defined in a single markdown file with identity, responsibilities, memory paths, escalation rules, and a system prompt. version-tracked. no database schema, no yaml config, just markdown in a git repo.

this turned out to be the right call. agents are easy to create, modify, and document. the format is trivially parseable and human-readable. when an agent misbehaves, the definition is right there with a changelog.

## phase 2: infrastructure

the infrastructure monitor was the first permanent agent. it polls ollama, ram, cpu, disk, docker, network, and api spend every ░░░░ minutes. enforces api budget caps. escalates critical failures immediately.

this paid for itself within hours. a runaway build process filled the disk to ░░░░ percent. the monitor caught it, posted a t0 alert, and the orchestrator suspended the build pipeline until the issue was resolved. without it, the entire system would have locked up overnight.

## phase 3: development pipeline

coder agent handles code generation and refactoring. testing agent validates every coder output by running the test suite. after three consecutive failures, the planner/debugger agent takes over. this is the only agent that uses the most capable cloud model, because it handles the hard problems.

dependency watcher scans all active projects nightly for cves and outdated packages. critical vulnerabilities post as t1 alerts.

this pipeline has caught things that would have been embarrassing in production. a mismatched dependency version. a test suite that passed locally but failed on clean install. edge cases in error handling that the coder missed.

## phase 4: content pipeline

the content pipeline was the most fun to build. watch agent monitors news sources every ░░░░ hours and scores relevance against configured content pillars. seo agent researches keywords before long-form content. content writer drafts in a defined voice. compliance review gates everything against platform tos, legal risk, and accuracy checks.

the watch agent surfaced the dirty frag disclosure from a sans diary entry before it hit the broader news cycle. that alone justified the pipeline.

social media adaptation and multi-platform publishing are staged. the infrastructure is built but the api integrations aren't wired yet.

## key decisions

**local-first.** cloud inference is convenient but it creates dependency. i wanted a system that could operate offline for the core workflows. local models handle ░░░░ percent of the volume. cloud apis are reserved for tasks that genuinely need the capability delta. this also means the monthly operating cost is predictable and low.

**deepseek over claude for orchestration.** the orchestrator handles high volume: every message, every dispatch, every synthesis. at scale, claude's per-token cost adds up fast. deepseek provides comparable reasoning at a fraction of the cost. claude is reserved for the planner/debugger agent where the additional reasoning capability genuinely matters.

**discord as the human interface.** not a custom web ui, not a slack app, not a terminal. discord was already where i spend time. the bot posts to channels, threads organize conversations, reactions trigger workflows. it's not elegant but it works and i don't have to maintain a frontend.

**git as the memory layer.** agent definitions, context documents, project records: all in git. hourly commits for active sections, nightly commits for archives. this means rollback is always `git checkout`. no database corruption, no migration scripts, no backup strategy beyond what git already provides.

**terminal noir aesthetic.** dark background, cream text, muted gold borders. no gradients, no rounded corners, no sans-serif fonts. every interface i build follows the same visual language. it started in the aleph tui and carried into the design system for anything nexus-produced.

## current capabilities

what the system can do right now:

- accept any input via discord, classify urgency, and route to the right agent
- generate, test, and debug code across multiple projects
- write content in a consistent voice, review for compliance, and prepare for publishing
- monitor infrastructure and api spend 24/7
- scan dependencies nightly for vulnerabilities
- watch security news and surface actionable stories
- escalate failures through defined paths with fallback models

## what's next

phase 5: publishing and social media automation. the agents are defined and tested. they need api integrations to actually post.

the osint tool (aleph) is being refactored into a modular provider architecture. once that's stable, it feeds back into the watch agent and content pipeline.

and i want to improve the agent definition format: make escalation rules more expressive and add circuit breaker patterns for cascading failures.

## the point

this isn't about replacing developers or writers. it's about having reliable, specialized agents that handle the work i'd otherwise do manually: monitoring, triaging, drafting, testing so i can focus on the parts that need actual judgment.

the system runs on hardware i own, costs pocket change per month, and gets better every time i add an agent or refine a prompt.

that's the bar. not "can it write a haiku." can it take over a shift.
