# ST01_03: Validate Docker Compose — Spark Cluster

## Parent Link
`base/BT01_infrastructure_setup.md`

## Actionable Steps
- [ ] Step 1: Verify Spark master starts and web UI is accessible at http://localhost:8080
- [ ] Step 2: Verify Spark worker registers with master node
- [ ] Step 3: Validate Spark worker has correct CPU/memory allocation (2 cores, 2GB)
- [ ] Step 4: Test spark-submit connectivity from master to worker

## Dependencies
- Docker networking (`echomind-net`) must be functional

## Definition of Done
Spark master/worker cluster is operational with the worker registered and visible in the master web UI.
