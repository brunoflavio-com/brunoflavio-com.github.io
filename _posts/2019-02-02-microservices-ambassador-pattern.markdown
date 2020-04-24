---
layout: post
title:  "Designing Distributed Systems - The ambassador pattern"
date:   2019-02-02 22:30:00 +0100
categories: blog microservices docker
---

This post is related to the book  “Designing Distributed Systems by Brendan Burns (O’Reilly). Copyright 2018 Brendan Burns, 978-1-491-98364-5.” (see initial post and motivation on [this series here]({% post_url 2019-01-29-microservices-sidecar-pattern %})).

The book is freely available on the [Microsoft Azure resources website](https://azure.microsoft.com/en-us/resources/designing-distributed-systems/)

While the sidecar pattern was easy to handle with docker-compose, this one would look better on a Kubernetes cluster in order to benefit from pods. Nevertheless, let's see an example of **the ambassador** pattern.


![Ambassador](/assets/img/ambassador.png)


# If I can speak to the source, why would I talk to an Ambassador instead?

Your service doesn't need to know **everything** about the environment it runs on. Nevertheless, your service has dependencies on other services, databases, caches, amonst others. What if you had available an ambassador, on a convenient location, and offload into it the knowledge about your dependent service's topology, distribution, sharding, etc?

Wait, isn't that the same as proxying or load balancing? It almost is, with the difference that this is a custom and co-located proxy with the **client**. This means that you're actually augmenting the capabilities of your clients without touching their core functionality.

# How can I get an ambassador up and running?

## Service topology

When deploying an ambassador you'll likely consider whether your ambassador should handle multiple clients or instead be deployed together with each client that needs it.

On the later case where the client gets it's own and exclusive ambassador then you'd deploy it as a [sidecar]({% post_url 2019-01-29-microservices-sidecar-pattern %}).

If on the other hand you want to share the ambassador for multiple clients then you'd need to work out how granular you want to be: Do I want an ambassador per machine serving the multple services running there? Then you'd probably setup a daemon running locally. You may want to configure the ambassador per service and deploy an ambassador, or a cluster of them, for each service.

