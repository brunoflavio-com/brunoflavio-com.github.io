---
layout: post
title:  "Designing Distributed Systems - The ambassador pattern"
date:   2019-02-02 22:30:00 +0100
categories: blog microservices docker
---

This post is related to the book  “Designing Distributed Systems by Brendan Burns (O’Reilly). Copyright 2018 Brendan Burns, 978-1-491-98364-5.” (see initial post and motivation on [this series here]({% post_url 2019-01-29-microservices-sidecar-pattern %})).

The book is freely available on the [Microsoft Azure resources website](https://azure.microsoft.com/en-us/resources/designing-distributed-systems/)

While the sidecar pattern was easy to handle with docker-compose, this one would look better on a Kubernetes cluster in order to benefit from pods. Nevertheless, let's see an example of **the ambassador** pattern.


![Sidecar](/assets/img/ambassador.png)


# If I can speak to the source, why would I talk to an Ambassador instead?

Your service doesn't need to know **everything** about the environment it runs on. Nevertheless, your service has dependencies on other services, databases, caches, amonst others. What if you had available an ambassador, on a convenient location, and offload into it the knowledge about your dependent service's topology, distribution, sharding, etc?

Wait, isn't that the same as proxying or load balancing? It almost is, with the difference that this is a custom and co-located proxy with the **client**.


