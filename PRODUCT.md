# Shifted: product description

This file tells the build agent what Shifted does. Every prompt in `prompts/` points to it.

## Name

- Name: Shifted (working name).
- Bundle identifier: not decided.

All prompts read the name from this section. To rename the app, change it here.

## What Shifted is

Shifted is a desktop app that keeps several Gmail accounts in one window. Each account shows Gmail's own web interface. You switch between accounts from a sidebar.

## What Shifted is not

- Shifted is not a web browser. It has no address bar and no browser tabs, and you cannot use it to visit websites in general.
- Shifted is not a mail client. It does not call the Gmail API, and it does not draw mail itself. See `docs/adr/0001-wrap-gmail-web-interface.md`.

## Open questions

These items are not decided yet. The prompts will not be complete until each item has an answer.

- Operating systems and tech stack.
- How each account keeps its own sign-in.
- Which kinds of Google accounts the app supports.
- Which links stay in the app and which links open in the default browser.
- How you sign in to Google inside the app.
- How the app gets the unread count for each account.
- The feature list for the first version.
- How the build agent gets the files from this repo.
