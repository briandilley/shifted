# Shifted: product description

This file tells the build agent what Shifted does. Every prompt in `prompts/` points to it. The words in this file have the meanings in `GLOSSARY.md`.

## Name

- Name: Shifted (working name).
- Bundle identifier: not decided.

All prompts read the name from this section. To rename the app, change it here.

## What Shifted is

Shifted is a desktop app that keeps several Gmail accounts in one window. Each account shows Gmail and Google Calendar, with Google's own web interface. You switch between accounts from a sidebar.

## Google apps in each account

- Each account shows two Google apps: Gmail (`https://mail.google.com`) and Google Calendar (`https://calendar.google.com`).
- Gmail and Calendar in one account use the same session, so you sign in once for both.
- Google Chat and Google Meet appear inside Gmail where Google puts them. Shifted does not show them as separate apps.
- Shifted does not show any other Google app, for example Drive, Docs, or Contacts.

## Platforms

Shifted runs on macOS, Windows, and Linux. All three platforms get the same features.

Shifted is an Electron app written in TypeScript. See `docs/adr/0003-electron-and-typescript.md`.

## Window and sidebar

Shifted has one main window. The sidebar is on the left. The content area is on the right. The content area shows one Google app of one account at a time.

```
+----+---------------------------+
| [A]|                           |
|  M |                           |
|  C |     Gmail of account A    |
| [B]|                           |
| [C]|                           |
|    |                           |
| [+]|                           |
+----+---------------------------+
```

- The sidebar shows one avatar for each account, in a vertical list. The add-account button is below the list.
- Under the avatar of the selected account, the sidebar shows two small icons: Gmail (M in the drawing) and Calendar (C in the drawing). Click an icon to show that Google app in the content area.
- Each account remembers the Google app that you used last. When you switch back to that account, Shifted shows that Google app.
- There is no bar above the content area. Gmail and Calendar use the full height of the window.

## Supported accounts

Shifted supports every Google account that has Gmail:

- Personal accounts that end in `@gmail.com`.
- Google Workspace accounts, for example `name@company.com`.
- Google Workspace accounts that sign in through a company identity provider, for example Okta or Microsoft Entra ID.

Example: you add `name@company.com`. Google sends you to `company.okta.com` to sign in. Shifted shows the Okta page in the app, because it is part of the sign-in. After the sign-in, Shifted returns to Gmail.

## Distribution

- The main prompts build a version of Shifted that you run from source on your own computer.
- The last prompt is optional. It adds signed installers and auto-update for macOS, Windows, and Linux.
- The optional prompt needs an Apple Developer account for macOS and a code-signing certificate for Windows.

## Accounts and sessions

- Each account has its own session, with its own cookies and site data. See `docs/adr/0002-isolated-session-per-account.md`.
- A session persists on disk. After you quit Shifted and open it again, each account is still signed in.
- If you sign out of Gmail in one account, the other accounts stay signed in.
- When you remove an account from Shifted, Shifted deletes the session of that account from disk.

## Links

Shifted decides where each link opens. The rules apply to links that you click and to pages that Gmail or Calendar opens on their own.

1. A Gmail link opens in the Gmail Google app of the same account.
2. A Calendar link opens in the Calendar Google app of the same account. Example: in Gmail, you click an event invitation that goes to `calendar.google.com`. Shifted switches the account to its Calendar Google app and shows the event.
3. During sign-in, every page stays in Shifted. This includes Google's sign-in pages and the pages of a company identity provider. Sign-in starts when Google's sign-in page loads, and it ends when Gmail or Calendar loads.
4. A Gmail pop-out opens in a child window that uses the session of the same account. Examples: pop-out compose, the print view, and "Open in new window".
5. Every other link opens in the default browser of the computer. This includes Google Docs, Drive, Meet, and Contacts.

Rule 5 has a known cost. Example: you click a Google Docs link in account B. The link opens in your default browser, which can be signed in as account A. Shifted accepts this cost, because it is not a web browser.

## Privacy and network

- Shifted has no server of its own.
- Shifted has no sign-up, no license check, no telemetry, no crash reporting, and no settings sync.
- All data of Shifted stays on the computer: the account list, the preferences, and the sessions.
- The only network traffic is the traffic of Google's own pages, the pages of a company identity provider during sign-in, and the update check if the build has one.

## What Shifted is not

- Shifted is not a web browser. It has no address bar and no browser tabs, and you cannot use it to visit websites in general.
- Shifted is not a mail client. It does not call the Gmail API, and it does not draw mail itself. See `docs/adr/0001-wrap-gmail-web-interface.md`.

## Open questions

These items are not decided yet. The prompts will not be complete until each item has an answer.

- Tooling: package manager, build tool, and the UI library for the sidebar.
- How you sign in to Google inside the app.
- How the app gets the unread count for each account.
- The feature list for the first version.
- How the build agent gets the files from this repo.
