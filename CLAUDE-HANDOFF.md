# Voice Studio handoff for Claude

Prepared 2026-11-01 (Asia/Seoul). This document describes the current implementation and the remaining work. It contains no credentials, private voice samples, or conversation transcripts.

## Project and requested result

Voice Studio is a local desktop voice companion with Jarvis and Deckard characters. The user wants natural, interruptible, low-latency Korean conversation while keeping the existing character voices. GPT and Claude can perform delegated desktop tasks. The user has approved considering cloud APIs to prioritize realtime performance, but actual voice-sample transmission and paid activation still need a concrete destination and cost review.

Repository: <https://github.com/mujinmoolsan/whisperflow-windows>. Sharing branch: `voice-studio-2026-11-01`. The complete source snapshot is packaged as `Voice-Studio-2026-11-01-source.zip`; extract it before inspecting or editing the application. A local preparation alone does not confirm remote publication.

## Implemented

- A character-themed workspace with selection, task history, microphone controls, and account-connection status.
- A regenerated Deckard upper-body portrait with mouth and jaw movement driven by the audio that is actually playing. It is a fixed-view **2.5D articulated portrait**, not a freely rotatable volumetric model. The image is mapped to a shallow Three.js mesh. Local speech envelopes and live output levels both drive the mouth. Eighteen offline geometry/lifecycle checks passed; neutral and speaking browser renders were reviewed.
- A Jarvis blue electron-cloud renderer with volume, continuously flowing light, and visible motion. It replaces the earlier point-and-line graph appearance. Its app-specific motion toggle can explicitly opt into motion while preserving the operating system's reduced-motion preference. Twenty-four offline rendering and lifecycle checks passed.
- Character-specific workspace skins: a cyan laboratory for Jarvis, with six technical screens, an animated robot arm on the left, and three armor displays; an empty headquarters interior for Deckard, with architectural ivory, navy, and gold panels across the whole interface and no background teammates. The character selector also updates the brand, captions, footer, browser title, and theme color.
- A decorative laboratory layer with drawings, waveforms, circuits, and exploded views. Every screen is marked DESIGN VISUAL: these graphics are not PC, microphone, model, or network telemetry. The layer does not request APIs or change voice/input behavior. Lifecycle checks cover frame limits, persona switching, motion preferences, visibility, zero size, duplicate inclusion, BFCache, teardown, and pointer transparency.
- Three procedural Three.js armor props: silver, red/gold with a circular reactor, and red/gold with a triangular reactor. They use a fixed camera, small head/wrist movements, and reactor lighting. They are authored geometric approximations, not trained models or official film assets; reference photographs are excluded from the public source. Twelve mocked lifecycle checks passed, including WebGL context loss and GPU-resource disposal.
- Browser review confirmed the six screens, left arm, and three armor displays with no console errors observed. The motion toggle pauses both new layers, and selecting Deckard hides them. Both character skins and the existing controller's state-preservation behavior were also reviewed.
- Existing local speech support, phrase segmentation, exact-text audio caching, and next-segment prefetch. The selected local voices remain available.
- An adapted official OpenAI Live Console browser transport, pinned to commit `5f64ecb3009d8274da9953c825d469ea6b6b67c3`. Source attribution and the MIT license are retained under `third_party/openai-live-console/`. Session start, cancellation, stale-event ownership, and close handling were tested with mocks.

## What is not working yet

The armor's raster image-generation attempt was blocked; the implemented alternative is procedural 3D geometry. Its fixed-view decorative props do not reproduce the detail of a production film asset or offer a user-operated model viewer.

**Applying the official transport code did not activate realtime conversation.** No OpenAI API project/key or registered Jarvis/Deckard OpenAI voice IDs are configured. The Live option remains disabled. ChatGPT or Claude subscription login is not an API credential. No end-to-end latency claim is supported by the mock tests.

The portable Claude Code CLI is installed but its supported `auth status` command currently reports `loggedIn: false` and `authMethod: none`. The desktop Claude app has a separate sign-in surface; its sign-in state has not been verified for this handoff. Do not describe the handoff as delivered to Claude until an actual send is confirmed.

## Verified latency findings

- GPT text deltas can reach the UI, but speech synthesis starts only after the final reply has been received and validated. The first reply and first synthesis therefore remain largely serial.
- The installed Qwen SDK returns a complete generated waveform before playback. Its `non_streaming_mode=False` option does not by itself enable true streaming generation.
- Deckard currently uses Qwen3-TTS 0.6B Base with PyTorch FP32 on CUDA. Jarvis Korean uses the native GGUF engine with CPU inference. Do not assume both characters use the same GPU path.
- Existing timing observations included approximately 6.28 seconds to first text, 8.02 seconds to final reply, and 33.7 seconds to first audio. A cached-audio case still took about 7.05 seconds to first audio. These are historical examples rather than new benchmarks, and exclude input voice-activity detection and transcription time.
- The browser currently ignores microphone samples during both voice preparation and playback. A simple `voiceBusy` to `playing` substitution is unsafe: preparation may finish during a new utterance, and reconnect snapshots do not independently report playback state. Correct interruption needs explicit voice-only cancellation, a prepare-to-play handshake, and a trustworthy playback snapshot.

## Proposed cloud direction

The working proposal is a streaming conversation frontend using OpenAI for realtime understanding and text output, paired with a separate streaming TTS service that can use the existing reference recordings. Desktop actions remain delegated to the existing GPT/Claude task backend. This separates conversational latency from long-running tool work.

See [CLOUD-REALTIME-PLAN.md](CLOUD-REALTIME-PLAN.md) for the proposed interface boundaries and a bounded validation sequence. This document is a plan, not an already functioning hosted integration.

An identified TTS candidate is Alibaba Cloud Qwen-Audio-TTS streaming, which documents Korean reference-voice enrollment, segmented text input, and audio-chunk output. The current local reference recordings must not be uploaded automatically. A different engine may change the voice; audition and user acceptance are required before replacing the current voice path. The hosted integration, account setup, and live performance test are **not yet implemented or completed** in this handoff.

Official references:

- OpenAI public source: <https://github.com/openai/openai-live-console>
- OpenAI realtime conversations and text output: <https://developers.openai.com/api/docs/guides/realtime-conversations>
- OpenAI custom-voice requirements: <https://developers.openai.com/api/docs/guides/custom-voices>
- Qwen streaming TTS: <https://www.alibabacloud.com/help/en/model-studio/realtime-tts-user-guide>
- Qwen reference-voice enrollment: <https://www.alibabacloud.com/help/en/model-studio/voice-clone-design-http-api>

## Relevant code

- `whisperflow/companion.py`: local app routes, state, task orchestration, and speech scheduling.
- `whisperflow/agent_runner.py`: supported GPT/Claude CLI connection and task execution.
- `whisperflow/live_session.py`: existing official Live integration boundary and lifecycle.
- `whisperflow/windows_qwen.py`, `whisperflow/approved_voice.py`: local synthesis and playback path.
- `whisperflow/static/live-client.js`: browser Live wrapper and output-energy events.
- `whisperflow/static/companion.js`: UI, microphone flow, and task/speech events.
- `whisperflow/static/jarvis-neural.js`: Jarvis animation.
- `whisperflow/static/persona-theme.css`, `persona-theme.js`: workspace skins, character labels, and theme switching.
- `whisperflow/static/lab-activity.css`, `lab-activity.js`: decorative technical screens and robot-arm motion.
- `whisperflow/static/armor-display.css`, `armor-display.js`: procedural armor geometry and animation lifecycle.
- `whisperflow/static/deckard-avatar.js`, `deckard-avatar.css`, `deckard-resynthesized.png`: Deckard portrait and mouth animation.

## Continuation instructions

1. Extract the shared source archive, read this handoff, and confirm the deployment state before making changes. Preserve the selected character voices and the current visual assets.
2. Treat the present Live option as inactive until real credentials, voice configuration, and an end-to-end check exist. Do not claim that official source code alone provides the hosted models or Dot-equivalent latency.
3. Design the realtime conversation and streamed audio path with cancellation, bounded playback queues, speech interruption, and output-driven avatar motion.
4. Keep API keys server-side. Do not add credentials, account configuration, local machine paths, reference recordings, caches, private histories, or generated session logs to GitHub.
5. Do not install a large model, start a heavy benchmark, upload a voice sample, enable a billed API, or expose the local server publicly merely to complete the handoff. Make the next action and its actual costs/data destination concrete first.

## Suggested message to Claude

Please review this Voice Studio repository and its handoff document. Preserve the Jarvis and Deckard voices and the current appearance. The official OpenAI Live transport is integrated but inactive; local synthesis remains the available voice path. Focus on a practical streamed conversation architecture and explain exactly what credentials, voice enrollment, costs, and validation are still required. First acknowledge the current state and propose the smallest verifiable implementation step; do not silently switch character voices or claim realtime performance before testing.
