# Later slice: filter the catalog by mood

**Status:** Proposed for Level 5 Copilot cloud agent delegation. Not implemented, and not validated with listeners.

## Overview

This record describes a small follow-up to the shared Music Catalog playlist slice. A workshop participant chose and confirmed it after a short Design Thinking (DT) exploration. It is a separate later change. It does not alter the shared Level 3 scope in [playlist-design-decisions.md](playlist-design-decisions.md).

## Problem

Music often doesn't match the energy a listener wants at a particular time of day. The exploration framed it as: *"How might we help listeners hear music that matches the energy they want at each time of day, without making them set things up every time?"*

The catalog has no data to support this today. Each of the 12 tracks has only `id`, `title`, `artist`, `album` and `durationSeconds`.

## User value

A listener can quickly find tracks with the energy they want right now, without scanning the whole catalog. The slice also adds the per-track mood data that later time-of-day features would need.

## Chosen scope

- Give each track one mood from a fixed set: **Calm**, **Focused** or **Upbeat**.
- Return the mood with each track, and let the API filter tracks by mood.
- Add a mood choice in the front end that filters the visible catalog.

## Acceptance criteria

| ID | Observable behaviour |
| --- | --- |
| AC-1 | `GET /api/tracks` returns each track with a `mood` field set to `calm`, `focused` or `upbeat`. All 12 tracks are still returned, and existing track fields are unchanged. |
| AC-2 | `GET /api/tracks?mood=calm` returns HTTP 200 with only Calm tracks. The same applies to `focused` and `upbeat`. |
| AC-3 | `GET /api/tracks` with an unsupported `mood` value returns HTTP 400 with a JSON error body. |
| AC-4 | The UI has a labelled mood control with All, Calm, Focused and Upbeat. Choosing a mood shows only matching tracks; choosing All shows all 12. |
| AC-5 | When the filter changes, a polite `aria-live` status announces the result, for example "4 tracks match Calm". |
| AC-6 | Adding a filtered track to the playlist still works. The playlist endpoints, the 404 and 409 responses and the empty-state text are unchanged. |
| AC-7 | xUnit tests in `tests/api` using `WebApplicationFactory<Program>` cover AC-1 to AC-3. Vitest and Testing Library tests in `src/front` cover AC-4 to AC-6, finding controls by role and accessible name. |

## Exclusions

- Choosing a mood automatically from the time of day, or from weekday versus weekend
- Custom moods, editing moods, or a listener's mood schedule
- Saving the selected filter or any preference, and persistence of any kind
- Users, authentication and external music or mood services
- Recommendations, ranking or search
- Changes to `src/api/Data/tracks.json`
- Any change to the shared playlist contract or its duplicate-feedback UX

## Assumptions

None of these has been tested with listeners:

- Listeners recognise Calm, Focused and Upbeat as useful moods.
- One mood per track is enough.
- The mood given to each track is a placeholder editorial choice. It is not based on listener input or audio analysis.
- Filtering by mood helps listeners even before any time-of-day automation.

## Dependencies

- The shared Level 3 playlist slice is merged, including `GET /api/tracks` and the catalog view with Add controls.
- Repository conventions apply. The API uses minimal API endpoints under `/api` with camelCase JSON. State is in memory only. Front-end code uses function components and hooks, with no added libraries.
- Track moods are kept as a read-only, in-memory map in the API, and the seed data stays unchanged.

## Rationale

| Option considered | Decision |
| --- | --- |
| **Filter the catalog by mood** | **Chosen.** This is the smallest step toward the framing question and fits one delegated issue. It also adds the mood data the other options depend on. |
| Pre-select a mood from the time of day | Deferred. It needs per-track moods first and involves more untested assumptions about daily listening patterns. |
| Listener-defined moods and schedule | Deferred. It is too large for one issue, needs configuration UI, and works against the "set up once at most" goal unless designed carefully. |

## Notes and limitations

- This is a proposal, not a requirement or a commitment. Refine unclear acceptance criteria before creating an issue.
- The DT exploration was a short sampler. No method was completed, and no listeners were interviewed or tested.

---

*This document was drafted with AI assistance and requires human review before it is used to create an issue.*
