# autoscaling-webapp-right-way

## Preface

In this article we are going to learn some common solutions of how to automatically scale a modern web application. It aims to present a good practice/guideline to designing an auto-scalable web application.

## How does auto-scaling work

Essentially, auto-scaling works in two steps. First, it determines if the current resource availibity matches the resource demand. Second, it adjusts the availibility accordingly. For example, says we have built a news website, in the morning users causes a demand for 5 servers, but you only have 3 available. The auto-scaler should provision two more for you to meet the demand. Likewise, when users leave during the night, the auto-scaler should shrink the deployment to 1 or 2 servers to save money. To achieve this task, the auto-scaler requires monitoring information, which should be up to date and offer useful information about the status your cloud application. A good metric to monitor is one that relates to a single layer of your application. For instance, recording how many users want to access your service via a load balancer would be good. Monitoring CPU usage of a single unit, however, may be a bad idea since in case of under-provisioning, it does not shows how many additional resources is needed, only that there is a problem. A simple auto-scaler works in reactive fashion: resource availability is modified as the reaction when the threshold value has been passed.
The typical reaction would be like:

* Add `X` servers
* wait until they have booted completely, and
* investigate if the resource availability matches demands, repeating as needed.

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

#### Routing configuration

The load balancer can be designed to route the requests to its targets using `protocol` and `port numbers`, regarding to both the inbound and outbound traffics.

|Inbound Protocol|Outbound Protocol|Health Check Protocol|
|---|---|---|
|TCP|TCP|HTTP/HTTPS/TCP|
|SSL/TLS|TCP/TLS| HTTP/HTTPS/TCP|  
|UDP|UDP|HTTP/HTTPS/TCP|

The ports ranges from 1 to 65535

#### Routing Algorithm

* **The least connection**: Selects the service with the fewest active connections.
* **The least response time**: Selects the service with lowest average respose time.
* **Round Robin**: The most commonly used routing algorithm is [***Round-robin***](https://en.wikipedia.org/wiki/Round-robin_scheduling). After the load balancer takes the requests from clients, filetered with rules of protocols and port numbers, the load balancer then forwards the requests to a list of target servers attached to it in rotations.

#### Health Check

Health check is performed by the load balancers. It routinely generates http requests to the target instances's endpoint like `/heathz`, through a ping port, for eample, `80`, to examine the instance is healthy or not. If the status code is not equaled to `200`, the load balancer will unregister this instance from the sending list, until the failed instacnes goes back up again. New requests will stop sending to the unhealthy targes, instead spread to other healthy instances.

#### Session Handling

* Sticky
* Non-sticky

#### Security Settings

#### ACL

### Auto Scaler

The auto scaler compoments are mostly sit in between the load balancer and groups of server instances. They monitor the workloads and runs some algorithms to decide when and how to scale up/down of the number of the running instances.

#### Auto Scaling Policy and Metrics

Run a program intercepts the requests from the load balancer, right before they are sent to those target instances. In that way we can easily observe the number of the read/writes reqeusts per seconds(minutes), then set the threshold to increase or to reduce the number of server instances.

* Scaling timing: the auto-scaler first needs to decide when to perform the scaling actions. It either can proactively provision/deprovision resources ahead of the workload changes if they are predictable since the provision/deprovision process takes considerable time or can perform actions relatively when workload change has already happened.
* Workload prediction:
* Adaptivity to change:
* Oscillation Mitigation:

#### Monitoring

* Low-level indicator:
* High-level indicator:
* Hybrid:

#### VM/Container Provisioning

#### Proejct Deployment

To deploy the new version of the products without the downtime is an art.

### Server Instacnes

Servers are the most bottom level of our web application. They can be some services, some databases, or some API endpoints serving those reqeusts come from the web clients. They are the workers who do the heavy calculations and perhaps read/write operations. Of cousrse, they are considered the basic units to perform the scaling procedures.

#### Vertical Scale

One solution we can come up with is to add more hardware resources, in terms of more processor units, more memory, or physical disks, within a single unit. In that way we have more powerful capabilities to serve the clients. This is called vertical scaling, also known as "scaling up". Vertical scaling sometimes referes to the software performance improvements, like optimizing algorithms and application code.

#### Horizontal Scale

Instead of vertical scaling, another approach is to add more units to the application's cloud architecture. This means adding more small capacity units instead of addning a single unit of larger capacity. The reqeusts for resources are then spread accross multiple units thus reducing the excess load on a single machine. This is called horizontal scaling, also called "scaling out".

## Kubernetes

> Desinging your API in microservices and adding horizontal scaling might seems like the best choice nowadays, unless your application is already running in an on-premise environment and your will need to scale it because of unexpected large spikes in web traffic. [Kubernetes](https://github.com/kubernetes/kubernetes) is one of the best choice to help you achieve this.

### HPA (k8s auto-scaler)

Kubernetes has a kind of resource called  Horizontal Pod AutoScaler. It automatically scales the pods in replication controller, deployment, and replicaset based on observed **CPU/Memory utilization** (or with custom metrics support, on some other application-supported metrics)

### Pods (microservices)

#### Rolling Upate/Rollback

Use [Helm](https://github.com/helm/helm) to deploy your application in Kubernetes cluster. Helm suports rolling update and rollback mechanisms letting you quickly switch your application back and forth in betwween different versions. In case of any testing scenarios or disaster reocvery, you can rollback your application in a short time.

<!-- ### Monitoring

Regarding to application level's monitoring. the most commonly used service is [**Prometheus**](https://prometheus.io/). The powerful time series database constantly scrapes the metrics from different exporters offered by other third-party services. With fancy browser based UI, we can easily define our Service-Level Indicator(SLI)s, monitor our web application from different perspective of views, and alert when abnormal happens. -->
