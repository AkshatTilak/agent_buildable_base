# Subtask: ASR Model Contracts & Adapters

**Parent Link:** `base/model_hub.md`

## Actionable Steps
- [ ] Implement unified ASR adapter interface normalizing output across all ASR models.
- [ ] Support local: SenseVoice-Small, Whisper V3 Turbo, Whisper Large V3, Parakeet TDT, Granite Speech.
- [ ] Support cloud: Gemini 3.5 Flash (audio), Deepgram Nova-3.
- [ ] Standardized JSON schema:
  ```json
  {"text": "...", "segments": [...], "emotion": "...", "audio_events": [...], "language": "en"}
  ```
- [ ] `emotion` and `audio_events` return `null` for non-SenseVoice models.

## Dependencies
- Framework-specific packages: `funasr`, `faster-whisper`, `nemo_toolkit`, `transformers`.

## Definition of Done
- Each ASR model selectable via `ASR_MODEL` env var with standardized output.
- Emotion/event fields correctly populated (SenseVoice) or null (others).
