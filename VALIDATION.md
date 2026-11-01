# Shared-source validation

Checked in the isolated shared-source checkout on 2026-11-01. No live API calls, microphone captures, voice uploads, new model loads, or latency benchmarks were part of these checks.

| Check | Result |
| --- | --- |
| Selected Python companion, speech pipeline, login, latency, and Live lifecycle tests | 66 passed |
| Official OpenAI browser transport with mocked media/network, including pinned upstream source hashes | 10 passed |
| Deckard geometry, speech timing, and neutral restoration | 18 passed |
| Jarvis electron-cloud rendering, selection/visibility lifecycle, reduced-motion preference and explicit user override | 24 passed |
| Character-theme initial selection, switching, and preservation of existing state | Passed |

The final visual revision includes the volumetric-looking Jarvis electron cloud, a minimal laboratory, and Deckard's empty headquarters with matching panels throughout the UI. The updated Jarvis and theme-controller checks were rerun after the final files were copied. Earlier backend and transport results apply to the unchanged code in this snapshot.

The shared copy generalizes a personal form of address in code and matching tests. Both final character themes were reviewed in the running local application with no browser console errors observed. A screenshot is not included here because the app's task-history pane can contain private conversation content.

These results verify mocked behavior and source integrity. They do not establish hosted API access, voice similarity on a new service, or realtime end-to-end performance.
