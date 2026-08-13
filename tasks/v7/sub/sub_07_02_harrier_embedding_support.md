# Subtask 07_02: Harrier 0.6B Local Embedding Integration

## Objective
Integrate the `harrier-0.6b` local embedding model into Syntraflow embedder registry with local execution fallback rules.

## Tasks
1. `[x]` Register compatibility alias `harrier-0.6b` for `microsoft/harrier-oss-v1-0.6b` in `projects/syntraflow/embedders/registry.py` with dimension mapping (1,024d).
2. `[x]` Implement SentenceTransformers wrapper for the canonical Microsoft Harrier checkpoint in `projects/syntraflow/embedders/harrier.py`.
3. `[x]` Configure local CPU/GPU fallback resolution when running in offline or RAM-constrained environments.
4. `[x]` Add test case in `tests/test_embedders.py` verifying Harrier embedding vector output dimensions.
