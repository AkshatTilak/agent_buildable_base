# Subtask 07_02: Harrier 0.6B Local Embedding Integration

## Objective
Integrate the `harrier-0.6b` local embedding model into Syntraflow embedder registry with local execution fallback rules.

## Tasks
1. `[x]` Register `harrier-0.6b` model definition in `projects/syntraflow/embedders/registry.py` with dimension mapping (768d).
2. `[x]` Implement SentenceTransformers wrapper for Harrier in `projects/syntraflow/embedders/harrier.py`.
3. `[x]` Configure local CPU/GPU fallback resolution when running in offline or RAM-constrained environments.
4. `[x]` Add test case in `tests/test_embedders.py` verifying Harrier embedding vector output dimensions.
