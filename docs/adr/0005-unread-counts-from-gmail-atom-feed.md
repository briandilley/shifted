# Unread counts from the Gmail Atom feed

Shifted gets the unread count of each account from the Gmail Atom feed (`https://mail.google.com/mail/u/0/feed/atom/<label>`). It sends the request with the session of the account, about every 60 seconds. It does not read the count from the Gmail page.

We chose the feed because it works when Gmail is asleep, and because it does not break when Google changes the markup of the Gmail page. A count read from the page needs Gmail to stay loaded, so it does not work with the "Background loading" values that put Gmail to sleep. In a test on 2026-10-21, the feed returned HTTP 200 for a personal account and for a Workspace account, with only the cookies of the Electron session.

The feed for the whole inbox does not match the number that Gmail shows when the inbox uses category tabs. In the test, the whole-inbox feed counted thousands of unread messages, and the Primary feed (`%5Esq_ig_i_personal`) counted 2, which matched "Inbox (2)" in the Gmail page title. So Shifted learns, for each account, which feed matches Gmail. The first time Gmail loads, Shifted compares the number in the page title with the counts of both feeds.

Shifted also makes its new-mail notifications from the same feed, so that they work when Gmail is asleep.

## Consequences

- The Atom feed is an old Google feature with little documentation. Google can remove it. If the feed stops working, Shifted shows a warning mark on the badge, not a wrong number.
- Inbox types other than the default (for example "Important first" or "Multiple inboxes") can need the per-account override in configuration.
