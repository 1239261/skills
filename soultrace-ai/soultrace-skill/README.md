# SoulTrace Agent Skill

Agent skill for [SoulTrace](https://soultrace.app) — an adaptive personality assessment using a 5-color psychological model with Bayesian active learning.

## Install

```bash
npx skills add soultrace-ai/soultrace-skill
```

## What it does

Lets AI agents take the SoulTrace personality test on behalf of users. The agent presents 24 questions, collects 1-7 Likert scale answers, and returns a personality archetype from 25 possible types based on a 5-color model (White, Blue, Black, Red, Green).

## API

The skill calls `POST https://soultrace.app/api/agent` with a stateless protocol — all accumulated answers are sent with each request.

See [skills/soultrace/SKILL.md](skills/soultrace/SKILL.md) for full API documentation.
