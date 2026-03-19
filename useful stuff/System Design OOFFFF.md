# TOPICS
# Networks 
### SSE (Server Sent Events) vs Websockets 
Both are used when you need real time updates. Key difference is SSE s for server to client push only (like live scores or notifications), while WebSockets handle bidirectional communication where both sides send messages (like chat or live collaboration). 
SSE is simpler to implement using only standard HTTP infrastructure. 

### TCP vs UDP
TCP takes care of the ordering of packets. Guaranteed ordering but higher latency.
It is default and reliable.
UDP is connectionless. No Delivery guarantee. No ordering. Lower Latency.
We would use UDP for *mutliplayer games, video conference* where its fine if we loose some frames. 
Important: UDP is not supported by the web. 
Research more on UDP uses. 

### HTTP 
Has two things, Request and Response. 
The Request can specify the type of data it would accept like json or compressed. The server can now process it and respond either giving what it wanted or returning back saying i dont have that format, I only serve text here you go. This is the most powerful thing of HTTP, it makes it backwards and future compatible. So we can create infinite number of types of representation of data and HTTP supports them all. 

### REST API 
Representational state transfer. 
Built on top of HTTP. 
Has Verbs like, Get, Post, Put. Then you organise the API calls by URLs. 
*Note:* Whenever we use GET we would pass in the arguments in the URL itself as GET has no request body. 
### GraphQL 
![[Pasted image 20251108172724.png]]

Basically the front end can say the backend or API on what to return. 
Would be usally used in the interview when the *requirements are not fixed and might change over time.*  

### gRPCs -> Protobufs + Services 
Basically uses binary for transmission. 
Pros : High Performance 
Cons : Difficult to debug as data is passed as binary. 

We could efficiently use APIs and HTTPs to communicate between the client and the web server. And then use gRPCs to communicate between services. 
![[Pasted image 20251108175026.png]]

### SSE 
Extension on HTTP 
Enables having longer requests that can be sent to the client. 
For example, in an AI chatbot, it would obviosuly take more time for the respone to be procesesd.

### Websockets 
Bi-directional data. 
Uses TCP. 

### WebRTC
![[Pasted image 20251108194620.png]]
Peer to Peer Connections 
Used for stuff like audio or video conferencing. Even used for **collaborative document editing**. 

### Vertical Scaling vs Horizontal Scaling 
In Vertical Scaling, we just upgrade the server. So add more RAM.

In horizontal Scaling, we divide the load into different servers. 

# Microservices 
- Runs in its own process and can be deployed independently
    
- Communicates with other services via APIs (REST, gRPC, messaging, etc.)
    
- Manages its own database → avoids tight coupling
    
- Can scale and fail independently

## 3 Features of MicroServices

### Service Discovery
A **central registry** that keeps track of all available service instances and their locations. 
When a service starts it registers itself witht the registry (e.g. its IP and its port.)
When another service needs it, it queries the registry to find a live instance
Common tools: Kubernetes Service / DNS

### Data Consistency Across Services
**Problem:**  
Each service has its **own database**, which avoids tight coupling — but introduces **consistency challenges**.  
Example: `OrderService` creates an order → `PaymentService` must record payment → both must stay in sync.
You can’t use a normal SQL transaction across multiple services, so you use distributed consistency patterns.

 **Solution : Event-driven architecture**
- Each service publishes **events** (e.g. “OrderCreated”) to a message queue (Kafka, RabbitMQ, SNS/SQS).
- Other services subscribe and **react** asynchronously.
- Ensures **eventual consistency** — not all data is updated instantly, but the system converges to a consistent state.

### Fault Isolation 
**Problem:**  
If one service crashes or slows down, you don’t want it to bring down the entire system.

**Solution**
 a. **Timeouts & Retries**
- Always set request timeouts when calling another service.
- Retry failed calls — but carefully (avoid retry storms).
b. **Circuit Breaker Pattern**
- If a service keeps failing, stop sending requests temporarily.
- Example: Netflix Hystrix or Resilience4j
- Prevents cascading failures.
 c. **Bulkheads**
- Isolate resources for each service (threads, DB connections) so one service can’t exhaust them all.
d. **Graceful degradation**
- Continue with limited functionality.  
    Example: If recommendation service fails, just show basic product info.
e. **Health checks and monitoring**
- Detect unhealthy services and remove them from rotation automatically


# NoSQL vs Relational DB
No relations in NoSQL databases. But there are relations, keys, foreign keys in relational datbases. 
Relational follows strong ACID principles. So Atomicity, Consistency, Isolation and Durability. 
Relational scales vertical, whereas NoSQL scales horizontal (**NoSQL implements sharding for us, it is inbuilt.** ). 
NoSQL are typically denormalised. 
NoSQL provides **eventual consistency.** 


# Load Balancers
## Level 4 
TCP / UDP level. Increases performance. 

## Level 7 
Application Level. Can route based on API endpoints. The most default one 

# Api Gateways 
Api gateways are quite similar to load balancers but are not. They just handle authentication, and do some routing based on endpoint, so for example 
`/api/users/` -> User LB 
`api/payments` -> Pay LB 


# Redis 
Key value pair that can be used for real time leaderboards like for coding contests and stuff. 
So use **zadd** for sorted list. which can have a key which is the contest id. 

But now we have a **problem**
What if the database and the redis become inconsistent with each other? 
### Solution -> Change Data Capture
Have a message queue which is hooked up to the write ahead log of the database. 
And because the write ahead log is atomic, this solves the problem. 
So now we can have workers that can read from the message queue and send the message to Reddis. 
Common tool that implements this is Debezium. 


# CDN
Content Delivery Network. 
Mostly used for serving static content closer to the user to reduce latency.

| Type                        | Description                                                                |
| --------------------------- | -------------------------------------------------------------------------- |
| **PoP (Point of Presence)** | A geographical location that hosts one or more edge servers.               |
| **Caching**                 | Temporarily storing content closer to users.                               |
| **TTL (Time to Live)**      | How long a cached item is considered valid before re-fetching from origin. |
| **Cache Invalidation**      | Removing or refreshing outdated content from edge caches.                  |
| **Edge Server**             | Server near the user that caches and delivers content.                     |
| **Origin Server**           | The actual source of truth for your content.                               |

### How it happens 
Basically DNS lookup happens, CDN's provider Anycast DNS returns the nearest edge server based on latency and geography. 

Anycast, basically assigns the same IP to all the servers in different geographical locations and then uses DNS to magically resolve it to the nearby server.  (Using BGP which stands for Border Gateway Protocol and basically is a routing protocol which routes packets and runs the entire internet.) 
Two different types of CDN algos

**“Push CDN”** – You manually upload (push) files to CDN storage.
**“Pull CDN”** – CDN automatically fetches (pulls) from your origin when requested.

# [Book](https://learning.oreilly.com/library/view/system-design-guide/9781805124993/B20923_01.xhtml#_idParaDest-17)
## CAP Theorem 
You can only have 2 out of the 3. 
1. Consistency
2. Availability
3. Partition Tolerance -> Network Partitions. Partitions occur due to hardware failures, network outages, leading to nodes being split into isolated groups, disrupting the normal flow of comms. 
*Note:* In distributed systems, network partitions are always a thing in real world. So you can only ever in theory pick consistency or availability and not both. 


## DNS and GSLB

DNS can also perform GSLB to some extent by returning multiple IP addresses for a DNS query. Different clients will receive those IP addresses in a rotated order, distributing traffic to the different data centers in a round-robin fashion.

However, round-robin DNS load balancing has limitations. It cannot account for uneven demand from different ISPs or detect crashed servers. DNS uses short time-to-live durations for cache entries to enable more effective load balancing.

Overall, both global and local load balancing layers work together to achieve the optimal distribution of incoming traffic across a system with multiple data centers and servers.

While DNS load balancing provides some level of global load distribution, it has limitations that necessitate local load balancers within data centers:

- The small 512-byte size of DNS packets means DNS cannot include all possible server IP addresses in its responses to clients. This limits its load-balancing capabilities.
- Clients can arbitrarily select from the set of IP addresses returned by DNS, potentially choosing addresses for busy data centers. DNS has limited control over client behavior.
- DNS cannot determine the closest server for a given client to connect to, though **geolocation** and **anycasting** techniques could help. However, these are not trivial solutions.
- During failures, recovery through DNS can be slow due to caching, especially when time-to-live values are long.

To address some of these limitations, local load balancers within data centers are needed.

Client applications can connect to local load balancers using virtual IP addresses and these local load balancers distribute the incoming requests from clients to all the available servers.

In summary, while DNS load balancing provides a basic level of global load distribution, local load balancers provide more intelligent and effective load balancing within data centers. They can implement sophisticated techniques to distribute load optimally among backend servers. So, both layers – global and local load balancing – are required to achieve high performance and scalability at both the data center and global levels.

## Key Value Stores 

### Quorum 
They use n, r, w quorums. 
where n is the number of nodes, r is the number of nodes that data is read from and w is the number of nodes where we write data to 

we would need to follow the golden rule 
```
w + r > n
```

That means say for example, we have something like,
n = 3 (A, B, C)
w = 2 
r = 2 

if we are writing to A, B
and now we are reading from B,C 
so we would return the valid output. 

This is soo SMART!!
So even though they are eventually consistent underneath, they are still strongly consistent in the application layer. 

**So why can't we use NoSQL for payments?**
Even though NoSQL gives us strong consistency in the application level, it can't be used in Payments. 
As NoSQL only provides *per key strong consistency* .
In Payments we would need to obviously modify different parts, like update someone's money and then set up the transaction in the transaction table. NoSQL doesn't offer atomicity for multiple key read/writes like how Relational databases offer. We would require the *ACID* principles for payments. 
DynamoDB / Cassandra / Riak can give:

- Atomic read-modify-write on **one key**
- Strongly consistent read of **one key**
- CAS (conditional writes) on **one key**
- Quorum guarantees for **one key**

But payments involve **multiple pieces of data that must change together**, for example:

Payment example:
`- reduce user balance - create transaction record - update order status - log audit entry - update merchant balance`
That’s **5 keys**.
NoSQL cannot:
- update multiple keys atomically
- enforce cross-key constraints
- guarantee ACID transactions
- prevent partial updates across keys
- guarantee total ordering of operations
- support rollback
- support isolation levels like SERIALIZABLE

So NoSQL falls apart for multi-step financial workflows.

Quaroms and sharding 
Terminates TSL and SSL 

### 🌳 **Why Merkle Trees?**

Imagine you have **two replicas** that should contain **the same set of keys**, but because of eventual consistency, some writes may have landed on one replica and not the other.

We need a way to detect:

- which keys are **different**
    
- without sending the entire database across the network
    
- quickly, and with low bandwidth
    

This is where **Merkle Trees** come in.

---

### 🌿 **Core Idea**

A **Merkle tree** is just:

- Hash the value of each key → this becomes a _leaf node_
    
- Combine child hashes → hash them → this makes the _parent node_
    
- Continue until you get one final hash → the _root hash_
    

If **two replicas have the same root hash**, they have the same data.  
If not, we walk down the tree _branch by branch_ until we find where they differ.

---

### 🧠 **Intuition With a Simple Example**

Say your keys are:

`k1 → value A k2 → value B k3 → value C k4 → value D`

Each node hashes each value:

`H(A), H(B), H(C), H(D)`

Then pairs them:

`Left subtree  = H( H(A) + H(B) ) Right subtree = H( H(C) + H(D) )`

Then hashes again:

`Root = H( Left subtree + Right subtree )`

You store this tree **per key range**.

---

### 🔍 **Comparing Replicas**

Replica 1 sends Replica 2 the **root hash**:

- If root hashes are equal → 🟢 _their key ranges are identical_  
    → No further work needed.
    
- If root hashes differ → 🔴 _there is inconsistency_  
    → Walk down the tree:
    
1. Compare left subtree hashes
2. If they differ, compare their children    
3. Continue until hitting a leaf
4. The leaf corresponds to a _specific key_ that’s inconsistent
    

Now you synchronize **only that key**.

---

### 🚀 **Why This is Good**

Without Merkle trees, you'd have to:
- send all keys over the network, or
- compare every value
That’s too expensive.
With Merkle trees:
- You only exchange small hashes (like 32 bytes each)
    
- You never read disk unless a mismatch is discovered
    
- You synchronize _only the wrong keys_, not the whole DB
    

This makes anti-entropy (“make replicas converge”) fast and cheap.

---

### ⚠️ **Downside**

When a node **leaves or joins**, key ranges shift.

Since the tree is built _per key range_, you must:

- rebuild the Merkle tree for that range
    
- rediscover inconsistencies for that new range
    

Tree rebuilding is expensive, but this happens rarely.

---

### 🧩 **Why This Matters in Eventual Consistency**

Eventual consistency means:

- nodes **don’t guarantee immediate synchronization**
    
- replicas may diverge temporarily
    
- we need periodic **anti-entropy** (background repair)
    

Merkle trees are the mechanism used in these background repairs.

They're what keep Dynamo/Cassandra/Riak consistent in the long term **without shipping entire databases around**.

---

### 🔚 Summary (super short)

- Merkle tree = hash of hashes of hashes
    
- Root hash equal → data identical
    
- Root hash not equal → drill down until you find the changed keys
    
- Only sync the changed keys
    
- Saves massive bandwidth and disk I/O
    
- Used for background repair in eventually consistent key-value stores
    

---

If you want, I can draw you a visual tree or show exactly how Dynamo and Cassandra implement this in practice.

# PRACTICE PROBLEMS

## URL Shortener 

*Note:* Remember while performing the GET on short URL, there is **inbuilt caching**. So Redirect **301** would cache it in the user's browser. And **302** woudln't. In order to know about the link analytics it would be essential to not have caching.  

![[Pasted image 20251109205606.png]]

## Google Docs
Requriements 

Functional 
1. Users should be able to CRUD documents 
2. Multiple users should be able to access the document 
3. Updates should be visible to all the users who have document open in real time.
4. Cursor position of users.

Non Functional 
1. Millions of documents open 
2. Up to 100 concurrent users per document. 
3. Latency <= 200ms 
4. **Document to converge/ consistency** so when one user updates the local document, before it reaches to other user, the other user might have updated their own document. Problem if we send all our text over. 

![[Pasted image 20251109230915.png]]

#### Operational Transform 
*Really Important* It solves the consistency issues. Basically, lets say someone sends the message and edit ("Hello", 5) and someone else sends something like (",", 5) then operational transform can easily change the ("Hello", 5) to ("Hello", 6). Hence fixing any consistency issues. 

### Basic Workflow
So the object store stores the document. It would be an old version. We would only get the edits sent to the websocket, which would perform OT in the websocket and even the client side. Then store this edit in the database. 
So now when someone new joins we could just take the document from the object store using the CDN and then fetch the recent updates stored in the databse and apply them after performing OT in them. 
BOOM!
But now in order to solve the flushing, so the object store stores the new version. We could just ask the websocket service to spawn a job when the number of users that are accessing the document becomes 0, that would just add these stuff straight into the object store and delete these records from the database.

### Scale
The websocket services are *stateful* that means like they are not independent. Like only one of the service can have a web socket connection open at any time. 
In order to scale before the websocket, we could just add a load balancer which could use consistent hashing on the document id and point to the valid websocket service that may or may not have the document_id connection open in it. 

## Design Youtube 
![[Pasted image 20251110215254.png]]

### TCP vs UDP
TCP is what is used here. 
We would use UDP for live streaming so if we miss anything we dont care lol. 
But we would use TCP here as if we are watching a movie we would want to view every second. Like if we miss a 2s bit, the entire plot could change. 

*Note:* Videos are fetched by chunks. So around 1 minute worth of data whenever it requires. This would mean it is much more effective, and we dont need to download the entire video before playing it to the user. 
