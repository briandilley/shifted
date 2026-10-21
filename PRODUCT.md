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

## Accounts and sessions

- Each account has its own session, with its own cookies and site data. See `docs/adr/0002-isolated-session-per-account.md`.
- A session persists on disk. After you quit Shifted and open it again, each account is still signed in.
- If you sign out of Gmail in one account, the other accounts stay signed in.
- When you remove an account from Shifted, Shifted deletes the session of that account from disk.

## What Shifted is not

- Shifted is not a web browser. It has no address bar and no browser tabs, and you cannot use it to visit websites in general.
- Shifted is not a mail client. It does not call the Gmail API, and it does not draw mail itself. See `docs/adr/0001-wrap-gmail-web-interface.md`.

## Open questions

These items are not decided yet. The prompts will not be complete until each item has an answer.

- Tech stack.
- Which links stay in the app and which links open in the default browser.
- How you sign in to Google inside the app.
- How the app gets the unread count for each account.
- The feature list for the first version.
- How the build agent gets the files from this repo.
