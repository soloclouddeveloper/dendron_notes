---
id: ud14zad5nf0htgw1opsfedz
title: Professional Cloud Developer
desc: ''
updated: 1742384167934
created: 1741345686998
---

## Study notes for GCP Professional Cloud Developer

[Official Exam Guide](https://services.google.com/fh/files/misc/professional_cloud_developer_exam_guide_english.pdf) effective after Mar 12, 2025

This version includes Google’s AI tools (Gemini Code Assist and Cloud Assist),
and includes a new objective, 4.3, that represents troubleshooting and
observability tasks.

---

Certification exam guide (effective March 12, 2025)

A Professional Cloud Developer builds and deploys scalable, secure, and highly available applications by using Google-recommended tools and best practices. This individual has experience with cloud-native applications, Google Cloud APIs, developer and AI tools, managed services, orchestration tools, serverless platforms, containerized applications, test and deployment strategies, problem determination and resolution, and datastores. This individual also has proficiency with at least one general-purpose programming language and can instrument their code to produce metrics, logs, and traces.

## Section 1: Designing highly scalable, available, and reliable cloud-native applications (~36% of the exam)

---

### **1.1 Designing high-performing applications and APIs. Considerations include:**

- Choosing the appropriate platform based on the use case and requirements (e.g., Compute Engine, GKE, Cloud Run)

- Building, refactoring, and deploying application containers to Cloud Run and GKE
- Understanding how Google Cloud services are geographically distributed (e.g., latency, regional services, zonal services)

- Configuring load balancers and applications for session affinity and performant content delivery
  - [Session Affinity](https://cloud.google.com/load-balancing/docs/backend-service#session_affinity)
    - > Session affinity lets you control how the load balancer selects backends for new connections in a predictable way as long as the number of healthy backends remains constant.
    - Useful for stateful applications that require the same backend for the same user.
    - Best-effort basis (based on):
      - backend health
      - adding/removing backends
      - weights
    - Best used when large distibution of unique connections    
    - By default, GCP LBs determine BE based on 5 parameters
    - Packet's source IP 
    - Packet's source port (if in packet header)
    - Packet's desitnation IP 
    - Packet's destination port (if in packet header)
    - Packet's protocol
  
    - **Types of affinity**
      - Client IP, no destination affinity
        - single tuple hash
        - available only for internal passthrough Network LBs 
      - Client IP 
        - two tuple hash created from **source** IP and **destination** IP
        - Available to all GCP LB with BEs
      - Generated Cookie 
        - > the load balancer includes an HTTP cookie in the Set-Cookie header in response to the initial HTTP request.
        - name of cookie depends on type of LB
        - > he load balancer directs those requests to the same backend instance or endpoint, as long as the session affinity cookie remains valid
      - [Header field](https://cloud.google.com/load-balancing/docs/backend-service#header_field_affinity)
        - > With header field affinity, requests are routed to the backends based on the value of the HTTP header in the consistentHash.httpHeaderName field of the backend service. 
        - See documentation for types of LBs available
      - [HTTP Cookie affinity](https://cloud.google.com/load-balancing/docs/backend-service#http_cookie_affinity)
        - > When you use HTTP cookie-based affinity, the load balancer includes an HTTP cookie in the Set-Cookie header in response to the initial HTTP request. You specify the name, path, and time to live (TTL) for the cookie
        - Available LBs:
          - All application LBs
          - Cloud Service Mesh
      - [Stateful cookie-based affinity](https://cloud.google.com/load-balancing/docs/backend-service#stateful-session-affinity)
        - > When you use stateful cookie-based affinity, the load balancer includes an HTTP cookie in the Set-Cookie header in response to the initial HTTP request. You specify the name, path, and time to live (TTL) for the cookie.
        - Available LBs:
          - All application LBs, **except** classic Application LB
          - As long as cookie is valid and BE is available the LB will direct to specific BE.
        - **Differences** to other session affinity LBs:
          - > Stateful cookie-based affinity has no specific requirements for the balancing mode or for the load balancing locality policy (localityLbPolicy)
          - > Stateful cookie-based affinity is not affected when autoscaling adds a new instance to a managed instance group
          - > Stateful cookie-based affinity is not affected when autoscaling removes an instance from a managed instance group unless the selected instance is removed
          - > Stateful cookie-based affinity is not affected when autohealing removes an instance from a managed instance group unless the selected instance is removed
        - **Zero TTL Cookie Affinity**
          - LB does not assign an `Expire` attribute
          - Different browsers treat Zero TTL differently                                                                    

- Implementing caching solutions (e.g., Memorystore)
  - [Valkey](https://cloud.google.com/memorystore/docs/valkey/product-overview)
  - [Redis Cluster](https://cloud.google.com/memorystore/docs/cluster/memorystore-for-redis-cluster-overview)
  - [Redis](https://cloud.google.com/memorystore/docs/redis/memorystore-for-redis-overview)
    - secured via private IP address, same authorized VPC, IAM roles
  - [Memcached](https://cloud.google.com/memorystore/docs/memcached/memcached-overview)
    - binary and ASCII protocol compliant
    - authorized VPC defined during setup and cannot be altered after deployment
    - auto discovery of nodes programmatically
  - > Memorystore for Valkey and Redis Cluster scale without downtime to support up to 250 nodes, terabytes of keyspace, and 60x more throughput than Memorystore for Redis with microsecond latencies
  - > Memorystore for Valkey and Redis Cluster have zero-downtime scaling, automatically distributed replicas across availability zones, and automated failover. Memorystore for Redis Cluster also offers a 99.99% SLA
  - > Session management, frequently accessed queries, scripts, and pages are common examples of caching

-  **Creating and deploying APIs (e.g., HTTP REST, gRPC [Google Remote Procedure Call])**
    - [Bridge The Gap Between gRPC And REST HTTP APIs](https://cloud.google.com/blog/products/api-management/bridge-the-gap-between-grpc-and-rest-http-apis)
      - efficient serialization
      - low latency
      - strong typed messages
    - [gRPC Overview](https://cloud.google.com/api-gateway/docs/grpc-overview)
      - > In gRPC, a client application can directly call methods on a server application on a different machine as if it was a local object
     

- **Using application rate limiting, authentication, and observability (e.g., Apigee, Cloud API Gateway)**
  - [Choosing Between Apigee API, Cloud API Gateway and Cloud Endpoints](https://cloud.google.com/blog/products/application-modernization/choosing-between-apigee-api-gateway-and-cloud-endpoints)
  - [Apigee](https://cloud.google.com/apigee)
    - Enterprise grade 
    - Any environment, use case or scale 
    - High API volumes 
    - APIs exposed to 3rd parties 
    - High reliability
  - [Cloud API Gateway ](https://cloud.google.com/api-gateway/docs)
    - Fully managed service 
    - Ideal for cloud native apps using GCP services 
    - Inexpesive to get started. 
  - [Cloud Endpoints](https://cloud.google.com/endpoints/docs)
    - Requires users to manage every detail of API operations.
    - Configure, deploy and manage [ESPv2 Proxy](https://cloud.google.com/endpoints/docs/openapi/glossary#extensible_service_proxy_v2)
    - Most common use case is with gRPC services.
    - 3 Options depending on where API is hosted:
      - OpenAPI 
      - gRPC 
      - App Engine standard enviornment

- **Integrating applications using asynchronous or event-driven approaches (e.g., Eventarc, Pub/Sub)**
  - ChatGPT response for [[pro_cloud_dev.asynch_event_driven]] approaches
    - [Eventarc Documentation](https://cloud.google.com/eventarc/docs)
      - Two types:
        - [Eventarc Standard](https://cloud.google.com/eventarc/standard/docs/overview)
          - Format
            - HTTP requests in binary content mode
            - Payload encoding
              - `application/json`
              - `application/protobuf`
        - [Eventarc Advanced](https://cloud.google.com/eventarc/advanced/docs/overview)
          - [Retry Policy](https://cloud.google.com/eventarc/advanced/docs/overview#event-retry-policy)
            - Default 24 hours with exponential backoff
          - Format 
            - HTTP requests in binary content mode
            - Workflow destinations converts event to JSON object
        - [Features Comparison Chart](https://cloud.google.com/eventarc/docs#features-comparison-table)
      - [Event Definition](https://cloud.google.com/eventarc/advanced/docs/overview#events):
        - > An event is a data record that expresses an occurrence and its context, and indicates a change in a resource or environment. An event is a discrete unit of communication, independent of other events. For example, an event might indicate a change to data in a database, a file added to a storage system, or a scheduled job.
      - Observability
        - detailed logs available in Cloud Auditing Logs

- **Optimizing for cost and resource usage**
  - [Well Architected Framework: Cost Optimization Pillar](https://cloud.google.com/architecture/framework/cost-optimization)

- **Understanding data replication to support zonal and regional failover models**
  - References - [Distaster Recovery](https://cloud.google.com/architecture/disaster-recovery)
  - Zonal Resources hosted in a single zone
    - Zonal resources:
      - Compute Engine (virtual machines)
      - Persistent disks
  - Regional Resources hosted in multi-zones in the same region
  - Multi-regional hosted across multiple regions.

- **Using traffic splitting strategies (e.g., gradual rollouts, rollbacks, A/B testing) on a new service on Cloud Run or GKE**

- **Orchestrating application services with Workflows, Eventarc, Cloud Tasks, and Cloud Scheduler**
  - Reference Dendron page: [[pro_cloud_dev.orchestrating_app_services]]

### **1.2 Designing secure applications. Considerations include:**

- **Implementing data retention and organization policies (e.g., Cloud Storage Object Lifecycle Management, Cloud Storage use and lock retention policies)**
  - [Object Lifecycle Management](https://cloud.google.com/storage/docs/lifecycle)
    - Lifecycle config
      - a rule contains one action and multiple conditions
      - if multiple rules contain same action, action is applied if any of the conditions are met.
      - if multiple rules have conditions met at same time, then the order is:
        - `delete` takes precedence over `SetStorageClass` action
        - The lowest price of "at rest storage" pricing is used.
    - Lifecycle Actions
      - rule can contain only a single action
      - `Delete`
        - default is soft delete which GCP will retain for 7 days
      - `SetStorageClass`
      - `AbortIncompleteMultipartUpload`
    - [Lifecycle Conditions](https://cloud.google.com/storage/docs/lifecycle#conditions)
      - All conditions are optional but at least one condition is required.
    - > The object's time spent set at the original storage class counts towards any minimum storage duration that applies for the new storage class.

- Using security mechanisms that identify vulnerabilities and protect services and resources (e.g., Identity-Aware Proxy [IAP], Web Security Scanner)
- Responding to and resolving vulnerabilities, including those identified by Artifact Analysis and Security Command Center
- Storing, accessing, and rotating application secrets, credentials, and encryption keys (e.g., Secret Manager, Cloud Key Management Service, Workload Identity Federation)
- Authenticating to Google Cloud services (e.g., Application Default Credentials, JSON Web Token [JWT], OAuth 2.0, Cloud SQL Auth Proxy, AlloyDB Auth Proxy)
- Managing and authenticating end-user accounts (e.g., Identity Platform)
- Securing cloud resources using Identity and Access Management (IAM) roles for service accounts
- Securing service-to-service communications (e.g., Cloud Service Mesh, Kubernetes Network Policies)
- Running services with least privileged access
- Securing application artifacts using Binary Authorization

### **1.3 Storing and accessing data. Considerations include:**
  -  Selecting the appropriate storage system based on the volume of data and performance requirements
      - Cloud Storage
        - Infinite scale, unlimited amount of objects
        - Max Object size = 5 TB
  -  Designing appropriate schemas for structured databases (e.g., AlloyDB, Spanner) and unstructured databases (e.g., Bigtable, Datastore)
  -  Understanding the implications of eventual and strongly consistent replication of AlloyDB, Bigtable, Cloud SQL, Spanner, and Cloud Storage
  -  Creating signed URLs to grant access to Cloud Storage objects
  -  Writing data to BigQuery for analytics and AI/ML workloads
  
---

## Section 2: Building and testing applications (~23% of the exam)

### **2.1 Setting up your development environment. Considerations include:**
  -  Emulating Google Cloud services using the Google Cloud CLI for local application development and local unit testing
    - [Ref Google Official Doc](https://cloud.google.com/sdk/gcloud/reference/beta/emulators)
    - `glcoud beta emulators GROUP`
        - Possible GROUP:
            - bigtable
            - datastore
            - firestore
            - pubsub
            - spanner
    - [Using Emulator](https://cloud.google.com/pubsub/docs/emulator#using_the_emulator)
        - App must be built using Cloud Client Libraries
        - **DOES NOT** support `gcloud pubsub` 
            - What about bigtable, datastore, etc??
  -  Using the Google Cloud console, Cloud SDK, Cloud Code, Gemini Cloud Assist, Gemini Code Assist, Cloud Shell, and Cloud Workstations

### **2.2 Building. Considerations include:**

  -  Using Cloud Build and Artifact Registry to build and store containers from source code
  -  Configuring provenance in Cloud Build (e.g., Binary Authorization)

### **2.3 Testing. Considerations include:**
  -  Writing unit tests with the help of Gemini Code Assist
  -  Executing automated integration tests in Cloud Build

---

## Section 3: Deploying applications (~20% of the exam)

### **3.1 Deploying applications to Cloud Run. Considerations include:**
  -  Deploying applications from source code
  -  Invoking Cloud Run services using triggers (e.g., Eventarc, Pub/Sub)
  -  Configuring event receivers (e.g., Eventarc, Pub/Sub)
  -  Exposing and securing APIs in applications (e.g., Apigee)
  -  Deploying a new API version in Cloud Endpoints considering backward compatibility

### **3.2 Deploying containers to GKE. Considerations include:**
  -  Deploying containerized applications
  -  Defining resource requirements for container workloads
  -  Implementing Kubernetes health checks to increase application availability
      - Readiness probe:  Is the application **ready** to receive traffic?  No - don't send traffic
      - Liveliness probe:  Is the pod responding?  No - restart the pod
  -  Configuring the Horizontal Pod Autoscaler for cost optimization
      - 1 New instance at a time, no reduction in capacity:
        - maxSurge: 1 | maxUnavailable: 0
      - Minimum costs, 1 at a time (no increase in number of instances)
        - maxSurge: 0 | maxUnavailable: 1

  - Misc Notes:
    - Default deployment strategy: Rolling update
    - Rolling updates: 
      - Update pods in batches
    - Recreate:
      - Kill all existing pods before new ones are created
    - Network Policy
      - Kubernetes network policies do not configure authentication or authorization, only which pods can communicate with other pods within same namespace.

---

## Section 4: Integrating applications with Google Cloud services (~21% of the exam)

### **4.1 Integrating applications with data and storage services. Considerations include:**
  -  Managing connections to various Google Cloud datastores (e.g., Cloud SQL, Firestore, Cloud Storage)
  -  Reading and writing data to and from various Google Cloud datastores
  -  Writing applications that publish and consume data using Pub/Sub

### **4.2 Consuming Google Cloud APIs. Considerations include:**
  -  Enabling Google Cloud services
  -  Making API calls by using supported options (e.g., Cloud Client Libraries, REST
API, gRPC, API Explorer) taking into consideration:
  -  Batching requests
  -  Restricting return data
  -  Paginating results
  -  Caching results
  -  Handling errors (e.g., exponential backoff)
  -  Using service accounts to make Cloud API calls

### **4.3 Troubleshooting and observability. Considerations include:**
  -  Instrumenting code to facilitate troubleshooting using metrics, logs, and traces
in Google Cloud Observability
  -  Identifying and resolving issues using Google Cloud Observability
  -  Managing application issues using Error Reporting
  -  Using trace IDs to correlate trace spans across services
  -  Using Gemini Cloud Assist