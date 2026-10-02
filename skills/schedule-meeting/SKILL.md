---
name: schedule-meeting
description: Book a meeting on the user's Retime calendar. Use when the user wants to schedule, set up, book or arrange a call or meeting with someone, asks for open times on their Retime links, or says "find a time with ...". Finds free slots, confirms the attendee's time zone, proposes up to three options, and books only after explicit confirmation.
---

# Schedule a meeting with Retime

Tools: `list_event_types`, `get_availability`, `create_booking` (Retime connector).

## Steps

1. **Pick the event type.** Call `list_event_types`. If one type clearly fits (title, duration), use it. If several fit, ask the user which one. If none exist, offer the `set-up-event-type` skill.
2. **Collect the minimum inputs.** You need:
   - attendee full name
   - attendee email
   - attendee IANA time zone (for example `Europe/Berlin`)
   - target date or range (default: next 7 days; max 31)
   Ask in one short message for whatever is missing. Never guess or invent an email address or a time zone. A city or country is not enough to confirm a time zone; restate your interpretation ("Berlin, so Europe/Berlin?") and wait for a yes.
3. **Find slots.** Call `get_availability` with `event_type_id`, `start_date`, `end_date`, and the attendee's `time_zone`.
4. **Propose at most 3 slots.** Spread them across different days or day parts. Show each with weekday, date, time, and time zone abbreviation, for example "Tue 6 Oct, 10:00 CEST (Europe/Berlin)". If the user's own zone differs, show both.
5. **Confirm before booking.** Summarize: event type, attendee name and email, chosen slot with time zone, any note. Ask "Book it?". Do not call `create_booking` until the user says yes in this conversation.
6. **Book.** Call `create_booking` with `event_type_id`, `start_time` (ISO 8601 with offset, copied from the chosen slot), `attendee_name`, `attendee_email`, `time_zone`, and `notes` if given. Retried calls are safe: the server will not double-book the same request.
7. **Report.** State the confirmed time with time zone, attendee, and the meeting link or location if returned. Say the attendee receives the confirmation from Retime.

## Rules

- Never book without explicit confirmation of the exact slot.
- Never invent attendee details.
- Always state times with a time zone.
- Use only slots returned by `get_availability`. Do not compute your own.
- Book one meeting per confirmation.

## When a tool fails

A result with `isError: true` carries a plain message. Read it and act:

- "Event type not found." Re-run `list_event_types` and choose again.
- Validation message (bad email, time zone, date). Fix the one field with the user and retry once.
- Slot no longer free. Call `get_availability` again and propose fresh slots.
- No slots in range. Say so, offer to widen the date range (max 31 days), or suggest another event type.
- Any other error, or an authentication failure. Show the message, do not retry in a loop, and tell the user to reconnect the Retime connector if sign-in expired.
