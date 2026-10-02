# Retime for Claude

Schedule meetings and manage your [Retime](https://retime.so) calendar from a Claude conversation. Claude finds free slots, confirms the attendee's time zone, proposes up to three options, and books, moves, or cancels a meeting only after you confirm.

## What is in this plugin

- A connector to the Retime MCP server (`.mcp.json`). This is the only network destination the plugin uses. It runs no scripts, hooks, or local programs and installs no packages.
- Four skills that teach Claude how to use the connector safely.

## Install

- **Claude (web, desktop, mobile) and Cowork:** open **Customize > Plugins**, find **Retime** under **Discover**, and add it.
- **Claude Code:** install it from the directory (it syncs from your account), or test a local copy with `claude --plugin-dir ./retime-claude-plugin`.

## Connect your Retime account

1. Open the plugin's **Connectors** tab and select **Connect** on Retime.
2. A Retime sign-in page opens (OAuth 2.1; Claude registers itself as a client automatically).
3. Sign in with your Retime email and password. If you signed up with Google and have no password, choose **Email me a sign-in link**, then open the link in the email (single use, valid 15 minutes).
4. Review the consent screen and select **Allow**.

No API keys or secrets are stored in this plugin. Access tokens last 1 hour and refresh for 30 days. You can disconnect at any time from the Connectors tab.

## Skills

| Skill | What it does |
| :- | :- |
| `schedule-meeting` | Finds free slots, confirms time zone, proposes up to 3, books after your yes |
| `manage-bookings` | Lists and reviews bookings; reschedules or cancels after explicit confirmation |
| `set-up-event-type` | Creates a new scheduling link with sensible defaults |
| `weekly-schedule-brief` | Read-only summary of upcoming bookings |

## Tools provided by the Retime connector

| Tool | Type | Purpose |
| :- | :- | :- |
| `list_event_types` | read-only | List your scheduling links |
| `get_availability` | read-only | Free slots for an event type (up to 31 days) |
| `list_bookings` | read-only | Upcoming, past, or cancelled bookings |
| `get_booking` | read-only | Details of one booking |
| `create_booking` | write | Book a slot (idempotent) |
| `reschedule_booking` | write | Move a booking to a free slot |
| `cancel_booking` | destructive | Cancel a booking and notify attendees |
| `create_event_type` | write | Create a one-on-one scheduling link |

## Example prompts

- "Book a 30-minute intro call with Dana Cole (dana@example.com) next week. She is in Toronto."
- "What is on my schedule this week?"
- "Move my Thursday 2pm call with Sam to Friday morning."
- "Cancel tomorrow's coffee chat with Priya and tell her I am sick."
- "Create a 45-minute Zoom event type called Design Review."

## Privacy

When you use the plugin, Claude sends your requests to Retime through the connector: event type details, date ranges, and, for bookings, the attendee's name, email, time zone, and notes you give. Retime returns your event types, free slots, and bookings. Nothing is sent anywhere else. Retime handles data under its [privacy policy](https://retime.so/privacy). The connector can only access the account you sign in with.

## Safety

Claude is instructed to never book, reschedule, cancel, or create an event type without your explicit confirmation, never to invent email addresses, and to always show times with a time zone. The Retime server also tells Claude to confirm before write actions.

## Support

Questions or problems: https://retime.so/support. Plugin source: this repository.

## License

MIT. See [LICENSE](LICENSE). Changes: [CHANGELOG.md](CHANGELOG.md).
