---
name: set-up-event-type
description: Create a new Retime event type (a bookable scheduling link) such as a coffee chat, intro call or 30-minute consult. Use when the user wants a new meeting type, booking link or scheduling page. Asks only for what is missing and confirms before creating.
---

# Set up a Retime event type

Tools: `list_event_types`, `create_event_type`.

## Steps

1. **Check for duplicates.** Call `list_event_types`. If a similar one exists (same title or purpose), tell the user and ask whether to reuse it or create another.
2. **Gather inputs, asking only what is missing.** Required: `title`, `duration_minutes`, `location_type`. Optional: `location_value`, `description`.
   Defaults to propose when the user is vague:
   - duration: 30 minutes
   - location: `google_meet` for remote meetings
   - description: omit (Retime fills in a default)
   `location_type` values: `google_meet`, `zoom`, `teams`, `address`, `call`, `online`, `other`.
   - `google_meet`, `zoom`, `teams` use the user's connected account; no `location_value`.
   - `address`, `call`, `online`, `other` need a `location_value` (street address, phone number, or link). Ask for it; never invent one.
3. **Confirm.** Show title, duration, location, description. Say it uses the user's default availability. Ask "Create it?" and wait for a yes.
4. **Create.** Call `create_event_type`.
5. **Report.** Give the title, duration, location, and the public booking URL if returned. Offer to schedule a meeting on it with `schedule-meeting`.

## Rules

- Never create without confirmation.
- Never invent a location value, phone number, or address.
- This tool cannot edit or delete event types or change availability. If asked, say so and point the user to the Retime dashboard.

## When a tool fails

`isError: true` results carry a plain message:

- Validation error (for example missing `location_value`, bad duration). Fix that one field with the user and retry once.
- Conferencing account not connected. Tell the user to connect it in Retime, or pick another location type.
- Other errors or expired sign-in. Show the message, do not loop, and suggest reconnecting the Retime connector.
