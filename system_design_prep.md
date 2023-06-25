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

### How to deliver data quickly ?

1. Batching => instead of making multiple http requests make a single one with multiple requests
eg: google drive batchapi, kafka, SQS

<img width="600" alt="Screen Shot 2023-06-24 at 8 55 36 PM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/ed8cdc01-b9db-4a9d-a47c-1026f4f9f368">

<img width="600" alt="Screen Shot 2023-06-24 at 8 56 34 PM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/7d2a52c4-19ba-48c9-9107-8986f66c31fe">

2. Compression

Think about compression in databases(say configure cassandra db for compression),queues etc

### How to deliver data at large scale ?



### How to protect servers from clients ?[System Overload]
<img width="600" alt="Screen Shot 2023-06-24 at 9 07 02 PM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/18763726-8afe-4ab1-b78d-0bbe5ceda313">

The manual is a bad idea, and resource estimation is also a hard problem. Autoscaling => solves unpredictable traffic spikes.
How to autoscale ?

<img width="600" alt="Screen Shot 2023-06-24 at 9 10 12 PM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/b9851c17-aa16-4254-988a-da87289470ba">

Autoscaling system design of a web-service ?

Notes: here load-balancer is also used for service-discovery=> how to know about the new servers created by the provisioning system.

<img width="700" alt="Screen Shot 2023-06-24 at 9 18 12 PM" src="https://github.com/vin136/Machine-Learning-Interview-Questions/assets/21222766/dbed84fc-d87f-4957-8582-97e7aa5638f9">






### How to protech clients from servers ?












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

# Design Discord/slack/msging

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



-------------------------

# General tools











