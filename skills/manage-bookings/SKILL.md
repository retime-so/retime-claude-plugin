---
name: manage-bookings
description: Review, look up, reschedule or cancel existing Retime bookings. Use when the user asks what is booked, wants details of a meeting, needs to move a meeting to another time, or wants to cancel one. Always confirms before rescheduling or cancelling.
---

# Manage Retime bookings

Tools: `list_bookings`, `get_booking`, `get_availability`, `reschedule_booking`, `cancel_booking`.

## Look up

1. Call `list_bookings` with `status` (`upcoming` default, `past`, or `cancelled`), plus `from`/`to` (YYYY-MM-DD) if the user gave a window. Use `cursor` to fetch more pages only if needed.
2. Show a compact list: date, time with time zone, event type, attendee, status. Keep booking ids internal unless the user asks.
3. For detail on one booking, call `get_booking` with its `booking_id`.
4. If several bookings match the user's description, list them and ask which one. Never act on a guess.

## Reschedule

1. Identify the exact booking (see above) and call `get_booking` for its event type and current time.
2. Confirm the attendee's time zone with the user. Do not assume it.
3. Call `get_availability` for the booking's event type, in that time zone. Propose at most 3 slots.
4. Confirm: "Move <attendee> from <old time + zone> to <new time + zone>? Reason: <reason or none>." Wait for an explicit yes.
5. Call `reschedule_booking` with `booking_id`, `new_start_time` (ISO 8601 with offset), optional `reason`.
6. Report the new time with time zone.

## Cancel

1. Identify the exact booking.
2. Cancelling notifies the attendees and cannot be undone. State that plainly.
3. Confirm: "Cancel <event type> with <attendee> on <time + zone>? Reason sent to them: <reason or none>." Wait for an explicit yes for that specific booking.
4. Call `cancel_booking` with `booking_id` and optional `reason`.
5. Report that it is cancelled. Offer to rebook with `schedule-meeting`.
6. Bulk cancel: confirm the full list once, then still cancel one at a time and report each result.

## Rules

- Never reschedule or cancel without an explicit yes in this conversation.
- A past approval does not cover a different booking or different details.
- Never invent booking ids; only use ids from tool results.
- Always state times with a time zone.

## When a tool fails

`isError: true` results carry a plain message:

- "Booking not found." The id is wrong or not yours. Re-run `list_bookings` and ask the user to choose.
- New time not free or invalid. Fetch fresh availability and propose again.
- Already cancelled. Say so; do nothing more.
- Other errors or expired sign-in. Show the message, do not loop, and suggest reconnecting the Retime connector.
