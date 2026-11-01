# Proposed streaming conversation path

Status on 2026-11-01: **design and source investigation only**. This hosted path has not been implemented, activated, or benchmarked. The user has approved considering cloud APIs to prioritize conversational latency, while retaining the Jarvis and Deckard voices. A specific voice-upload destination and usage cost still need to be reviewed before transmission or billed activation.

## Why the current local pipeline is slow

The existing application streams GPT text to the UI, but waits for the validated final reply before starting local speech synthesis. Its installed Qwen SDK returns a complete waveform. Phrase segmentation, a resident model, reference conditioning, exact-text caching, and next-phrase prefetch already exist; adding those again does not solve the first-audio delay.

The current engines also differ by character: Deckard uses the Qwen3-TTS 0.6B Base PyTorch path, while Korean Jarvis uses the native GGUF path, now with optional CUDA execution and a CPU fallback. The GPU change accelerates a synthesis component; it does not make the existing sequential pipeline streamed or full duplex. Historical observations, the separate short local CUDA test, and their limits are summarized in [CLAUDE-HANDOFF.md](CLAUDE-HANDOFF.md).

## Proposed architecture

1. Use an OpenAI Realtime session for streamed microphone input, turn detection, conversational understanding, and **text-only output**. The official API supports text output without choosing an OpenAI output voice. This avoids making OpenAI custom-voice registration a prerequisite for this particular architecture.
2. Send stable answer text segments to a separate hosted streaming TTS connection. The identified candidate is Qwen-Audio-TTS, using a registered reference voice rather than an unrelated built-in voice.
3. Receive PCM chunks, queue a bounded amount of audio, and start playback before the entire sentence or reply has been synthesized. Drive the selected avatar from the audio being played, not from input microphone volume.
4. Keep long-running desktop actions on the existing GPT/Claude task backend. A conversational turn should not wait for all delegated work to complete before the interface can acknowledge progress.
5. On a user interruption, cancel the active response and TTS generation, clear queued output audio, stop avatar speech motion, and associate subsequent events with a new turn ID. Late chunks from older turns must never play.

The hybrid path also needs a heard-text ledger: track which text has actually reached audio playback, rather than assuming that all generated text was spoken. Evaluate out-of-band text generation with `conversation: "none"` and commit only the appropriate delivered content to conversational context, or use another explicitly designed context-management boundary. Do not apply an audio-item truncation using `audio_end_ms` to a text-only item. This is a design requirement to validate against the selected Realtime API version, not an implemented behavior in this snapshot.

This requires a new streaming boundary between conversation and audio output. It is not accomplished by setting the installed local SDK's `non_streaming_mode` flag to false, and the already integrated OpenAI Live Console transport does not automatically provide this hybrid path.

## Voice preservation

The candidate TTS service documents reference-audio enrollment, Korean language hints, segmented text input, and streamed audio output. The same local reference recording can be evaluated in that service, but a different model may produce a different timbre, accent, cadence, or pronunciation. Voice similarity is therefore a user-reviewed acceptance test, not an assumed property.

Do not upload `.references/` automatically. Identify the exact selected sample and destination, review the service's applicable requirements, and obtain the needed authorization before sending it. The local voice path remains available until the new voice is accepted.

OpenAI's separate recording-based custom-voice path has additional account eligibility and a matching speaker-consent recording requirement. Existing local audio files are not automatically OpenAI voice IDs. A prompt-generated voice also does not establish preservation of the user's existing character voice.

## Credentials, cost, and deployment

- An OpenAI API project/key is required for the proposed Realtime session. A ChatGPT subscription login is not a substitute for that API credential.
- The separate TTS path requires its service account, region/workspace, API credential, and registered voice identifier.
- Keep long-lived keys on the local application server; expose only the documented short-lived client credential when required by the browser transport. Never put API keys into Git, browser source, logs, screenshots, or this handoff.
- Review the exact model and region's current prices, voice-enrollment charge if applicable, usage limits, and a small explicit trial budget before activation. No billed trial or voice enrollment has been performed here, and this document makes no unverified cost estimate.
- Preserve the local loopback application. Publishing this source repository does not authorize public exposure of desktop-control endpoints.

## Smallest useful validation sequence

1. Prepare an isolated adapter and mocked lifecycle tests for cancellation, stale turn IDs, queue bounds, and credential ownership.
2. Configure credentials locally after the user completes any necessary account and billing steps.
3. With an approved short sample and trial budget, enroll one voice and audition fixed Korean sentences. Confirm voice identity before replacing either character's voice.
4. Measure separately: end of user speech, first transcription/text event, first received PCM chunk, and first audible sample. Record median and slower-turn results on the user's actual network; avoid quoting vendor best-case latency as a local measurement.
5. Test interruption, reconnect, delayed chunks, empty replies, tool delegation, and service failure. Only enable the hosted mode after these pass, with a clear local fallback.

## Official references

- [OpenAI Realtime conversations: text output, audio input, and interruption](https://developers.openai.com/api/docs/guides/realtime-conversations)
- [OpenAI custom voices: eligibility and recording requirements](https://developers.openai.com/api/docs/guides/custom-voices)
- [Qwen streaming text-to-speech](https://www.alibabacloud.com/help/en/model-studio/realtime-tts-user-guide)
- [Qwen reference-voice enrollment and Korean language hints](https://www.alibabacloud.com/help/en/model-studio/voice-clone-design-http-api)
- [Current Alibaba Cloud model pricing](https://www.alibabacloud.com/help/en/model-studio/model-pricing)
- [Qwen3-TTS source and available model families](https://github.com/QwenLM/Qwen3-TTS)

These documents establish supported interfaces, not the user's account access, permission to upload any particular recording, voice fidelity, or demonstrated end-to-end latency.
