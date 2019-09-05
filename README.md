# autoscaling-webapp-right-way

## Preface

In this article we are going to learn some common problems and solutions of how to automatically scale the modern web applications. It aims to present a good practice/guideline designing a auto-scalable web application.

Supposed we have built an online news website, the site traffic usually starts to going up in the morning, and then goes down gradually when the time is closed to the noon. An e-commerce site usually suffers sudden burst of traffic especially on holidays like Black Friday. These heavy traffic spikes may take down our service in a short time, so we need to know when and how to scale up and down our servers.

## Auto-Scaling Checklist

* What component(s) of the application do we want to auto-scale?
* What capacity/size should each server instance have?
* How do we automate making a new server instance operational?
* How do server instances find each other (service discovery) and synchronize/collaborate?
* What is a single server instance's capacity (in terms of our monitored metrics)?
* What limit on deployment size do we have?
* Where are images and videos stored?
* How many read/write requests are recieved per seconds? Per minute?
* What is the level security required?
* Are this synchronous or ascynchronous requests?
* How is the website handling sessions?
* What are your loading balanceing considerations? (scaling, caching, session management, etc.)?

## The Old School

> A traditional auto-scaling web application are usually consisted of a some classic components. They are ***Load Balancer***, couples of of ***Auto Scalers***, and a bunch of ***Server Instances***.

### Load Balancer

The most important component of a modern web application must be the **Loaod balancer**. A load balancer serves a single point of contact for clients and distributes the workload across multiple target servers.

#### Routing

The routing policies of the load balancer can be configured with `protocol` and `port`, regarding to both the inbound and outbound traffics.

|inbound protocol   |   |   | outbound protocol  |   |
|---|---|---|---|---|
|TCP   | aa  | c  |  HTTP, HTTPS, Websocket |   |
|SSL/TLS   | b |  c |  HTTP HTTPS Websocket|   |
|UDP   |  b |  c |  HTTP HTTPS Websocket |   |
|TCP_UDP | v  |  c |  HTTP HTTPS Websocket |   |

The ports ranges from 1 to 65535

#### Routing Algorithm

* **The least connection**: Selects the service with the fewest active connections.
* **The list response time**: Selects the service with lowest average respose time.
* **Round Robin**: The most commonly used routing algorithm is [***Round-robin***](https://en.wikipedia.org/wiki/Round-robin_scheduling). After the load balancer takes the requests from clients, it fileters out the requests with rules of protocols and ports, the load balance then continuous rotates a list of target servers attached to it and forwards the requests to them.

#### Health Check

Health check is performing from by the load balancers. It routinely makes http requests to the target instances's endpoint like `/heathz` through a ping port like `80`, to examine the instance is healthy or not. If the status code is not equaled to `200`, the load balancer will unregister this instance from the sending list, until the failed instacnes goes back up again. New requests will stop sending to the unhealthy targes, instead spread to other healthy instances.

#### Session Handling

#### Security Settings

#### ACL

#### Monitoring

The most commonly used serviceis [**Prometheus**](https://prometheus.io/). The powerful time series database constantly scrapes the metrics from different exporters offered by other third-party services. With fancy browser based UI, we can easily define our Service-Level Indicator(SLI)s, monitor our web application from different perspective of views, and alert when abnormal happens.

### Auto Scalers

The auto scaler compoments mostly sit in between the load balancer and the group of our server instances. It runs some algorithms to decide when and how to scale up/down of the number of the running instances.

#### Auto Scaling Policy and Metrics

* Scaling timing: the auto-scaler first needs to decide when to perform the scaling actions. It either can proactively provision/deprovision resources ahead of the workload changes if they are predictable since the provision/deprovision process takes considerable time or can perform actions relatively when workload change has already happened.
* Observer: we can run a program intercepting the requests from the load balancer, right before they sent to those target instances. In that way we can easily count the number of the reqeusts, and set the threshold to create more server instacnes, or to shrink the number of the running instances.

#### VM/Container Provisioning

### Proejct Deployment

### Server Instacnes

Servers are the most bottom level of our web application. They can be some services, some databases, or some API endpoints serving those reqeusts come from the web clients. They are the workers who do the heavy calculations and perhaps read/write operations. Of cousrse, they are considered the basic units to perform the scaling procedures.

#### Vertical Scale

One solution we can come up with is to add more hardware resources, in terms of more processor units, more memory, or physical disks, within a single unit. In that way we have more powerful capabilities to serve the clients. This is called vertical scaling, also known as "scaling up". Vertical scaling sometimes referes to the software performance improvements, like optimizing algorithms and application code.

#### Horizontal Scale

Instead of vertical scaling, another approach is to add more units to the application's cloud architecture. This means adding more small capacity units instead of addning a single unit of larger capacity. The reqeusts for resources are then spread accross multiple units thus reducing the excess load on a single machine. This is called horizontal scaling, also called "scaling out".

## Kubernetes

> Desinging your API in microservices and adding horizontal scaling might seems like the best choice nowadays, unless your application is already running in an on-premise environment and your will need to scale it because of unexpected large spikes in web traffic. [Kubernetes](https://github.com/kubernetes/kubernetes) is one of the best choice to help you achieve this.

### HPA (k8s auto-scaler)

Kubernetes has a kind of resource called  Horizontal Pod AutoScaler. It automatically scales the pods in replication controller, deployment, and replicaset based on observed CPU utilization (or with custom metrics support, on some other application-supported metrics)

### Pods (microservices)

### Rolling Upate/Rollback

Use [Helm](https://github.com/helm/helm) to deploy your application in Kubernetes cluster. Helm suports rolling update and rollback mechanisms letting you quickly switch your application back and forth in betwween different version in case of any unexpected situation happaned, or disaster reocvery.
