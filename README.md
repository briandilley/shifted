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

### Step 1: Clone this repo

This repo is the spec repo. Clone it to a folder of your choice. The examples use `~/src`.

```sh
mkdir -p ~/src
git clone https://github.com/briandilley/shifted.git ~/src/shifted-spec
```

Get the full path of the clone. You need it in step 3, because some agents do not expand `~`.

```sh
echo ~/src/shifted-spec
```

### Step 2: Make the code repo

The code of the app goes in a second, empty folder. This folder is the code repo.

```sh
mkdir -p ~/src/shifted-app
cd ~/src/shifted-app
git init
```

### Step 3: Run prompt 00

Start your agent in the code repo:

```sh
cd ~/src/shifted-app
claude        # or: codex
```

Paste this message into the agent. Put your full path in place of `/Users/you/src/shifted-spec`.

```text
Read /Users/you/src/shifted-spec/prompts/00-bootstrap.md and do what it says.
The spec repo is at /Users/you/src/shifted-spec.
```

Some agents cannot read files outside their folder. For those agents, copy the prompt to the clipboard and paste it, followed by the line "The spec repo is at ...".

```sh
cat ~/src/shifted-spec/prompts/00-bootstrap.md | pbcopy                     # macOS
cat ~/src/shifted-spec/prompts/00-bootstrap.md | xclip -selection clipboard  # Linux
Get-Content ~\src\shifted-spec\prompts\00-bootstrap.md | Set-Clipboard      # Windows PowerShell
```

Prompt 00 copies the spec into `docs/spec/` in the code repo. It also writes `AGENTS.md` there. From now on, every prompt is in the code repo, so the later messages are short.

When the agent finishes, do the manual tests at the end of the prompt:

```sh
npm install
npm run dev
npm test
npm run test:e2e
```

### Step 4: Run prompts 01 to 09

Start a fresh agent session for each prompt. In Claude Code, type `/clear`. In Codex or Cursor, start a new session or chat.

Paste this message. Change the file name for each prompt, in number order.

```text
Read docs/spec/prompts/01-one-account.md and do what it says.
```

After the agent finishes, do the manual tests at the end of that prompt. If a manual test fails, tell the agent which one, and what you saw:

```text
Manual test 3 of prompt 01 failed. After the sign-in, the sign-in window
stayed open and Gmail did not show in the main window. Fix it, then run
npm test and npm run test:e2e again.
```

Each phase ends with one commit, for example `Phase 01: one account`. Run `git log --oneline` to see the phases that are done.

### Step 5: The optional prompts

- Prompt 10 makes signed installers and auto-update. It needs an Apple Developer account for macOS and a code-signing certificate for Windows.
- Prompt 11 tries Touch ID passkeys on macOS. It needs the signed build of prompt 10.

### Sign in to Google

On macOS and Linux, passkeys do not work in Shifted. If Google asks for a passkey, click "Try another way". Then use your password and 2-Step Verification. You sign in to each account only once. Shifted keeps the sign-in after a restart.

### If the spec changes later

The code repo has a copy of the spec from the time of prompt 00. To get a newer spec, pull it first:

```sh
git -C ~/src/shifted-spec pull
```

Then give the agent this message:

```text
The spec repo at /Users/you/src/shifted-spec changed. Compare it with
docs/spec/. Copy the changes into docs/spec/, and list the code changes
that the new spec needs. Do not change the code yet.
```

## The prompts

| Prompt | Phase |
| --- | --- |
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
