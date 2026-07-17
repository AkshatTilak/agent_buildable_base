# Subtask: Inference ASR Endpoint

**Parent Link:** `base/inference_models.md`

## Actionable Steps
- [ ] Implement `/infer/transcribe` endpoint on inference server.
- [ ] Default model: SenseVoice-Small (~250 MB VRAM). Framework: `funasr`.
- [ ] Input: Raw WAV/MP3 audio file bytes.
- [ ] Output: `{"text": "...", "segments": [...], "emotion": "...", "audio_events": [...], "language": "en"}`.
- [ ] `emotion` and `audio_events` populated by SenseVoice; `null` for other ASR models.
- [ ] Register with VRAM Manager lazy loading system.
- [ ] Add `funasr` to `pyproject.toml` inference extras.

## Dependencies
- `funasr` package, VRAM Manager, Model Registry.

## Definition of Done
- Endpoint transcribes audio files with segment timestamps.
- Emotion/event detection works with SenseVoice-Small.
- Model loads on demand.
