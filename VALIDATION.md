# Shared-source validation

Checked in the isolated shared-source checkout on 2026-11-01. No live API calls, microphone captures, voice uploads, new model loads, or latency benchmarks were part of these checks.

| Check | Result |
| --- | --- |
| Selected Python companion, speech pipeline, login, latency, and Live lifecycle tests | 66 passed |
| Official OpenAI browser transport with mocked media/network, including pinned upstream source hashes | 10 passed |
| Deckard geometry, speech timing, and neutral restoration | 18 passed |
| Jarvis electron-cloud rendering, selection/visibility lifecycle, reduced-motion preference and explicit user override | 24 passed |
| Character-theme initial selection, switching, and preservation of existing state | Passed |
| Decorative laboratory: six screens and arm, duplicate/zero-size guards, frame cap, persona/visibility/motion lifecycle, teardown, and absence of input/API/media side effects | Passed |
| Procedural armor: frame cap, persona/visibility/motion lifecycle, WebGL context restoration, BFCache, and GPU-resource disposal | 12 passed |

The latest visual revision adds six decorative technical screens, an operating left robot arm, and three procedural Three.js armor props to the Jarvis laboratory. The new laboratory and armor checks were run from repository-relative test paths after the deployed files were copied. Earlier backend, transport, avatar, and theme results apply to their unchanged code in this snapshot.

The shared copy generalizes a personal form of address in code and matching tests. Both character themes and the final laboratory overlays were reviewed in the browser with no console errors observed. The reviewer also confirmed that turning motion off pauses the two new layers and that Deckard hides them. No reference photograph or private-history screenshot is included in this source archive.

These results verify mocked behavior and source integrity. They do not establish hosted API access, voice similarity on a new service, or realtime end-to-end performance.
