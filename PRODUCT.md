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

The build tools are:

- npm, for packages and scripts.
- electron-vite, for the development server and the build of the main process, the preload scripts, and the renderer.
- electron-builder, for installers on all three platforms. Its electron-updater part gives auto-update on macOS, Windows, and Linux (AppImage). The other choice, Electron Forge, has no auto-update for Linux.

## Version 1 features

Version 1 has these features, in addition to the sidebar, the tab bar, and adding and removing accounts. Each feature has its own section below when its details are decided.

- Unread badges: an unread count on each account in the sidebar, and the total on the app icon.
- Notifications: desktop notifications for new mail and for Calendar reminders, in every account.
- `mailto:` handler: Shifted can be the default email app of the computer.
- Keyboard shortcuts: jump to an account, move to the previous or next account, and switch between the Gmail and Calendar tabs.

## Window and sidebar

Shifted has one main window. The sidebar is on the left. The tab bar is at the top of the right side. The content area is below the tab bar. The content area shows one Google app of one account at a time.

```
+----+---------------------------+
| [A]| [ Gmail ] [ Calendar ]    |
| [B]+---------------------------+
| [C]|                           |
|    |   Calendar of account A   |
| [+]|                           |
+----+---------------------------+
```

- The sidebar shows one avatar for each account, in a vertical list. The add-account button is below the list.
- The tab bar shows one tab for each Google app of the selected account: Gmail and Calendar. Click a tab to show that Google app in the content area.
- Each account remembers the Google app that you used last. When you switch back to that account, Shifted shows that Google app.

## Unread badges

- Each account in the sidebar shows its unread count on its avatar. If the count is 0, Shifted shows no badge.
- The unread count of an account is the number that Gmail shows next to Inbox for that account.
- The app icon shows the sum of the unread counts of all accounts. On macOS this is the Dock badge. On Windows it is an overlay on the taskbar icon. On Linux it is the launcher badge, on desktops that support one.
- Shifted gets each unread count from the Gmail Atom feed with the session of the account, about every 60 seconds, and when you switch accounts. See `docs/adr/0005-unread-counts-from-gmail-atom-feed.md`.
- When the inbox of an account uses category tabs, Gmail counts only the Primary tab. Shifted learns this for each account the first time Gmail loads, and then reads the matching feed.
- Each account has an override in configuration: "Primary", "Whole inbox", or "Important".
- If Shifted cannot read the feed of an account, for example because the account is signed out, the badge of that account shows a warning mark instead of a number.

## Notifications

New mail:

- Shifted makes the new-mail notifications itself, from the same Atom feed as the unread badges. They work when Gmail is asleep.
- Shifted shows one notification for each new unread message in the feed that the unread count uses. The notification shows the account, the sender, and the subject.
- When Shifted starts, and after you sign in to an account, Shifted reads the feed once without notifications. This prevents a burst of old messages.
- When you click a new-mail notification, Shifted comes to the front, selects the account, shows the Gmail tab, and opens that message.
- If Gmail's own new-mail notifications are on in Gmail's settings, you get two notifications for each message. The help text of Shifted tells you to keep Gmail's own new-mail notifications off.

Calendar and Chat:

- Calendar reminders and Google Chat messages come from the web notifications of the Calendar and Gmail pages. Shifted allows these notifications and shows them as desktop notifications.
- When you click one of these notifications, Shifted comes to the front and selects the account and the tab that sent it.

## Background loading

A configuration value, "Background loading", decides which Google apps stay loaded when you do not look at them. A loaded Google app uses roughly 200 to 500 MB of memory.

| Value | What stays loaded | What sleeps |
| --- | --- | --- |
| Everything (default) | Gmail and Calendar of every account. | Nothing. |
| Calendars only | Calendar of every account, and the Google apps of the selected account. | Gmail of each other account, 5 minutes after you leave it. |
| Selected account only | The Google apps of the selected account. | All Google apps of each other account, 5 minutes after you leave it. |

- A sleeping Google app loads again when you open it.
- Unread badges stay current in every value, because they do not need Gmail to be loaded.
- With "Selected account only", Calendar reminders come only from the selected account.

## Supported accounts

Shifted supports every Google account that has Gmail:

- Personal accounts that end in `@gmail.com`.
- Google Workspace accounts, for example `name@company.com`.
- Google Workspace accounts that sign in through a company identity provider, for example Okta or Microsoft Entra ID.

Example: you add `name@company.com`. Google sends you to `company.okta.com` to sign in. Shifted shows the Okta page in the app, because it is part of the sign-in. After the sign-in, Shifted returns to Gmail.

## Sign-in

- You sign in on Google's own sign-in page, in a sign-in window. The sign-in window is a small separate Shifted window, about 480 by 720 points, that uses the session of the account.
- The top of the sign-in window shows one line of help from Shifted. The rest of the window shows Google's sign-in page.
- While the sign-in window is open, the content area of the account says that sign-in is in progress.
- When Gmail loads in the sign-in window, Shifted closes the sign-in window and shows Gmail of that account in the main window.
- If Google signs an account out, the content area of that account says that the account is signed out and shows a "Sign in again" button. The button opens the sign-in window for that account. Shifted never shows Google's sign-in page in the content area.
- Shifted never pretends to be another browser. See `docs/adr/0004-honest-user-agent-for-sign-in.md`.
- Passkeys do not work in Shifted. When Google asks for a passkey, you click "Try another way" and use your password and 2-Step Verification. The sign-in screen of Shifted tells you this before you start.

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
2. A Calendar link opens in the Calendar Google app of the same account. Example: in Gmail, you click an event invitation that goes to `calendar.google.com`. Shifted switches to the Calendar tab of the same account and shows the event. It does not open a new window.
3. In the sign-in window, every page stays in the sign-in window. This includes Google's sign-in pages and the pages of a company identity provider.
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

- The UI library for the sidebar.
- How the app gets the unread count for each account.
- Notifications: per-account switches, snooze, and quiet hours.
- `mailto:` handler: details.
- Keyboard shortcuts: the exact keys.
