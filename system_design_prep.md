# Questions: Front end

When there is any new application, you'd want to start developing independently, how's the communication/synchronization, say you might need some functionality before you can do etc ?

# API Design

## Basics
Api's over the web, communication types.

Many protocols built on top of 

`TCP` : HTTP,websockets,smtp,SSH
`UDP`: WebRTC(peer-peer,eg: googlemeet)

building a realtime chat-system with HTTP , client needs to do polling and expensive,
- with websockets, you send a http request and your connection is upgraded to websocket(status code 101), then a persistent connection is established.once established data can move in either side.
- con of websockets : stateful, hard to recover from lost connection.

## Represention

  Data Formats
  Textual formats: good when the client is outside organization as readability plays a big role.
  
  - JSON => Compact,human readable, and language agnostic(imagine pickling the data and sending)
  
  Binary Format: much more compact with stronger schema.
  - protobuffer,apache-thrift,avro

## Paradigms

REST API

What's REST ?

It's the series of guidelines made to make web scalable,reliable,here are those

- Client-Server architecture,aka request-response : seperation of concerns
- Cache : store response to the earlier request. response data should be explicitly marked as `cacheable`. Tradeoff: reduce reliability(stale data) but increases speed. But typically we have `Expires` header in the HTTP response.
- Stateless: Reliability(under failure,since server stores no data,client is not effected) and Scalability(since data is removed after processig a request,server can handle more requests)

  `Cons`: repeated effort,or establish connection.
Moreover it's architecture is `layered and uniform interface` thus we can put proxy's,load-balancers etc in between client and server.

<img width="862" alt="Screen Shot 2023-06-24 at 2 26 20 PM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/b1590d7a-6450-47f3-8980-1ddff3b2d934">

**Best-Practices**

- Filtering and pagination : eg `https://www.example.com/posts?author=fahim`
- `API versioning`: eg: API versioning can be done by adding /v1 or /v2 to the API path.

  **Cons of RestAPI**

`Multiple requests problem:` A REST API sends multiple requests whenever data required by the application resides on multiple endpoints
`Overfetching and underfetching`

Example:
stateless design example: consider pagination with youtube : when user scrolls client sends the offset and limit,thus server needs to store no state. `https://youtube.com/videos?offset=0&limit=10`

Overfetching and underfetching: Say we want to show comments, require username,photo and comment, the `\user` endpoint might have more data.
  

**GRAPHQL**
It uses POST request where in body we give the `query`. For each resource we can specify the fields we want.

- Query (read)
- mutation (any changes)

Built on `POST` it's not idempotent thus caching is little bit hard. Also,GraphQL server uses a schema to describe the shape of your available data.

CONS : 1) Cachining is hard, 2) error-handling is not that good, we need to parse the response.

**gRpc**

Built on HTTP2. We need proxy can't be used with browser. Sends proto-buffer instead of JSON. => much faster and efficient. By directional streaming.

`cons`: less tooling + slower to develop.

## Security

1. Trasport layer security: client is able to authenticate the server via `TSL`.

Client a. ensure the server identity(via digital signatures) b. Confidentiality: encryption

2. Input Validation.

why ? : to protect from attacks like SQL/CODE Injection

**Client side-validation**
eg:


```

   function emailValidation() {
  let entity = example.forms["Form"]["email"].value;
  if (entity == "") {
    alert("E-mail is a required field");
    return false;
  }
}
```

**Server side validation**:
eg: Password strength check. if not strong we get a response.

Why's client side validation required ? => reduce user latency. and reduce strain on server.

3. Authorization and authentication

HTTP PROVIDES Basic authentication via Authorization header. eg: Authorization: Basic Qm9iOnRoZWZhcm1lcg==, this is the base64 encoding of the username and password.

`cons`: credential are encoded instead of encrypted.

API KEYS : authenticate the application sending the request

`pro`: attacker has a compromised key, they won’t have the user’s credentials.
`con`: need another authorization protocol

`JSON Web Token`

`pros`:
- offers temp access to a resource and scalable. but has  `overhead`

The process is like client sends a basic authenticate request using http header, username, password.  validates and sends the token,client sends this back and eventually connection is verified.

But typically we use industry standard token-based authorization tools like OAuth 2.0.

**Some important concepts**

- API rate limiter : throttles(ignore) clients' requests that exceed the predefined limit in a unit time. Can also to prioritization of requests.

Where to place: Server side or as a Middleware. if on client side service provide configuration can't be easily applied.

- EVent driven protocols: The event-driven architecture protocols provide users with the intended data without making a request to the server whenever an event triggers. We learned that event-driven architecture is a suitable option to address the limitations of HTTP techniques for real-time communication

eg: Websocket: both sides communicaton happens, there are other one's only where client get's info from server everytime there's an event.

- Cookies and Sessions: A cookie is a small amount of data stored by a specific website (server) on the user's computer.Cookies are also used to establish sessions on the server side, session info is stored on server side.

- Idempotency: It allows caching thus optimization. Good as it avoids possible data duplication issues(imagine a bank transaction)
GET, PUT,DELETE => IDEMPOTENT, PATCH,POST=> NOT.

How to make POST idempotent: client can add a unique request identifier.

 - Serverside vs Client side rendering
   - Client side: The server sends the empty HTML file (which means no HTML content is populated except the basic structure of the HTML file) with JavaScript code or a link to it. The browser executes this JavaScript code to generate the web page. (Good to use when data changes frequently)
    - Serverside: sends the complete HTML. (GOOD FOR INITIAL LOAD,else making multiple requests for html is not good.)

- Caching
 - client-side: Browser caches resources  related to HTML, CSS, and other multimedia files
 - server side:
     - API gateway cache:  stores responses to frequently requested API calls
     - Application server cache:  data is stored on the database, and fetching the data from the disk takes much more time than the RAM. This layer stores the frequently accessed data objects in different formats.
      memcached and `Redis` for server-side caching.
  - Middleware cache:
      - CDNs
      - DNS, ISP
For caching HTTP response provides various headers that let's u decide what/how to cache. eg: `Last-Modified`,`Cache-Control`



Good API design =  Forward and Backward compatibility, pagination, versioning

## Example API designs:

- API GATEWAY: when you have multiple microservices inside an application, we have middleware that does authentication and provides a facade with loose coupling, the services themselves are abstracted away.
- gateway(reverse proxy) hides the server’s origin and retrieves internal data for the client. The gateway acts as a single endpoint that client apps use, and then redirects all requests to internal (micro)services. This way, only this **one endpoint** is exposed to the world.

- Authenticates and authorizes incoming requests,throttles requests based on rate limiting,Caches the response to frequently made search queries


# Testing

Unit tests: tests on individual components that each have a single responsibility (ex. function that filters a list).

Integration tests: tests on the combined functionality of individual components (ex. data processing).

Regression tests: tests based on errors we've seen before to ensure new changes don't reintroduce them.

ML systems can run to completion without throwing any exceptions / errors but can produce incorrect systems
`Unit and integration testing of all the preprocessing code` :

Testing framework - Pytest

```
# tests/food/test_fruits.py
def test_is_crisp():
    assert is_crisp(fruit="apple")
    assert is_crisp(fruit="Apple")
    assert not is_crisp(fruit="orange")
    with pytest.raises(ValueError):
        is_crisp(fruit=None)
        is_crisp(fruit="pear")
```
Can parametrize to avoid redundancy

```
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

```


`Testing the Data`

We used to get data from the client and someone else is responsible for cleaning and uploading it to S3. Now the thing in machine learning is it fails silently.

Test the column types,uniqueness, distribution ranges, missingness etc. Tool: `GreatExpectations`
```
# Unique values
df.expect_column_values_to_be_unique(column="id")
```

`Testing Models`

Some basic tests on models like

- dec loss after one batch
- overfit on a batch

  `Behavioral testing`

  Imagine you've updated a sentiment analysis system you've trained and tested on you. Now in the same sentence you replace with synonyms, output label shouldn't change `invariance testing`. Now you might have `directional expectations`. For us we introduced noise and checked action consistency.

# System Design

## Short list of tools

API GATEWAY: LOOSE COUPLING WHEN LOT OF SERVICES, REVERSE-PROXY, Authentication, and caching on the client side.

Databases: STORE DATA + sharding and replication. 

Configuration service : eg:zookeeper.


## Core Concepts

**General Tools**

### DataBases

**Relational Databases.**

Stress on 
A: atomicity : all or nothing for transactions
C: consistency : constraints (unique ids',non-null, primary-key etc)
I:Isolation : serialization (multiple concurrent transactions can lead to inconsistent states eg: DirtyReads => when one transaction reads a value that's not committed yet)
D: durability (permanently exist)

**NoSQL**

`Key-Value` => Redis,memcache (uses RAM)

USE: small data with very quick reads and writes.

`Document Stores`: MongoDB

`USE`: No Schema and very deep nesting.Not many joins are needed.+ SCALED easily.

`WideColumn Database`: Cassandra

USE: time series data, typically best when we have lot of writes n not much read/update + Flexibility(schema or noschema) + Massive Scale

`GraphDB`: Neo4j

USE: Natural graphs


Why Scaling SQL databases is Hard ?

Imagine creating two shards => now how to know where to route, joins, and enforce non-null, foreign constraints.

NoSQL Gives Eventual consistency.
----------------------

Example of scaling SQL based database

Scalability and Performance: Use a Proxy-server that decides which shard to route the information + Configuration Service(Zookeeper) maintains shardState + ShardProxy(cache query result,publish metrics,terminate queries that take too long to run) 

Availability: Replication, maybe a ReadReplica. And put these in different datacenters. After write data is syn or Asynch replicated.

<img width="600" alt="Screen Shot 2023-06-24 at 7 50 30 PM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/2d4fa2c8-595d-4b90-8404-ca58d2b7b447">

Now Consider NoSQL: Apache Cassandra

- Data is split into Shards, but each shard talks to each other, so no configuration service needed. Shard exchg info with few others(gossip protocol),clients call any node(closest or round-robin), during writes it sends the request to multiple nodes and waits for confirmations, called as quorum rights and reads. Deals with staleness. + stores copies.
<img width="600" alt="Screen Shot 2023-06-24 at 7 56 43 PM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/a5f8d61c-6baf-492c-8523-fe297a5035ba">

-------------------------------

### CDN's and Caching

Point to keep-in-mind when using these in system design

**CDN**

How data get's into a CDN => a. push server (when data get's to origin server,it pushes it to CDN's), b. Pull: first look at closest cdn, if not found it acts as a proxy and request from origin, now it stores this copy, better if `diff users have different patterns`.

eg: say signing up to twitter and we are using apple to signup, since it's static code we are likely getting it from APPLE CDN for authentication.

**Caching**
Imagine twitter getting tweets

how data comes in ?
Cache-aside (we can also expiry time on cache-entries to overcome some drawbacks)

<img width="600" alt="Screen Shot 2023-06-24 at 8 32 27 PM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/8aa630b3-9936-4b19-8457-7a540b2b38eb">

Read and Write through cache 

<img width="600" alt="Screen Shot 2023-06-24 at 8 40 00 PM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/3e315864-d769-45c1-b6ef-dc71dbd885c9">

Write-back

<img width="600" alt="Screen Shot 2023-06-24 at 8 41 25 PM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/409ed61e-94da-483f-8415-427810c195cd">


- write around cache : first write stuff on disk, in read if there's a cache-miss write it down.

- write back cache : put in cache,don't upload to disk and put it in disk lazily (fast writes with less durability eg: maybe liking)
  
hOW Data goes out of cache when filled ?

Many caching algorithms eg: LRU(makes sense in case of twitter),LFU,FIFO. Also time-based eviction but it's typically implemented as refresh-ahead,useful if there are hotkeys(very frequently accessed)

<img width="600" alt="Screen Shot 2023-06-24 at 8 23 20 PM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/8f425f31-491d-44c3-9da1-7a60b626960d">

Cache can be used to deal with duplication => put(in databases) or even in msging queues. Basically store the massage hash in the cache.

Eg:
<img width="600" alt="Screen Shot 2023-06-24 at 8 26 45 PM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/21f6079e-e68e-410a-bb73-7c2a102b4295">

--------------------------------------

### How to build efficient communication in distributed systems (Throughput)

How to choose a protocol?

TCP: 3-way handshake, reliable,ordered,connection-oriented. Client can establish hundreds of TCP connections. Consume resourcers

HTTP: connection(TCP) per request.

UDP: NO-connection,no-handshaking. 
 - very fast but not reliable.





### How to deliver data quickly ?

1. Batching => instead of making multiple http requests make a single one with multiple requests
eg: google drive batchapi, kafka, SQS

<img width="600" alt="Screen Shot 2023-06-24 at 8 55 36 PM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/ed8cdc01-b9db-4a9d-a47c-1026f4f9f368">

<img width="600" alt="Screen Shot 2023-06-24 at 8 56 34 PM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/7d2a52c4-19ba-48c9-9107-8986f66c31fe">

2. Compression

Think about compression in databases(say configure cassandra db for compression),queues etc

### How to deliver data at large scale ?

1. Partitioning
  Used in kafka and other msg queues, also in databases.
How to partition in databases ?

<img width="600" alt="Screen Shot 2023-06-24 at 9 49 04 PM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/8f1c0099-ea7c-4055-8ffe-e3d2bf0c5d29">

How to partition ?

Range-based

<img width="600" alt="Screen Shot 2023-06-24 at 9 55 22 PM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/c808d8b0-58b6-4ea7-8d81-87d7b8c60ba4">

Hash based
<img width="600" alt="Screen Shot 2023-06-24 at 9 56 35 PM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/979dcccc-4795-4c1c-b03c-df2518bcc749">

How to rebalance the shards ? => many ways.




 

### How to protect servers from clients ?[System Overload]
<img width="600" alt="Screen Shot 2023-06-24 at 9 07 02 PM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/18763726-8afe-4ab1-b78d-0bbe5ceda313">

The manual is a bad idea, and resource estimation is also a hard problem. Autoscaling => solves unpredictable traffic spikes.
How to autoscale ?

<img width="600" alt="Screen Shot 2023-06-24 at 9 10 12 PM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/b9851c17-aa16-4254-988a-da87289470ba">

Autoscaling system design of a web-service ?

Notes: here load-balancer is also used for service-discovery=> how to know about the new servers created by the provisioning system.

<img width="700" alt="Screen Shot 2023-06-24 at 9 18 12 PM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/dbed84fc-d87f-4957-8582-97e7aa5638f9">

Load Shedding and rate-liming : Auto-scaling takes time thus still server can get overloaded.

Load Shedding: when request vol reaches beyond a certain threshold just ignore/drop the incoming requests.

<img width="431" alt="Screen Shot 2023-06-24 at 9 21 26 PM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/1cadcd29-850f-4d6a-b3b4-43e8d3aefb36">


Load testing is used to determine thershold for load-shedding and also thread tuning.

<img width="600" alt="Screen Shot 2023-06-24 at 9 25 45 PM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/3ba552f4-5694-4f7e-b657-8f11bc938848">

<img width="600" alt="Screen Shot 2023-06-24 at 9 25 11 PM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/0f9038a8-34e2-45ac-921f-e8a1daa28bba">


Rate-Limiting: how to fairly handle various client requests. under load-shedding the client that sends lot of queries get's an upper hand in processing.
<img width="374" alt="Screen Shot 2023-06-24 at 9 32 24 PM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/908162fc-2a90-4b83-b4f9-71b6fdb81594">



### How to protect clients from servers?

How does the client deal with exceptions from servers after load-shedding/rate-limited ?

Client thinks that prob is transient but what if server has trouble autoscaling and may take a lot of time. Intelligent retries doesn't help and client may exhaust it's resources via retries ?

Circuit breaker: count #retries and then stop after a threshold.

<img width="478" alt="Screen Shot 2023-06-24 at 9 43 23 PM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/e986a081-c5d7-4ded-9d00-c059884e6961">

### Tools

Notes: 
For client side patters like timeouts,retries,circuit-breaker pattern
Load balancer : NGINX, AWS ELB
Messaging systems: kafka / kinesis
Data processing: to process events and aggregate them in memory (apache spark/flink)
Storage: Apache cassandra
Store raw events : any cloud data-warehouse or apache hadoop
Other tools
vitess=> large cluster mysql instances
distributed cache=> Redis
Manage server discovery => Zookeeper.
Monitoring => AWS Cloudwatch

How to identify bottlenecks => Load testing(test under specific stress), stress-testing(identify breaking point), tool: apache jmeter
Health monitoring => metrics and dashboards (latency,traffic,errors)
Testing => how to ensure your system is doing correctly say count likes on youtube ?
sol : Auditing system 
  - weak audit system: generate requests for a while and count if it's as expected.
  - strong audit system : But still we miss rare events ? => one thing people do is they have two systems say hadoop with mapreduce vs streaming approach. (called lambda architecture).



# System Design examples

General model for scaling a system


Notes: Each web server in the cluster can access state data from databases. This is called the stateless web tier. removing state info from the server. In figure `NoSQL` DB is used for state-management.

The servers should be spread across the globe and in different data-centers. To further scale our system, we need to decouple different components of the system so they can be scaled independently=> Messaging queue .

`simple`

<img width="703" alt="Screen Shot 2023-06-25 at 8 45 07 AM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/2fba6119-5659-4941-865a-4e0c5c930980">

`final`

<img width="477" alt="Screen Shot 2023-06-25 at 8 54 58 AM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/be46e063-991e-4e4a-8d14-4dde3122746a">

## Design Twitter

Functional Requirements:
1. Follow each other and create tweets(can have images,vid etc)
2. Viewing a tweet-feed (we'll just see people that we follow)

Non-functional
1. How many tweet reads per-day => 200m/Dau,each reads 100/day,size of each tweet => 1MB(On avg) => overall we are reading 20peta-byte
2. How many writes => much less than reads
3. How many follower can a user have => some can have huge

Where do we store data ? => relational vs non-relational
first there is some relationships going on, followers and followers, though scaling is an issue., but we can also have a graphDB.

<img width="200" alt="Screen Shot 2023-06-25 at 9 09 36 AM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/ecca4d30-8fec-4b39-90d5-dcb9776d739d">

Above image talks about reading n showing tweets. now let's talk about the interface/API

- createtweet(text,media,uid)
- getfeed(uid)(need authentication here)
- follow(uid,oid)

How are we storing data ?
we'll have two tables , 
 - tweet,
 - follow(just two columns,index by follower)

Now we are hitting a lot of reads => leader-follower replication

For reads how do we shard ? maybe user-id, tweet-id will be a bad thing. 

So all the tweets corresponding to  a user is in a shard, and also in follow tabel we have all the follower-followee list in a single shard. Now to retrieve the actual feed, we need to read tweets from multiple shard and then sort them based on time-stamp.

Now we also want to order the tweets. Should i fetch all the tweets or based on user scrolling ?

First LRU cache helps to have some popular tweets in cache but for a specific user we can't expect to have his feeds in cache. Can we pre-generate the feed for all users ?

<img width="600" alt="Screen Shot 2023-06-25 at 9 25 37 AM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/d64eec7b-de90-4d43-b8e9-aee77b228054">


Now, we can also shard the feed cache that has all the feeds that get's updated asynchronously via pub-sub style model.
- But here if someone popular tweets, we'll update a lot of feeds in the cache.
- also if the user follows someone new(here we can tolerate some delay)





## Design Youtube



Core functionality

Functional : user's upload, and be able to watch. (let's focus on these)

Non functional
 - Durable(storage of videos)
 - Scalability(about 1B dau), lets a ratio of reads/writes = 100:1,let a user watch 5 videos/day => (1/100)*5Billion are uploaded per day = 50 Million. Maybe most videos don't get views
 - Availability(always should see the feed) vs Consistency(may miss some subsscription video)
 - Minimize latency as much as possible.

User uploads a video => API upload(format,uid,metadata)
Storage => MongoDB (video document, user document, denormalized aka replication, this means when a user changes to say profile pic both the user doc and the video doc's need to be changed, here lag is fine and we can do asynchronous updates.)

For viewing => 1. we can send in small chunks.,2. how to send chunks=> tcp vs udp,tcp is better unlike live streaming where missing a frame is ok.
We need rate-limiter(inside load balancer) interms of uploading video's.

<img width="868" alt="Screen Shot 2023-06-23 at 9 24 13 PM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/5427d67a-83ce-4622-8c93-ecb736fee31c">

## Design a monitoring system
Design a system that collects application performance metrics. Imagine you work at a large company that runs thousands of services. For all these services, we want to collect performance metrics such as throughput (number of requests processed per minute), errors (number of requests that fail), average response time, etc. Service owners should be able to see their per-minute service metrics in the dashboard.

**Functional requirements**

The system should store metrics at one-minute granularity.
Older data should be stored at lower granularity (e.g. 1 hour).

**non-Functional requirements**
High scalability (the total number of metrics will grow rapidly over time).

High availability (keep in mind that latest data is typically more important than old data).

High read performance (especially important for high resolution alerts configured on top of metrics).

Low dollar cost (especially for storing less relevant/old data).

**Key Actors**

Metric producer services, programs (web services, applications, batch jobs, etc.) that emit performance metrics.

User, typically an owner of the service, a person who watches the metrics.

Metric consumer services, programs that read metrics on a regular basis to timely react to their changes (e.g. alert generation systems, autoscaling systems, anomaly detection systems).

**Key components**

<img width="700" alt="Screen Shot 2023-06-25 at 10 25 00 AM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/5fe5e41b-cd2a-48d5-b250-f174d252e174">

API gateway, a web service that represents an entry point to the system.

Metric aggregator, a service that aggregates metric values in memory over a short period of time and stores the aggregated values in persistent storage.

Metric partitioner, a service responsible for partitioning metrics. This allows for more efficient aggregation of metric values.

Messaging system, a temporary buffer for metric data that helps parallelise metric processing.

Monitoring client, a client application for a monitoring system that helps to aggregate data on the client side and send aggregated data for further aggregation on the server side.

Hot storage, read-optimized storage.

Cold storage, persistent storage for metric values.

Read service, a web service responsible for serving read requests by retrieving metric data from multiple locations (hot and cold storage).

Now think about data-flow

**high-level architecture**

<img width="922" alt="Screen Shot 2023-06-25 at 10 28 09 AM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/01ac1d6b-0217-40f8-a86a-4999bc77f613">

Monitoring client is a daemon process that runs locally on every metric producer service machine. It reads data from service logs and aggregates data in a local memory. Think of it as a hashmap where a metric name represents a key (e.g. "total request count") and value represents a count (of requests, errors, etc.) or a sum (of response times, request bytes, etc.). Periodically, for example, at the end of every minute, all accumulated values are sent to the monitoring system.

API gateway is a single entry point for all requests. It is a common component of many modern architectures. Classic API gateway performs many different functions: authentication, authorization, request routing, response aggregation, protocol translation, load balancing, TLS termination, IP listing, rate limiting, response caching, response compression, logging, monitoring, and a few more. We will cover API gateways in more detail in the second module of the course. If the monitoring system and metric consumer services all live within a trusted environment and read latency is critical, we can have metric consumer services call the read service directly.

Each producer service can produce thousands of different metrics. And there can be thousands of producer services. Which can lead to high cardinality of metrics. To aggregate data on the server side, which means calculating the total count or the total sum for each metric for every 1-minute interval, we need to partition metrics first. This allows us to split all metrics into several disjoint groups, and then aggregate metric values within each group. We can use a unique metric name (metric name + service name) as a partition key. We can avoid partitioning metrics if metric cardinality is low.

Incoming metric data is buffered in a messaging system. Each metric goes to its own shard/partition inside the messaging system. There will be a separate consumer (metric aggregator service instance) for each shard.

Metric aggregator service aggregates metric values. Which basically means it sums up the incoming values for a metric. It does it for a predefined duration (e.g. 5-10 seconds) and sends the accumulated value to persistent storage. If we need to scale and/or speed up data aggregation, we first add more shards/partitions to the messaging system and then add the same number of machines/instances to the service. So that each new shard gets its own dedicated consumer.

Hot storage represents a distributed cache (e.g. Redis) or a key-value database (e.g. DynamoDB) and stores the last N days of data. In monitoring systems, the most recent data is more important and read frequently, while older data is usually less important and read less frequently. Therefore, this storage has two key requirements: high read throughput and low read latency.

Cold storage represents persistent storage for data. We can store all data there (if our hot storage is not persistent) or only data older than N days (if our hot storage is persistent). As discussed in the course, object storage (e.g. S3) is a good option. Data in hot and cold storage is stored at some granularity, for example, 1 minute. But on a large scale (from millions to billions of metrics), storing such a large amount of per-minute data is costly. In addition, as we have already mentioned, the value of monitoring data decreases over time. For this reason, it makes sense to aggregate data over time into 5-minute intervals, 1-hour intervals, and so on. The system should have a separate component responsible for aggregating data from shorter time intervals to longer time intervals and purging data for shorter time intervals from the storage.

Read service has three primary functions: route read requests to the appropriate storage, stitch data from hot and cold storage when both new and old data is requested, cache responses. The most recent monitoring data is not well suited for caching because it changes frequently. But old data can be effectively cached. When there is a read request that spans both storages, we will fetch new data from hot storage and old data from an internal cache of the read service.

**General video analytics**

1. Functional API requirements
   
<img width="600" alt="Screen Shot 2023-06-25 at 10 54 44 AM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/a440cd5b-51de-4f0e-abcf-310fa31de650">

2.Non-functional req

<img width="700" alt="Screen Shot 2023-06-25 at 10 57 57 AM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/5cf55d40-052c-4a6d-9aef-cdaabc54077e">

3. High level architecture

just throw high-level components that are needed, for our case

<img width="932" alt="Screen Shot 2023-06-25 at 10 59 56 AM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/bbdb6915-e0ec-40e5-aa2a-f0e954664fd0">

And now drive the conversation and choose which part of the system you wanna talk about. Typically it's data-model

What data you want to store , where, How ?

What ?

<img width="600" alt="Screen Shot 2023-06-25 at 11 03 25 AM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/336183c3-8637-49a2-ac50-46b0311c567a">

So which one should we choose ? Ask the interviewer. => Stream data-processing vs Batch data-processing

Let's focus on real-time aggregation.

Where do we store ? 

Think and design based on functional requirements

How ?

Think of data-flow. Let's say we want to build a report

Nouns : If using rDBM => THINK about entities
Verbs: if NOSQL => think about query and store it.

Note about cassandra: it's fault-tolerant and both read-write throughput inc linearly as we add new machines. Supports multi-datacenter replication n supprots timeseries data. SUPP: Asynch masterless replication.

<img width="600" alt="Screen Shot 2023-06-25 at 11 10 15 AM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/70361bb0-997b-4059-bb58-bfba4860fd55">


**PROCESSING Service**

If video user opens => show real-time count
if uploader opens => show hour-level counts

Requirements: scalable,reliable and fast.

<img width="400" alt="Screen Shot 2023-06-25 at 11 15 57 AM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/a1c120c4-e860-4208-a6b7-8ba2a4caaa3a">

Data-aggregation:

<img width="600" alt="Screen Shot 2023-06-25 at 6 05 24 PM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/82d16013-df73-436a-bb9f-4b8a8e9c7f47">

Eg: Push -based => rabbitMQ, Pull based => Kafka.

Should the processing service pull-events or get events pushed. Pull is better as there will be other temp storage in case of service failure.(reliability)

<img width="939" alt="Screen Shot 2023-06-25 at 11 21 29 AM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/f356f081-c3b0-4dfb-ba64-e3b02c2d59f8">

We don't want to loose raw-events ?

1. checkpointing: We can checkpoint after we processed some events determined by offset
2. partitioning: we can put events on diff queues

   <img width="400" alt="Screen Shot 2023-06-25 at 11 23 43 AM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/3f48c65b-eeea-4ea6-8607-7e639d07f6f2">

Detailed design of processing service

we need a partition consumer=> maintain connection and deserialize data. Also helps to eliminate duplicate events, we store a dist-cache for this.Typically this is a single thread(else complex inc say for checkpointing)

Aggregator : keeps accumulating the data,like a hash-map temporarily and pushes to internal queue.

Internal queue => decouple consumption and processing(of each temp hashmap),as here we can have multiple consumers(no issue). (imagine the airport check in , single-line for ticket check but multiple lines for screening)


DATABASE WRITER: 

- Deatletter queue => a place where msgs can be sent if they cannot be routed to correct destination.
- WHy ? protect ourselves from database per or availability issues.

Dataenrichment

- Remember how we store data in cassendra, we need video title with views count etc. But all these features doesn't come to processing service, thus this info comes from somewhere else, but trick is this DB should be on same server, thus eliminates remote calls => embedded databases. eg: linkedin use this for who viewed your profile where they show additional info like how many are recruiters.

State-management:

we keep things in memory but what to do if machine fails, but we have event stored in partition and can restart from there. better approach is to store the state:

precessing - service

<img width="600" alt="Screen Shot 2023-06-25 at 11 37 55 AM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/703c7d73-076a-45cb-8fed-c2793c9da241">

here's the data-ingesting pipeline


<img width="925" alt="Screen Shot 2023-06-25 at 11 39 49 AM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/9e2d0014-0e64-49ec-a62b-d2129608487a">

**Partitioner-service-client**

`blocking vs non-block I/O`

thread per request => When a client makes a request to server, it processes and sends back the request. Client initiates the connection by sockets, the thread that handles that connection is blocked, thread per connection, but if server starts to slow-down then whole cluser dies, thus we need rate-limiter.

alternative is non-blocking I/O => server just queues the request and then actual i/o process at some point,piling req is less expensive than piling threads.

Tradeoff: non-blocking => inc complexity, blocking=> we can use thread local variables etc.

`buffering and caching`

Combine events together and send several of them togeter.

`timeout`

- how much time a client is willing to wait for connection timeout: connection to establish, request-timeout: request. What should we do
- retry, (exponential backoff and jitter)
- still we can make too many retries=> circuit breaker pattern.

  
**Load-balancers**

How cone lead-balancer is not single point of failure ? how does loadbalancer know the partition-services, and it should know the health ? availability(primary and secondary)

Service registry registers to a load balancer.

**partitioner service and partitioner**

partitioner service 

=> retrieve individual video view event(remmeber we batched on client side), and route to a partition. + uses a strategy to partition. how to ensure fair load/balance ? (diff problem) a. split a big partition to two new partitions (consistent hashing) b. maybe for popular video channel use a diff partition.

client side service discovery => a webservice to which each partition registers thus partition service knows which nodes are available.

other option is like as in cassandra.

Partition replication => single leader and peer-peer

partition => also a webservice of pre-defined size that stores time ordered msgs

**Data retrieval path**

for retrievivg time series data => lot of data=> solution is roll-up, trick is we don't need to store all data in DB (cold storage)

<img width="600" alt="Screen Shot 2023-06-25 at 12 04 23 PM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/aa5cdbff-f223-4ced-a15f-4cc6b2131fef">


Notes:
Tools:
data-enrichment => embedded DB: ROCKSdb
scalin n managing MySQL: vitess
dist-cache for message deduplication: Redis
temp queue n deliver msgs: RabbitMq/Aws SQS
leader election n partition n server discovery: Zookeeper
Monitioring: AWS CLOUDWATCH,ELK(elastic,logstash,kibana)


<img width="974" alt="Screen Shot 2023-06-25 at 10 44 52 AM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/42a76fd8-9cbf-483f-98d0-e119ae901edb">


## Design Discord/slack/msging

Functional

Servers and channels.
We should have notifications of mentions
We should see the msgs with low latency,
should be able to pick from where left off in a chanel.

Non-functional

Low latency
available,redundant,etc
Scale (medium) => How many people could be in a server. Estimate => 10k msgs/day


High level actions

SendMsg(body) => can be done in a HTTP request.
server,channel

But seeing msgs=> 
 - HTTP via polling is fine but not good, say we poll every 1 sec not very good as too many requests
 - Websocket (HTTP streaming available in new version)

How do we handle page load, opening and seeing a channel ?

ViewChannel()
<img width="600" alt="Screen Shot 2023-06-23 at 10 15 46 PM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/1d6c2a0f-aefc-499d-a962-31e72e097dfd">


# Design Google drive

Functional

Upload
Download
Remove
Folders/hierarchy

Non-functional

50M active users,each user get's 15GB

Observations => storage req is huge.

We want durability

Read/write ratio => 2:1

What's the big problem => always available + reliable

Data Model

data = file,image,video etc
metadata = Database, NoSQL, not too many joins

Files => HDFS vs ObjectStore

- file-systems are stored in hierarchy,can modify files too, but complex
- S3 though flat but can make it hierarcy by names, but i'll get reliability and availablity for free(s3).

  For folders use a kv store DB where all the metadata of folders is stored.

<img width="904" alt="Screen Shot 2023-06-23 at 10 46 35 PM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/2634d656-4cf0-4e4c-a657-ee9ecabbcdcb">


# Design Notification System

We need to send msgs in response to some events.

Functional

System behaviour

# Design dist-queue

**High-level comp**


<img width="838" alt="Screen Shot 2023-06-25 at 1 32 36 PM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/f509fa9a-811b-46f1-a2b7-032753250e72">

Why load-balancer achiese throughput and reliability ?

<img width="894" alt="Screen Shot 2023-06-25 at 1 34 37 PM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/046c7845-a471-4724-a2d6-5f8c39ff157a">



Why Front-end service ?

<img width="1384" alt="Screen Shot 2023-06-25 at 1 31 21 PM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/3c0f7deb-04d0-49ec-b9d0-b954a86fea06">

Meta-data-service 

<img width="1320" alt="Screen Shot 2023-06-25 at 1 39 23 PM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/ec6dc75b-e553-45cf-9f4a-daa3be52d746">

Backend service ?

<img width="600" alt="Screen Shot 2023-06-25 at 1 44 08 PM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/59440fa5-7479-47df-8d53-517a6cd3cb7a">
<img width="600" alt="Screen Shot 2023-06-25 at 1 42 27 PM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/9fb19e70-ecad-477c-964a-ce9f3949a35a">


















