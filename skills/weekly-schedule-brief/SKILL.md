---
name: weekly-schedule-brief
description: Summarize the user's upcoming Retime bookings, such as "what's on my schedule this week", "brief me on tomorrow's meetings" or "who am I meeting next week". Read-only; never changes bookings.
---

# Retime schedule brief

Tools: `list_bookings`, `get_booking` (read-only). This skill never books, moves or cancels.

## Steps

1. **Set the window.** Default: today through the next 7 days. Convert phrases like "this week" or "tomorrow" to `from`/`to` dates (YYYY-MM-DD). If the user's time zone is unknown, ask once.
2. **Fetch.** Call `list_bookings` with `status` `upcoming`, `from`, `to`, `limit` 50. Follow `cursor` until the window is covered or you reach about 100 bookings; if more exist, say the brief is truncated.
3. **Add detail only if needed.** Call `get_booking` for a booking only when the user asks about it.
4. **Write the brief.** Group by day in order. Per booking: time range with time zone, event type, attendee name, location or meeting link. Then add 2-3 lines:
   - total meetings and total hours
   - back-to-back stretches (no gap) and the busiest day
   - days with no bookings
5. **Offer next steps.** Offer reschedule or cancel via `manage-bookings`. Do not act yet.

## Rules

- Show all times in the user's time zone, with the zone named. Mention the attendee's zone only if the data gives one and it differs.
- Report only what the tools return. No invented attendees, agenda, or prep notes.
- No bookings found: say "Nothing booked from <date> to <date>." Stop.

## When a tool fails

`isError: true` results carry a plain message. Show it, retry once if it looks like a bad date range, otherwise stop and suggest reconnecting the Retime connector if sign-in expired.
