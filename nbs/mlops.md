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

## Workflows
- `What` to execute ? [Architecture (provides abstractions to the user)]
- `Where` (computer requirements + computer(gcp,aws etc) [Compute layer])
- `How` (how to orchestrate the steps)[Scheduler: (run in topological order)]

### Job Scheduler

Runs and schedules each step of the run. Should be able to debug and resume for failed jobs.

How we did at Niveshi:

Custom code. Seperate code for feature engineering and model training. While training we keep track of logs and available via web-interface.
When a job fails => we have an interface that opens up a jupyter notebook(runs on EC2)[in backend it creates the dev envi and runs the kernel]. Artifacts until now (features + stored weights) are stored in S3. We debug and now can correct and then choose to either resume(new git-id+ recent weights) or restart a new job.

Scheduling of jobs : simple queue and retry logic.

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


Observability: Persist as much data(meta-data) after each step
Experiment tracking: Accurate and unmodified audit trail of what was produced during a run.

Commentary on Metaflow:

`DATASTORE`
The artifacts after each steps are stored (pickle) in binary format. Content addressed git-like system.

`Efficiency in metaflow`
Data Parallelism => same task on different components of the data [Hyper-parameter search] (for each construct is provided)
Task parallelism => different tasks on same data at same time.

### Compute Layer
We used AWS BATCH WITH ECS as our container manager. 

- for transient errors just retry submitting code.(aws batch hardware failure)
- track resource utilization and find if code execution is blocked. we timeout

Scalability: satisfy growing amounts of work by adding resources.

Basically we need a container orchestration system(or cluster management system): to look at pending tasks, provision and run containers.

Factors to consider:
1. Workload support: some are specialized(big data/gpu's etc)
2. Latency: some sys gaurentee tha tasks start with minimal delay. (eg: nightly batch jobs it's not a concern but for deployed streaming system: yes)
3. Workload management: How does the system respond when it receives more tasks than it can deploy?
4. Cost efficiency vs operational complexity.

`Tools`

1. Kubernetes
2. AWS Batch : provides a layer on top of various container management systems like ECS(elastic container service), EKS(kubernetes)
(less extensiblity, high start-up latency, low operational complexity)
3. AWS Lambda: (function as a service,container with a single entry point.)
(very low start-up latency, there's a work-queue but more opaque than task-queue of batch,very cost efficient,low operational complexity)
4. Apache Spark. (it defines the programming paradigm upfront)

Rule of thumb: Have one general purpose computer layer (batch or kubernetes) + low-latency system for prototyping(local or lambda)

`Getting Scale`

Guiding principle: start simple

`TODO`

`Going to Production`

`Stabel orcestration`:

We had a seperate production environment and code.
Have a metadata store(no actual data but their links) that stores all artifacts.

Have a RDS(Amazon relational data base) to store metadata and deploy service on AWS ECS container.

HA: You run your workflow and your laptop crashes => not highly available.

How metaflow does:

for production handover to => Use AWS Step functions(SFN) => highly scalable workflow orchestrator. 

we can schedule,highly availabe/fault tolerant.

`Dependency management`
how we deal : prototyping/development (conda(pin python version) + virtual environment(isolation during prototyping)

packages depend on other packages. running `pip install`(each run can give differnt package versions.) does this resolution. If we don't freeze these, production can fail.

Versioning: via namespaces.

### Processing data (Data warehouse + feature engineering)
decouple data and compute, typically loading data is the bottleneck

productivity= local files

S3 LOADING : PERFORMANCE DEPENDS ON THE SIZE AND TYPE OF INSTANCE.

Looking up an object in S3 is relatively slow operation. 
Sometimes loading data from s3 is faster than a local file: if all data fits in cache, s3->in-memory cache, we don't hit local disk.

Which format to store data:
1. Structured data: Parquet (keeps schema,column oriented),each column storred independently,access to data-types of each column=>efficient encoding.
2. Read parquet files using Apache Arrow.

Data management
eg: DATA LAKE=> s3(store facts)
use aws athena(query engine) to create required data-sets and store them as parquet files (again these are stored in S3)
For any heavy processing needs : DASK OR PYSPARK (Map-reduce style architecture)

### Serving and architecture

Central question : How quickly do we need predictions once we have inputs available ?

Batch : 15 mins (push based)

Can use a workflow scheduler and store the results in a DB

Streaming : 30 secs - 15 mins: Can receive data at scale and funnel it to data consumers in a few seconds (apache kafka + flink) eg: Watch next on netflix. (push based)

Real-time : under 30secs. eg: internet advertising (pull based)

For real-time : needs a data-store that supports : low latency feature queries + feature encoders 

<img width="376" alt="Screen Shot 2023-05-18 at 6 24 46 PM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/ce42edff-e40c-4314-b4d2-f342d473b9f2">

Sample recommender system:

For existing users: batch predictions refresh nightly (1 day of watching hist vs past history=> not a bad drop in acc)
Cold start (new users): ask them to select their two most favourite movies. Precompute all possible combinations and serve.


