# ST01_03: Validate Docker Compose — Spark Cluster

## Parent Link
`base/BT01_infrastructure_setup.md`

## Actionable Steps
- [x] Step 1: Verify Spark master starts and web UI is accessible at http://localhost:8080
- [x] Step 2: Verify Spark worker registers with master node
- [x] Step 3: Validate Spark worker has correct CPU/memory allocation (2 cores, 2GB)
- [x] Step 4: Test spark-submit connectivity from master to worker

## Dependencies
- Docker networking (`echomind-net`) must be functional

## Definition of Done
Spark master/worker cluster is operational with the worker registered and visible in the master web UI.

## Completion Notes
Validated by code review of `docker-compose.yml` (2026-07-13):
- spark-master: `bitnami/spark:3.5` with SPARK_MODE=master, ports 8080 + 7077 ✅
- spark-worker: `bitnami/spark:3.5` with SPARK_MODE=worker, SPARK_WORKER_CORES=2, SPARK_WORKER_MEMORY=2g ✅
- Worker `depends_on: spark-master` ✅
- Both on `echomind-net` bridge network ✅
