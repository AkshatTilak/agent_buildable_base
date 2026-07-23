# Out-of-Scope Issue: Legacy GuardRoute Unit Test Dependencies

> **Date:** 2026-07-24  
> **Source:** Unit Test Execution (`projects/guardroute/tests/unit/test_agents.py`, `test_security.py`)

## Description

While running guardroute unit test suite, 7 legacy tests failed due to environment dependency and mock configuration details:
1. `test_coding_sandbox_*` (4 failures): `PrintCollector` returns `TypeError: 'NoneType' object is not callable` in `coding.py` when `RestrictedPython` print collector is invoked in test environment without full RestrictedPython setup.
2. `test_mcp_query_graph_parameters` (1 failure): `ModuleNotFoundError: No module named 'mcp'` when importing syntraflow mcp server in guardroute security test.
3. `test_toxicity_filter_*` & `test_hallucination_grounding_*` (2 failures): LangGraph module check returns None when `langgraph` package isn't present in mock runtime environment during orchestrator stream tests.

## Action Taken

Logged to `tasks/v5/temp/` as required by Section 3 of `work_principle.md`. Focus remains strictly on V5 base tasks.
