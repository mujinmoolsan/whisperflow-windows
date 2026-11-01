# Shared-source validation

Automated checks were run in the isolated shared-source checkout on 2026-11-01. They use mocked native processes and media/network boundaries: no live API calls, microphone captures, voice uploads, or new model loads were part of the automated checks. A separately authorized local synthesis check is described below.

| Check | Result |
| --- | --- |
| Selected Python companion, speech pipeline, login, latency, and Live lifecycle tests | 66 passed |
| Native GPU selection/fallback, cancellation, process-start error logs, idle-worker ownership, and approved voice integration | 19 passed |
| Official OpenAI browser transport with mocked media/network, including pinned upstream source hashes | 10 passed |
| Deckard geometry, speech timing, and neutral restoration | 18 passed |
| Jarvis electron-cloud rendering, selection/visibility lifecycle, reduced-motion preference and explicit user override | 24 passed |
| Character-theme initial selection, switching, and preservation of existing state | Passed |
| Decorative laboratory: six screens and arm, duplicate/zero-size guards, frame cap, persona/visibility/motion lifecycle, teardown, and absence of input/API/media side effects | Passed |
| Procedural armor: frame cap, persona/visibility/motion lifecycle, WebGL context restoration, BFCache, and GPU-resource disposal | 12 passed |

The combined visual revision keeps six decorative technical screens, an operating left robot arm, and three procedural Three.js armor props. Its laboratory and armor tests passed from repository-relative paths. The latest native GPU changes received their own regression checks; the earlier companion, transport, avatar, and theme checks remain recorded for the earlier integration stage.

Separately, the deployed optional CUDA runtime was checksum-verified and enumerated the local NVIDIA GPU. A silent local short-phrase generation selected the CUDA executable with `-ngl 99`, took 5.867 seconds, and produced 1.332 seconds of 24 kHz audio. One CUDA attempt and no CPU fallback were recorded. The waveform was not played or uploaded and is excluded from this archive, as are runtime files and private logs. This result is not an end-to-end conversation latency or quality test. The later 300-second timeout adjustment was covered by mock assertions, without another model run. Upstream CPU/GPU comparison figures in CLAUDE-GPU.md are the upstream author's measurements, not this integration's reproduction.

The shared copy generalizes a personal form of address in code and matching tests. Both character themes and the final laboratory overlays were reviewed in the browser with no console errors observed. The reviewer also confirmed that turning motion off pauses the two new layers and that Deckard hides them. No reference photograph or private-history screenshot is included in this source archive.

These results verify mocked behavior and source integrity. They do not establish hosted API access, voice similarity on a new service, or realtime end-to-end performance.
