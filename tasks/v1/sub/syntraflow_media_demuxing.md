# Subtask: SyntraFlow Media Demuxing

**Parent Link:** `base/syntraflow_rag.md`

## Actionable Steps
- [x] Extract audio channel from uploaded video containers (`.mp4`, `.mov`, `.webm`, `.mkv`) using async media pipeline.
- [x] Generate temporary WAV audio files (`.wav`) for transcription.
- [x] Use `ffmpeg-python` or `pydub` (NOT `aiortc`). Requires `ffmpeg` binary.

## Definition of Done
- Uploaded videos have their audio successfully demuxed to WAV files.
