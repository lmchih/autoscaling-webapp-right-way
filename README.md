# autoscaling-webapp-right-way

This article we are going to learn some common problems and solutions of how to auto scale the modern web applications. It aims to present a good practice/guideline designing a auto-scalable web application.

Supposed we have built an online news website, the site traffic usually starts to going up in the morning, and then goes down gradually when the time is closed to the noon. An e-commerce site usually suffers sudden burst of traffic especially on holidays like Black Friday. These heavy traffic spikes may take down our service in a short time, so we need to know when and how to scale up and down our servers.

## Auto-Scaling Checklist

* What component(s) of the application do we want to auto-scale?
* What capacity/size should each server instance have?
* How do we automate making a new server instance operational?
* How do server instances find each other (service discovery) and synchronize/collaborate?
* What is a single server instance's capacity (in terms of our monitored metrics)?
* What limit on deployment size do we have?

## Vertical Scale

There are some terms we need to clarify first. One solution we can come up with is to add more hardware resources, in terms of more processor units, more memory, or physical disks, to a single unit. In that way we have more powerful servers to serve the clients. This is called vertical scaling.

## Horizontal Scale

Instead of vertical scaling, another approach is to horizontally add or remove instances with the same hardware specification. This is called horizontal scaling.

## Load Balancer

## Routing

## Session Handling


## Health Check

## Monitoring

## Security Settings

## ACL

## VM/Container Provisioning

## Auto Scaling Policy and Metrics

* Scaling timing: the auto-scaler first needs to decide when to perform the scaling actions. It either can proactively provision/deprovision resources ahead of the workload changes if they are predictable since the provision/deprovision process takes considerable time or can perform actions relatively when workload change has already happened.

## Proejct Deployment

## Rolling Upate

## Rollback

## Kubernetes

## HPA
