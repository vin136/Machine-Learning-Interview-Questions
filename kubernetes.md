# Docker

Name spacing and control groups: specific to linux operating system. When you install docker on mac it's running inside a linux virtual machine.

## Commands

- docker run `` : creating and running a container
- docker ps: list all running containers
  - --all : show all the containers that are ever created.
docker run = docker create(copy the file system) + docker start(run the startup command)
- docker system prune: delete all the containers and their images.
- docker stop : issues a sygterm command, gives time to clean up before killing
- docker kill : kills immediately.
- docker exec(run another command) -it(provide input to container) <container_id> <command>

Every process has three channels(STDIN,STDOUT,STDERR). `-i` flag attaches the stdin of the process to my terminal, `-t` just gives some formatting.

#get shell inside the container

- docker exec -it <container_id> sh

- docker run -it busybox sh: runs shell out of busybox image and skips any startup command.
Lifecycle of a container:

every container that's created(`run`) has a unique id that can be used to restart it again.

Note on flags:

-a : pipe the output to the command line.

## creating a dockerfile

specify a base image -> install some software -> give a startup command.

Dockerfile + docker build

#docker build: what's it doing



Commands:

- kubectl create -f db.yml : Create pod using 'db.yml'

- kubectl get pods : get list of all pods

Components:
a. API SERVER(events) b. scheduler(assigs pods to nodes) c.kubelet(manages resources of a node)
Mandatory: apiVersion , kind, metadata


------------

Steps for creating a multi-container application

`commands`
kubectl apply -f <filename>, kubectl get pods, kubectl delete -f <config-file>
To get detailed information about the containers inside the pod:
  `kubectl describe <object-type> <object-name>`
  
 `notes`
  
  How to make an update to the deployment when a new version of the image is available ?
  
  First why its hard : In our deployment config file we don't mention the verision of the image to use. When we `kubectl apply ..`, since there is no change in the file, it'll reject and do nothing.
  
  solution 1: Manually delete a pods. This will trigger recreation.(bad, not safe + users might be affected)
  solution2: when docker building an image add a tag for version.
  solution3: use an imperative command.(kubectl set image <object-type>/<object-name> <container-name> = <full-image-to-use>
  
  
  How do we ensure that your cluster is running what you intend to ?
  
  - Use describe command for a pod.
  
  Why do we need services ?
  
  every single pod get's an id assigned. If a pod get's recreated, it get's a random IP. Service notes down the pod-name and routes any traffic based on it. service = let's u connect to different pods.
  
  <img width="621" alt="Screen Shot 2023-03-26 at 1 30 08 PM" src="https://user-images.githubusercontent.com/21222766/227793408-4d7fffae-dd20-49ea-a61e-d10758d6a1b0.png">

  we cannot update the ports section inside a pod.
  kind and name = update(not a new one,as an identifier)

<img width="542" alt="Screen Shot 2023-03-25 at 12 39 04 PM" src="https://user-images.githubusercontent.com/21222766/227731304-d9cf04f0-2670-4c2d-abbe-bb70f479b047.png">

1. Make a docker-dev file, build it(`docker build -f Dockerfile.dev .`)

why kubernetes: multiple different types of containers on diff quantities on various nodes.

We work with cubectl tool which sends our requests to the master which manages the state.

<img width="1418" alt="Screen Shot 2023-03-26 at 1 15 13 PM" src="https://user-images.githubusercontent.com/21222766/227792586-5f16864a-914e-419a-b614-2f62ce118170.png">

  ----------
  creating a multi-client kubernetes application
  
 clusterip = provides access to an object to everyting else inside the cluster. not to outside services unlike nodeport. 
  
Create a docker file for each of the object

<img width="1339" alt="Screen Shot 2023-03-26 at 2 19 30 PM" src="https://user-images.githubusercontent.com/21222766/227932122-6bfe81d9-7701-4b06-ac64-6298c5567dd5.png">

  
  ## sample codes 
  
  Docker
  
  example of multi-stage docker file (client docker file)
  
  ```
  FROM node:16-alpine as builder
WORKDIR '/app'
COPY ./package.json ./
RUN npm install
COPY . .
RUN npm run build

FROM nginx
EXPOSE 3000
COPY ./nginx/default.conf /etc/nginx/conf.d/default.conf
COPY --from=builder /app/build /usr/share/nginx/html
  ```
  
  docker-compose
  
  ```
  version: "3"
services:
  client:
    image: "cygnetops/multi-client-10-14"
    mem_limit: 128m
    hostname: client
  server:
    image: "cygnetops/multi-server-10-14"
    mem_limit: 128m
    hostname: api
    environment:
      - REDIS_HOST=$REDIS_HOST
      - REDIS_PORT=$REDIS_PORT
      - PGUSER=$PGUSER
      - PGHOST=$PGHOST
      - PGDATABASE=$PGDATABASE
      - PGPASSWORD=$PGPASSWORD
      - PGPORT=$PGPORT
  worker:
    image: "cygnetops/multi-worker-10-14"
    mem_limit: 128m
    hostname: worker
    environment:
      - REDIS_HOST=$REDIS_HOST
      - REDIS_PORT=$REDIS_PORT
  nginx:
    image: "cygnetops/multi-nginx-10-14"
    mem_limit: 128m
    hostname: nginx
    ports:
      - "80:80"
```
  
  
 Kubernetes:
  
  sample service file
  
  ```
  apiVersion: v1
kind: Service
metadata:
  name: client-cluster-ip-service
spec:
  type: ClusterIP
  selector:
    component: web
  ports:
    - port: 3000
      targetPort: 3000
```
  
  Data-base persistent volume claim.:
  
  ```
  apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: database-persistent-volume-claim
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 2Gi
```
  
  Port-forwarding logic
  
  ```
  apiVersion: networking.k8s.io/v1
# UPDATE API
kind: Ingress
metadata:
  name: ingress-service
  annotations:
    kubernetes.io/ingress.class: 'nginx'
    nginx.ingress.kubernetes.io/use-regex: 'true'
    # ADD ANNOTATION
    nginx.ingress.kubernetes.io/rewrite-target: /$1
    # UPDATE ANNOTATION
spec:
  rules:
    - http:
        paths:
          - path: /?(.*)
            # UPDATE PATH
            pathType: Prefix
            # ADD PATHTYPE
            backend:
              service:
                # UPDATE SERVICE FIELDS
                name: client-cluster-ip-service
                port:
                  number: 3000
          - path: /api/?(.*)
            # UPDATE PATH
            pathType: Prefix
            # ADD PATHTYPE
            backend:
              service:
                # UPDATE SERVICE FIELDS
                name: server-cluster-ip-service
                port:
                  number: 5000
```
  
  
  ---------------
  Kubernetes monitoring, logging and auto-scaling ?
  
  We’ll explore commonly used and basic ways to auto-scale Pods based on memory and CPU consumption. We’ll accomplish that using` HorizontalPodAutoscaler`.
  The critical element in scaling Pods is the Kubernetes `Metrics Server`.(using helm)
  Just provides an API to get information about pods. Manually get info `kubectl top nodes`. hps uses this behind the scenes. here's a sample object:
  
  ```
  apiVersion: autoscaling/v2beta1
kind: HorizontalPodAutoscaler
metadata:
  name: api
  namespace: go-demo-5
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: api
  minReplicas: 2
  maxReplicas: 5
  metrics:
  - type: Resource
    resource:
      name: cpu
      targetAverageUtilization: 80
  - type: Resource
    resource:
      name: memory
      targetAverageUtilization: 80
  ```
  
  what if there’s not enough available memory or CPU, new Pods will be unschedulable and in the pending status. If we do not increase the capacity of our cluster, pending Pods might stay in that state indefinitely. To make things more complicated, Kubernetes might start removing other Pods to make room for those that are in the pending state. That, as you might have guessed, might lead to worse problems than the issue of our applications not having enough replicas to serve the demand.
  
  set-up: --enable-autoscaling argument when creating the cluster. It already comes with Cluster Autoscaler pre-configured and ready.
  
  Prometheus is a pull based data-base. We'll have third party exporters that takes the metrics and converts to prometheus format.
  
  
  
  
 -------------
  How I use docker/automation in my work ?
  
  Goals: reproducability, ease of development and deployment. Create a machine learning system that we can reliably iterate on.
  
  1. get a virtual env (pyenv for python version control
  2. set up logging 
  3. set up formatting and linting
  4. Makefile : automation tool for organizing commands.
  && => execute commands in one shell
  
  can be used to specify prereqs => here style before cleaning
  
  ```
  # Cleaning
.PHONY: clean
clean: style
    find . -type f -name "*.DS_Store" -ls -delete
    find . | grep -E "(__pycache__|\.pyc|\.pyo)" | xargs rm -rf
    find . | grep -E ".pytest_cache" | xargs rm -rf
    find . | grep -E ".ipynb_checkpoints" | xargs rm -rf
    find . | grep -E ".trash" | xargs rm -rf
    rm -f .coverage
```
  
 Testing code, data and models:
  
<img width="669" alt="Screen Shot 2023-03-27 at 12 07 28 PM" src="https://user-images.githubusercontent.com/21222766/227999247-fc6aa1b6-a9b7-443a-86cf-57ddf69c2282.png">
  
  We'll use pytest for testing:
  
 Testing functions. 
  '''
  # tests/food/test_fruits.py
def test_is_crisp():
    assert is_crisp(fruit="apple")
    assert is_crisp(fruit="Apple")
    assert not is_crisp(fruit="orange")
    with pytest.raises(ValueError):
        is_crisp(fruit=None)
        is_crisp(fruit="pear")
'''
  
 U can also parametrize:
  
  '''
  @pytest.mark.parametrize(
    "fruit, crisp",
    [
        ("apple", True),
        ("Apple", True),
        ("orange", False),
    ],
)
def test_is_crisp_parametrize(fruit, crisp):
    assert is_crisp(fruit=fruit) == crisp

  '''
  
  Additionally we can mark the tests, say for compute intensive tasks.
  
  b. Testing data
  
  `Great-expectations`: test for missingness,unique vals,type-adherence.
  
  Training tests:
  - Check shapes and values of model output
  - Check for decreasing loss after one batch of training
  - Overfit on a batch
  - is it completely trained (are artifacts generated)
  
  Behaviorial testing: perturbations and their outputs.
  
  Testing for inference: one row and batch 
  
  Monitoring: hese expectations continue to pass online on live production data while also ensuring that their data distributions are comparable to the reference window (typically subset of training data).
  
  ---
  Using git to it's best
  
  1. pre-commit hooks: 
  Before performing a commit to our local repository, there are a lot of items on our mental todo list, ranging from styling, formatting, testing, etc. And it's very easy to forget some of these steps, especially when we want to "push to quick fix". To help us manage all these important steps, we can use pre-commit hooks, which will automatically be triggered when we try to perform a commit.
  
 <img width="656" alt="Screen Shot 2023-03-27 at 12 59 08 PM" src="https://user-images.githubusercontent.com/21222766/228012785-263ba239-b402-42af-b5dd-33f9742a9c28.png">
  
  Deploy to cloud-run or apprunner.
  
  ---------
  Jenkins x for further automation:
  
  commands
  
  jx promote <> (to manually promote to production environment)
  
  -------
  Shell basics
  '''
  cat /var/log/nginx/access.log |
      awk '{print $7}' |
      sort             |
      uniq -c          |
      sort -r -n       |
      head -n 5
  
  '''
  
  awk => extract specific info from a file
  awk '{print $1}'
  
  awk -F "/" '/^\//' '{print $NF}' /etc/shells :(use '/' as the line splitter, get all lines that start with '/' and print out the last element in each of those lines)
  
  SED:(search and replace)
  
  networking commands
  
  `ifconfig` : all the devices connected(virtual or hard)
  
  ip -4 addr : get your ip address
  
  netstat : detailed info about your network connectivity. (netstat -a, netstat -at (only tcp connections), netstat -l (only active ports))
  
  curl <add> : sends a get request
  
  can also send post/any methods curl -X post --data "p1= value1" <address>
  
  ping <address> : just sends packets 

  
