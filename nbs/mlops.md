GOALS
- Execute many projects simultaneously(Volume)
- Fast iteration (Velocity)
- Results are robust (Validity)
- Support diff projects (Variety)

Layers of the data-science infrastructure to support the full lifecycle.

1. Data Warehouse: (common source of truth for data)
2. Compute Resources: (Scale smoothly to run different workloads)
3. Job Scheduler : retrain models regularly
4. Versioning: a. compare and contrast btw differnt versions b. run multiple versions simultaneously(A/B testing)
5. Architecture
6. Model Operations: Have systems in place for  Detection, troubleshooting and fixing errors
7. Feature engineering
8. Model Development

Workflows
- `What` to execute ? [Architecture (provides abstractions to the user)]
- `Where` (computer requirements + computer(gcp,aws etc) [Compute layer])
- `How` (how to orchestrate the steps)[Scheduler: (run in topological order)]

## Job Scheduler
Rubric for choosing schedulers
1. Architecture: Api/abstractions
2. Job Scheduler : how workflows are triggered, executed and monitored, failure handling.
3. Compute resources : how many steps can the system execute in parallel ? Specify diff resource requirements.

Things to consider:
Is the scheduler itself a single point of failure aka highly available(HA)?

```
Top tools:

Apache Airflow (not HA, supports many backends)
Kubeflow pipelines (under the hood scheduled by argo) (HA,runs on kubernetes cluster)
Aws Step functions (Arch: JSON based config called amazon states language, scheduler: HA,compute: intergration with aws services)
Metaflow
```


Static DAG's vs Dynamic DAG's

