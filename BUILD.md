# Shifted: how to build it

This file tells the build agent how to build and test Shifted. `PRODUCT.md` tells it what Shifted does. The words in this file have the meanings in `GLOSSARY.md`.

## Stack

Shifted is an Electron app written in TypeScript. See `docs/adr/0003-electron-and-typescript.md`.

The build tools are:

- npm, for packages and scripts.
- electron-vite, for the development server and the build of the main process, the preload scripts, and the renderer.
- React with TypeScript, for the screens of Shifted: the sidebar, the tab bar, the account picker, the top line of the sign-in window, and the configuration. Styles are plain CSS modules, with no component kit. Google's pages are not part of this.
- electron-builder, for installers on all three platforms. Its electron-updater part gives auto-update on macOS, Windows, and Linux (AppImage). The other choice, Electron Forge, has no auto-update for Linux.

## Tests

Each phase must prove that it works in three ways.

1. Unit tests with Vitest, for logic that needs no Google page. Examples: the link rules, the parsing of the Atom feed, the choice of the unread-count feed, the detection of new messages, snooze, quiet hours, the parsing of `mailto:` links, the configuration file, and the shortcut table.
2. End-to-end tests with Playwright for Electron. They start Shifted and drive its own screens: the sidebar, the tab bar, the account picker, the sign-in window, and the configuration window. In these tests, Shifted loads fake local pages in place of Gmail, Calendar, Google sign-in, and the Atom feed. A local test server serves the fake pages.
3. Hand checks, for what a test cannot do: a real Google sign-in, real Gmail and Calendar, and real desktop notifications.

Rules for tests:

- All Google URLs of Shifted are in one module. In test mode, that module points to the local test server. Nothing else in Shifted contains a Google URL.
- Tests never use a real Google account, and the code repo never contains a password or a cookie.
- A phase is done only when `npm test` and `npm run test:e2e` pass and every hand check of the phase passes.

## Rules for the build agent

- Never edit the files in `docs/spec/`. They are a copy of the spec repo.
- If the spec is silent about something, pick the simplest choice that fits the spec. Write the choice and the reason in `docs/decisions.md` in the code repo.
- If the spec conflicts with what you find, stop and ask the person. Example: Google changed a page, and a rule in the spec no longer works.
- Use the words of `docs/spec/GLOSSARY.md` in code, comments, and commit messages.
- End each phase with one git commit that names the phase, for example `Phase 03: unread badges`.
