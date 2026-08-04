# Base Task: Infrastructure & Testing

## What
Update Docker configurations to map volumes to local host paths, and write comprehensive API tests for the gateway to ensure the system is stable and verified.

## Why
Local testing and Docker testing were diverging in persistent storage behavior. Comprehensive API tests are necessary to prove that the v7 changes actually resolve the v6 bugs and don't silently fail.

## Associated Subtasks
- `[x]` `tasks/v7/sub/sub_04_01_docker_volumes.md`
- `[x]` `tasks/v7/sub/sub_04_02_gateway_tests.md`
- `[x]` `tasks/v7/sub/sub_04_03_references.md`
