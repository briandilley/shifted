<p align="center"><img src="assets/icon.png" width="160" alt="Shifted icon: three shifted mail cards with a red unread dot"></p>

# Shifted

Shifted is a desktop app that keeps several Gmail accounts in one window. You click an account in a sidebar to switch to it. Each account also has Google Calendar.

This repo does not contain the code of the app. It contains prompts that you give to an AI coding agent, for example Claude Code, Codex, or Cursor. The agent builds the app from the prompts.

Status: the prompts for version 1 are complete. Nobody has built the full app from them yet.

## Get started

### What you need

- git.
- Node.js 22.12 or newer. Run `node --version` to see your version.
- A coding agent that can read files and run commands in a folder. Examples: Claude Code (`claude`), Codex CLI (`codex`), or Cursor in agent mode.
- At least one Google account to test with.

### Build the app in three steps

1. Make an empty folder for the app, and start your agent in it:

   ```sh
   mkdir -p ~/src/shifted-app
   cd ~/src/shifted-app
   claude        # or: codex
   ```

2. Paste this message into the agent:

   ```text
   Clone https://github.com/briandilley/shifted.git into a temporary folder
   outside this one. Then read prompts/build-all.md in that clone and do
   what it says.
   ```

3. When the agent says that the build is complete, start the app and do the manual tests:

   ```sh
   npm run dev
   ```

   The list of manual tests is in `docs/manual-tests.md` in the app folder. It starts with the sign-in of your Google accounts.

The agent builds the 10 phases one after the other, with no stops for you. A full build takes hours, not minutes. The agent records its progress in `docs/build-progress.md`. It stops early in three cases: a tool is missing, a phase still fails after 3 repair attempts, or the spec has a conflict.

A full build runs many commands. If your agent asks before each command, the build waits for you. In Claude Code, answer the first question for each command with the option that does not ask again. Other agents have a similar mode. Use it only in the new app folder.

### If the build stops

Start your agent again in the same folder and paste this message:

```text
Read docs/spec/prompts/build-all.md and continue the build.
```

The agent reads `docs/build-progress.md` and continues from the first phase that is not done. If the folder has no `docs/spec/` yet, the build stopped in phase 00. In that case, paste the message of step 2 again.

### Sign in to Google

On macOS and Linux, passkeys do not work in Shifted. If Google asks for a passkey, click "Try another way". Then use your password and 2-Step Verification. You sign in to each account only once. Shifted keeps the sign-in after a restart.

### The optional prompts

- Prompt 10 makes signed installers and auto-update. It needs an Apple Developer account for macOS and a code-signing certificate for Windows.
- Prompt 11 tries Touch ID passkeys on macOS. It needs the signed build of prompt 10.

To run one of them, paste a message like this into your agent in the app folder:

```text
Read docs/spec/prompts/10-installers-and-updates.md and do what it says.
```

## Run the prompts one at a time

With this way, you watch and test each phase before the next one starts.

1. Clone this repo, and make an empty app folder:

   ```sh
   git clone https://github.com/briandilley/shifted.git ~/src/shifted-spec
   mkdir -p ~/src/shifted-app
   cd ~/src/shifted-app
   ```

2. Start your agent in the app folder. Paste this message, with the full path of your clone. Some agents do not expand `~`, so run `echo ~/src/shifted-spec` to get the full path.

   ```text
   Read /Users/you/src/shifted-spec/prompts/00-bootstrap.md and do what it says.
   The spec repo is at /Users/you/src/shifted-spec.
   ```

3. For each next prompt, start a fresh agent session. In Claude Code, type `/clear`. Paste this message, with the file name of the next prompt:

   ```text
   Read docs/spec/prompts/01-one-account.md and do what it says.
   ```

4. After each prompt, do the manual tests at the end of that prompt. If a test fails, tell the agent which test failed and what you saw. Then let it fix the phase before you go on.

Each phase ends with one commit, for example `Phase 01: one account`. Run `git log --oneline` to see the phases that are done.

### If the spec changes later

The app folder has a copy of the spec from the time of prompt 00. To get a newer spec, pull your clone of this repo. Then give the agent this message:

```text
The spec repo at /Users/you/src/shifted-spec changed. Compare it with
docs/spec/. Copy the changes into docs/spec/, and list the code changes
that the new spec needs. Do not change the code yet.
```

## The prompts

| Prompt | Phase |
| --- | --- |
| `prompts/build-all.md` | The master prompt. It runs the prompts 00 to 09 in order, each in a fresh context, and tracks the progress. |
| `prompts/00-bootstrap.md` | The code repo, the spec copy, `AGENTS.md`, Electron with React and TypeScript, Vitest, Playwright, and the fake Google test server. |
| `prompts/01-one-account.md` | One account: its session, the sign-in window, Gmail, the signed-out state, and sign-in that survives a restart. |
| `prompts/02-many-accounts.md` | The sidebar, the profile photo with a color ring, reordering, the context menu, the tab bar, and the configuration window. |
| `prompts/03-links.md` | The link rules, child windows, Calendar links, the default browser, page permissions, and downloads. |
| `prompts/04-unread-badges.md` | The Atom feed, the choice of feed for each account, the unread badges, and the app badge. |
| `prompts/05-notifications.md` | New-mail notifications from the feed, Calendar and Chat notifications, the switches, snooze, and quiet hours. |
| `prompts/06-mailto.md` | The account picker, the new message, and the default email app. |
| `prompts/07-shortcuts-and-background.md` | Keyboard shortcuts, background loading, hide on close, the tray icon, and open at login. |
| `prompts/08-hardening.md` | Security, crash and offline states, accessibility, and tests on all three platforms. |
| `prompts/09-release.md` | A trace of every rule of the spec, one list of manual tests, and the README of the code repo. |
| `prompts/10-installers-and-updates.md` | Optional: signed installers and auto-update. |
| `prompts/11-touch-id-passkeys.md` | Optional: Touch ID passkeys on macOS. It starts with a prototype. |

## How each prompt is built

Each prompt in `prompts/` stands on its own. It has these parts:

1. Goal: the result of the phase, in one or two sentences.
2. Read first: the spec files that the agent reads before it starts. The agent reads them from `docs/spec/` in the code repo.
3. Scope: what the agent builds in this phase.
4. Out of scope: what the agent does not build in this phase.
5. Automated tests: the unit tests and end-to-end tests that the agent adds in this phase.
6. Manual tests: the steps that you do by hand to make sure that the phase works.

## What is in this repo

- `PRODUCT.md`: what the app does and what it does not do.
- `BUILD.md`: the stack, the build tools, the icon, and how the agent tests each phase.
- `GLOSSARY.md`: the words that the documents use, with one meaning for each word.
- `PRIOR-ART.md`: what Shift does, and what Shifted keeps from it and drops.
- `docs/adr/`: the decisions, with the reason for each one.
- `prompts/`: the build prompts, one prompt for each phase, in number order.
- `assets/`: the app icon, as SVG and as a 1024-pixel PNG.
- `docs/agents/`: the configuration for the agent skills that we use to write these documents.
