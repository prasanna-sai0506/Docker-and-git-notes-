# System Design Fundamentals — Complete Trainer's Guide

---

## 1. Serverless vs Server-full (Traditional Servers)

### 1.1 Server-full (Traditional) Architecture
You provision, own, and manage a server (physical or VM) that runs **24/7**, whether or not it's handling requests.

**How it works:**
- You rent/buy a machine (EC2 instance, on-prem server, VPS)
- You install OS, runtime, dependencies
- Your app process stays alive continuously, listening on a port
- You are responsible for: scaling, patching, security updates, load balancing, uptime

**Characteristics:**
| Aspect | Detail |
|---|---|
| Billing | Pay for uptime, regardless of usage (idle = still paying) |
| Scaling | Manual or auto-scaling groups you configure |
| Cold starts | None — server is always warm |
| Control | Full control over OS, network, runtime versions |
| Ops burden | High — you patch OS, manage capacity, handle failover |
| Statefulness | Can easily hold in-memory state (caches, sessions) between requests |

**Examples:** EC2, DigitalOcean Droplets, on-prem data center servers, a Java/Spring Boot app running on a VM.

### 1.2 Serverless Architecture
You write **functions**, and the cloud provider runs them **only when triggered**, on infrastructure it manages entirely.

**How it works:**
- You upload a function (e.g., AWS Lambda, Google Cloud Functions, Azure Functions)
- Provider spins up a container/runtime **on-demand** when an event triggers it (HTTP request, queue message, file upload, cron)
- After execution, the container is frozen/destroyed (or kept "warm" briefly for reuse)
- You are billed **per execution + per millisecond of compute + memory used**

**Characteristics:**
| Aspect | Detail |
|---|---|
| Billing | Pay-per-use (per invocation/duration) — zero cost when idle |
| Scaling | Automatic, near-instant, scales to zero and to thousands of instances |
| Cold starts | Exist — first invocation after idle period is slower (container spin-up) |
| Control | No OS/infra control — provider abstracts it away |
| Ops burden | Very low — no patching, no server management |
| Statefulness | Stateless by design — each invocation is isolated; state must live externally (DB, cache) |
| Execution time limits | Usually capped (e.g., AWS Lambda: 15 min max) |

**Examples:** AWS Lambda, Google Cloud Functions, Azure Functions, Vercel/Netlify functions, Cloudflare Workers.

### 1.3 Side-by-Side Comparison

| Criteria | Server-full | Serverless |
|---|---|---|
| Cost model | Fixed/reserved cost | Pay-per-execution |
| Idle cost | You pay even at 0 traffic | $0 at 0 traffic |
| Scaling speed | Minutes (auto-scaling groups) | Seconds (per-request scaling) |
| Cold start | None | Yes, especially for rarely-used functions |
| Long-running tasks | Ideal | Poor fit (time limits) |
| Predictable heavy traffic | Cheaper at scale | Can get expensive at high sustained load |
| Spiky/unpredictable traffic | Wasteful (must over-provision) | Ideal — scales exactly to demand |
| Vendor lock-in | Low | Higher (tied to provider's runtime model) |
| Debugging/monitoring | Easier (persistent logs, SSH access) | Harder (distributed, ephemeral) |

### 1.4 When to Use What
- **Serverless:** APIs with unpredictable/spiky traffic, event-driven pipelines (image resize on upload), cron jobs, glue code between services, MVPs/startups wanting low ops overhead.
- **Server-full:** Sustained high-throughput systems, long-running processes (video encoding, ML training), apps needing custom OS-level config, apps needing persistent in-memory state (e.g., WebSocket servers, game servers).

**Trainer's tip:** Serverless doesn't mean "no servers" — it means "servers you don't manage." The provider still runs physical servers; the abstraction is what's "serverless."

---

## 2. Horizontal vs Vertical Scaling

Scaling = increasing a system's capacity to handle more load (users, requests, data).

### 2.1 Vertical Scaling (Scale Up)
Add **more power to the existing machine** — more CPU, RAM, faster disk (SSD/NVMe), better network card.

**Example:** Upgrading a server from 4 vCPUs/16GB RAM → 32 vCPUs/128GB RAM.

**Pros:**
- Simple — no application architecture changes needed
- No distributed-systems complexity (no data consistency issues across nodes)
- Great for stateful systems that are hard to distribute (e.g., a single relational DB instance)

**Cons:**
- **Hardware ceiling** — there's a maximum machine size you can buy
- **Single point of failure** — if this one machine dies, everything goes down
- Usually requires **downtime** to resize (reboot to apply new specs)
- Gets exponentially expensive at the high end (top-tier hardware costs disproportionately more)

### 2.2 Horizontal Scaling (Scale Out)
Add **more machines** to share the load, instead of making one machine bigger.

**Example:** Instead of 1 powerful server, run 10 medium servers behind a load balancer.

**Pros:**
- **No hard ceiling** — keep adding nodes as demand grows
- **Fault tolerant** — if one node dies, others keep serving (no single point of failure)
- Can scale **elastically** (add/remove nodes based on real-time demand)
- Cheaper commodity hardware can be used

**Cons:**
- Requires a **load balancer** to distribute traffic
- Introduces **distributed systems problems**:
  - Data consistency across nodes (if stateful)
  - Session management (where is a user's session stored?)
  - Network latency between nodes
  - Need for distributed caching/DB replication/sharding
- More complex to design, deploy, and debug

### 2.3 Comparison Table

| Criteria | Vertical Scaling | Horizontal Scaling |
|---|---|---|
| Method | Bigger machine | More machines |
| Ceiling | Limited by max hardware specs | Practically unlimited |
| Fault tolerance | Low (single node) | High (redundancy) |
| Complexity | Low | High (needs LB, distributed state) |
| Downtime to scale | Often yes | No (add nodes live) |
| Cost curve | Exponential at high end | Roughly linear |
| Best for | Databases (initially), simple apps | Web servers, microservices, stateless APIs |

### 2.4 How They Work Together in Practice
Real systems use **both**:
- Scale vertically first (cheapest/simplest) until you hit diminishing returns or a single-node ceiling
- Then scale horizontally for the stateless tiers (web/app servers)
- For databases: vertical scale the primary, then horizontally scale via **read replicas** and **sharding** for further growth

**Key enabling concepts for horizontal scaling:**
- **Load Balancer** — distributes requests across nodes (Round Robin, Least Connections, IP Hash)
- **Statelessness** — app servers shouldn't hold session data locally; store it in Redis/DB so any node can serve any request
- **Database replication** — master-replica setup for read scaling
- **Sharding/Partitioning** — splitting data across multiple DB instances by key range or hash

---

## 3. What Are Threads

### 3.1 Process vs Thread — Foundation
- A **process** is an independent running instance of a program with its own memory space (heap, code, data segments).
- A **thread** is the smallest unit of execution **within** a process. A process can have multiple threads.

**Key relationship:** Threads within the same process **share** the process's memory (heap, global variables, open files) but each thread has its **own**:
- Program counter (which instruction it's executing)
- Stack (local variables, function call frames)
- Register values

### 3.2 Why Threads Exist
Running things sequentially (one at a time) wastes CPU time when a task is waiting (e.g., waiting for disk I/O or a network response). Threads let a program do multiple things **concurrently** — e.g., one thread handles UI rendering while another downloads data.

### 3.3 Multithreading
Running multiple threads within one process simultaneously (or interleaved).

**Benefits:**
- Better CPU utilization on multi-core processors (true parallelism — different threads run on different cores)
- Responsiveness (UI doesn't freeze while background work happens)
- Efficient resource sharing (threads share memory, so no costly inter-process communication needed)

**Challenges:**
- **Race conditions** — two threads modifying shared data simultaneously, causing unpredictable results
- **Deadlocks** — two+ threads waiting on each other's locks forever
- **Thread safety** — need synchronization (locks, mutexes, semaphores) to protect shared data
- Debugging concurrency bugs is notoriously hard (non-deterministic, timing-dependent)

### 3.4 Thread Lifecycle (Java-style, but generally applicable)
1. **New** — thread object created, not yet started
2. **Runnable** — ready to run, waiting for CPU scheduling
3. **Running** — actively executing on a CPU core
4. **Blocked/Waiting** — paused, waiting for a resource/lock/signal
5. **Terminated** — finished execution

### 3.5 Threads vs Processes — Quick Comparison

| Aspect | Process | Thread |
|---|---|---|
| Memory | Separate memory space | Shares memory with parent process |
| Creation cost | Expensive (heavy) | Cheap (lightweight) |
| Communication | IPC needed (pipes, sockets) | Direct via shared memory |
| Crash impact | One process crash doesn't kill others | One thread crash can crash the whole process |
| Context switch cost | High | Lower |

### 3.6 Concurrency vs Parallelism (commonly confused)
- **Concurrency** = dealing with multiple tasks by interleaving (may run on a single core, switching rapidly)
- **Parallelism** = actually running multiple tasks **at the same time** (requires multiple cores)

### 3.7 Real-World Relevance (System Design)
- **Thread pools** (e.g., in web servers like Tomcat) — a fixed set of worker threads handle incoming requests instead of spawning a new thread per request (avoids overhead)
- **Async/non-blocking I/O** (Node.js event loop, Java NIO) — alternative to threads-per-request; one thread handles many concurrent connections by not blocking on I/O
- This directly relates to **how many concurrent requests a single server can handle**, which ties back into your horizontal/vertical scaling decisions

---

## 4. What Are Pages (Memory Paging)

### 4.1 The Core Problem
A computer has limited **physical RAM**, but programs often need (or the OS wants to pretend they have) more memory than physically exists, and multiple programs need to run simultaneously without stepping on each other's memory.

### 4.2 Virtual Memory
The OS gives each process the illusion of having its own large, continuous, private address space (**virtual memory**), separate from the actual physical RAM layout. This provides:
- **Isolation** — one process can't accidentally read/write another's memory
- **Abstraction** — programs don't need to know physical memory addresses
- **Overcommitment** — total virtual memory across processes can exceed physical RAM

### 4.3 What Is a "Page"?
To implement virtual memory, the OS divides:
- **Virtual memory** into fixed-size blocks called **pages** (commonly 4KB each)
- **Physical memory (RAM)** into same-size blocks called **page frames**

A **Page Table** (maintained per-process by the OS) maps virtual pages → physical page frames.

**Analogy:** Think of a book's index. The book (virtual memory) has chapter references (virtual addresses), and the index (page table) tells you the actual physical page number where that content lives.

### 4.4 How Paging Works Step-by-Step
1. A program references a virtual memory address
2. The CPU's **MMU (Memory Management Unit)** looks up the page table to translate virtual page → physical frame
3. If the page is in RAM → direct access (fast)
4. If the page is **not in RAM** → **Page Fault** occurs:
   - OS pauses the process
   - OS finds the page on disk (in the swap space/page file)
   - OS loads it into a free RAM frame (or evicts an existing page first if RAM is full)
   - Page table is updated
   - Process resumes

### 4.5 Page Replacement (When RAM Is Full)
When RAM is full and a new page must be loaded, the OS must evict something. Common algorithms:
- **FIFO** — evict the oldest loaded page
- **LRU (Least Recently Used)** — evict the page not used for the longest time (most common/practical)
- **Optimal** — evict the page that won't be needed for the longest future time (theoretical, used for comparison only)

### 4.6 Thrashing
If a system has too little RAM relative to what active processes need, it constantly page-faults and swaps — spending more time swapping pages in/out than doing actual work. Performance collapses. This is called **thrashing**.

### 4.7 Why Pages Matter in System Design
- Explains why **adding RAM (vertical scaling)** improves performance — fewer page faults, less disk swapping
- Underpins how **containers/VMs** isolate memory between tenants on the same physical host
- Relevant to understanding **cache-friendly programming** — accessing memory in patterns that align with page boundaries reduces page faults and improves performance
- Databases use similar paging concepts internally (e.g., B-Tree index pages, buffer pools) — same core idea, applied to disk-based data storage

---

## 5. How Does the Internet Work

### 5.1 The Big Picture
The internet is a **global network of networks** — millions of independently-operated networks (ISPs, universities, companies, data centers) interconnected and agreeing to speak the same set of protocols so any device can talk to any other device.

### 5.2 Step-by-Step: What Happens When You Visit a Website

**Step 1 — You type a URL** (e.g., `https://example.com`)

**Step 2 — DNS Resolution (Domain Name System)**
- Domain names (example.com) are human-friendly, but computers route using **IP addresses** (e.g., 93.184.216.34)
- Your browser asks a **DNS resolver** to translate the domain into an IP address
- Lookup chain: Browser cache → OS cache → Router → **ISP's DNS resolver** → if not cached, it queries:
  1. **Root DNS servers** (know where to find Top-Level-Domain servers, e.g., `.com`)
  2. **TLD DNS servers** (know where to find the domain's authoritative server)
  3. **Authoritative DNS server** for example.com (returns the actual IP)
- The IP is cached at various levels (TTL-based) to avoid repeating this every time

**Step 3 — Establishing a Connection (TCP/IP)**
- Your device and the server perform a **TCP three-way handshake**:
  1. Client → Server: SYN (synchronize)
  2. Server → Client: SYN-ACK
  3. Client → Server: ACK
- This establishes a reliable, ordered connection before any data is sent

**Step 4 — TLS Handshake (if HTTPS)**
- Client and server negotiate encryption: exchange certificates, agree on a cipher, establish a shared secret key
- This ensures data is encrypted end-to-end (confidentiality + integrity + authentication of the server)

**Step 5 — HTTP Request**
- Browser sends an **HTTP(S) request**: method (GET/POST/etc.), headers, and body if applicable
- This request travels as data broken into **packets**

**Step 6 — Routing Through the Network**
- Your request doesn't travel directly — it hops through many intermediate devices:
  - Your **router** → your **ISP** → through multiple **backbone routers** (using **BGP — Border Gateway Protocol** to decide the best path across networks) → eventually reaches the destination network → the target server
- Each hop uses **IP addresses** to decide where to forward the packet next (like postal routing)
- Packets may take **different paths** and arrive out of order — TCP reorders them at the destination

**Step 7 — Server Processes the Request**
- Request may hit a **load balancer** first, which routes it to one of many backend servers
- The server (web server → app server → database, etc.) processes the request and prepares a response

**Step 8 — Response Sent Back**
- Server sends back an HTTP response (status code, headers, body — e.g., HTML/JSON)
- Travels back through the same layered process (packets → routers → your ISP → your device)

**Step 9 — Browser Renders the Page**
- Browser parses HTML, fetches additional resources (CSS, JS, images) — each may repeat steps 2–8
- Renders the final page on your screen

### 5.3 The Layered Model (Why This Works — OSI/TCP-IP Model)
Communication is broken into layers, each responsible for one concern:

| Layer | Responsibility | Examples |
|---|---|---|
| Application | User-facing protocols | HTTP, HTTPS, FTP, DNS |
| Transport | Reliable delivery, ordering | TCP (reliable), UDP (fast, no guarantee) |
| Network | Addressing & routing | IP, routers, BGP |
| Data Link | Local network delivery | Ethernet, Wi-Fi, MAC addresses |
| Physical | Actual signal transmission | Cables, radio waves, fiber optics |

Each layer only needs to know how to talk to the layer directly above/below it — this is why the internet works across wildly different hardware (fiber, satellite, Wi-Fi) seamlessly.

### 5.4 Key Supporting Concepts
- **IP Address** — unique numerical identifier for a device on a network (IPv4: 32-bit, e.g., 192.168.1.1; IPv6: 128-bit, for the much larger address space needed today)
- **Ports** — identify which application/service on a device should receive the data (e.g., port 443 for HTTPS, port 80 for HTTP)
- **Packets** — data is broken into small chunks for transmission; each has header info (source/destination IP, sequence number) and reassembled at the destination
- **CDN (Content Delivery Network)** — caches content at edge servers geographically close to users, reducing latency (e.g., Cloudflare, Akamai)
- **NAT (Network Address Translation)** — lets many devices on a private network (home Wi-Fi) share one public IP

### 5.5 Why This Matters for System Design
- Understanding DNS + load balancers explains how traffic gets distributed across your horizontally-scaled servers
- Understanding TCP/TLS handshake costs explains why **connection pooling** and **keep-alive** connections matter for performance
- Understanding packet routing/latency explains why **CDNs** and **regional deployments** improve user experience
- This is the foundation before you go deeper into API design, load balancing algorithms, and caching strategies

---

## Quick Recap Table (All 5 Topics)

| Topic | Core Idea | Real-World Tie-In |
|---|---|---|
| Serverless vs Server-full | Who manages infra & how you're billed | Choosing compute model for your architecture |
| Horizontal vs Vertical Scaling | Bigger machine vs more machines | Foundation of all scalability design |
| Threads | Concurrent execution units within a process | How servers handle many requests at once |
| Pages | Fixed-size memory blocks for virtual memory | Why RAM/vertical scaling improves performance |
| How the Internet Works | DNS → TCP → routing → HTTP → rendering | The plumbing beneath every system you design |

**Suggested next topics to go deeper:** Load balancing algorithms, caching (CDN/Redis), database indexing & sharding, CAP theorem, message queues, microservices vs monoliths.
