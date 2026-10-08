# Music Catalog playlist slice: design decisions

## Overview

This record is the reviewed delivery brief for the shared Music Catalog playlist slice. Level 3 implements it, and the optional PRD work refines it.

The playlist scope was **supplied by the workshop**. It did not come from Design Thinking (DT) exploration. A short DT exploration ran beforehand. It is summarized under [Exploration context](#exploration-context) as background only. It is not validated research and does not change this scope.

Source of the delivery contract: [Debrief and hand off to the shared implementation slice](../afternoon-2/workshop.md#debrief-and-hand-off-to-the-shared-implementation-slice).

## Scope

A listener can browse the track catalog and add tracks to **one in-memory playlist**.

### API

| Endpoint | Purpose |
| --- | --- |
| `GET /api/tracks` | Return the catalog tracks. |
| `GET /api/playlist` | Return the tracks in the playlist. |
| `POST /api/playlist/tracks` | Add a track. The JSON request body contains `trackId`. |

### Front end

- The catalog and the playlist are both visible.
- While the playlist is empty, the page shows: "Your playlist is empty. Add a track to get started."

## Acceptance criteria

| ID | Observable behaviour |
| --- | --- |
| AC-1 | `GET /api/tracks` returns the catalog tracks. |
| AC-2 | `GET /api/playlist` returns the current playlist. On a freshly started API, the playlist is empty. |
| AC-3 | `POST /api/playlist/tracks` with a known `trackId` that is not yet in the playlist adds the track. It then appears in `GET /api/playlist`. |
| AC-4 | `POST /api/playlist/tracks` with an unknown `trackId` returns HTTP 404 and does not change the playlist. |
| AC-5 | `POST /api/playlist/tracks` with a `trackId` already in the playlist returns HTTP 409 and does not change the playlist. |
| AC-6 | The catalog and playlist are visible in the UI. The empty-state text appears while the playlist is empty. |
| AC-7 | Each control has an accessible, descriptive label. |
| AC-8 | Status feedback, including duplicate-add feedback, is visible and perceivable by assistive technology. |

## Open decision

**Duplicate-add feedback UX.** The feedback must be visible and accessible (AC-8). The interface approach is not decided here. It is chosen at the Level 3 RPI plan gate. Examples include an inline status message or disabling the Add control for tracks already in the playlist.

## Out of scope

- Users and authentication
- Persistence of any kind (database, file writes, external services)
- Reordering or removing playlist tracks
- Search
- Creating more than one playlist

## Constraints and conventions

These come from the [repository instructions](../../.github/copilot-instructions.md):

- Keep the API as minimal API endpoints under `/api`, returning camelCase JSON.
- Keep state in memory only. The playlist resets when the API restarts.
- Keep the existing `src/api` (ASP.NET Core) and `src/front` (React + Vite) setup. Use function components and hooks, with no added state-management or UI libraries.
- Cover behaviour changes with tests: xUnit in `tests/api`, Vitest and Testing Library in `src/front`.

## Exploration context

A time-boxed DT sampler (about 10–15 minutes) explored the question *"How might we help someone choose music for a listening moment?"* Methods 1–3 were sampled briefly. No method was completed, and Methods 4–9 were not reached.

| Category | Content |
| --- | --- |
| Evidence | The catalog (`src/api/Data/tracks.json`) has 12 tracks with `id`, `title`, `artist`, `album` and `durationSeconds`, and no mood or energy data. No listener interviews, observations or tests were run. |
| Assumptions (untested) | Listeners want music whose energy matches the time of day. Weekdays and weekends may need different moods. Listeners may want predefined moods (for example Calm, Focused, Upbeat) as well as their own. Any setup should happen once at most. |
| Planned work | Interview listeners, for example: *"Walk me through the last time music felt wrong for the moment. What was going on?"* Decide how tracks would be linked to moods. |

Mood and time-of-day ideas are **not part of this slice**. A separate later-slice proposal may record them.

## Notes and limitations

- Nothing in this record is validated with real users.
- If this record and another planning source disagree, resolve the difference before approving an implementation plan.

---

*This document was drafted with AI assistance and requires human review before it is used as a delivery brief.*
