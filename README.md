# Shifted

Shifted is a desktop app that keeps several Gmail accounts in one window. You click an account in a sidebar to switch to it.

This repo does not contain the code of the app. It contains prompts that you give to an AI coding agent, for example Claude Code, Codex, or Cursor. The agent builds the app from the prompts.

Status: in design. The prompts are not complete. Do not use them yet.

## What is in this repo

- `PRODUCT.md`: what the app does and what it does not do.
- `GLOSSARY.md`: the words that the documents use, with one meaning for each word.
- `PRIOR-ART.md`: what Shift does, and what Shifted keeps from it and drops.
- `docs/adr/`: the decisions, with the reason for each one.
- `prompts/`: the build prompts, one prompt for each phase, in number order.
- `docs/agents/`: the configuration for the agent skills that we use to write these documents.

## How each prompt is built

Each prompt in `prompts/` stands on its own. It has these parts:

1. Goal: the result of the phase, in one or two sentences.
2. Read first: the spec files that the agent reads before it starts. The agent reads them from `docs/spec/` in the code repo of the app.
3. Scope: what the agent builds in this phase.
4. Out of scope: what the agent does not build in this phase.
5. Hand checks: the steps that you do by hand to make sure that the phase works.

## How to use the prompts

1. Read `PRODUCT.md` to make sure that Shifted is the app you want.
2. Clone this repo: `git clone https://github.com/briandilley/shifted.git`.
3. Make an empty folder for the code of the app. This folder is the code repo. It is not the clone of this repo.
4. Open a fresh agent session in the code repo for each prompt, in number order. Give the agent the path of your clone of this repo in the first session.
5. Prompt 00 copies `PRODUCT.md`, `GLOSSARY.md`, `PRIOR-ART.md`, and `docs/adr/` into `docs/spec/` in the code repo. The later prompts read the spec from there.
6. After the agent finishes a prompt, do the hand checks at the end of that prompt.
7. If a hand check fails, tell the agent which check failed before you go to the next prompt.
8. The last prompt is optional. Use it only if you want signed installers and auto-update.
