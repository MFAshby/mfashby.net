---
title: "Terraform"
date: 2022-01-23T21:49:23Z
draft: true
---

I work at a company developing a web-based patient portal for users to access their medical records. Our application comprises a number of apps, some developed in-house and some third party software, working together to form our SAAS product.

We have acceptance tests for our application which require the whole stack to be started, plus a browser. Because of the number of these tests that we have, we actually start several copies of the whole application stack and use selenium grid to run the tests across them to keep the overall test duration lower.

We have been using docker-compose for starting our test environments for several years. The advantage of this approach is that it's simple: in theory, a single configuration file describes how to deploy the application stack, and a single command brings it up and tears it down again.

The problem is that it's not flexible enough by itself. In order to bring up several independent copies of the system on the same host container names must be suffixed to avoid name collisions, and the corresponding application configuration telling each application how to communicate with others must be updated. Also it's important to bring up applications in the correct order, for example databases must be started and healthy before applications which use them can start.

We have worked around these limitations with scripting: generating a docker-compose file for the multiple application stacks, generating configuration files for each application, and starting applications in the appropriate order, and waiting until they are healthy before booting their dependents. This works, but is unfortunately fragile. Dependencies between applications are not explicit: either startup order, or variables (e.g. URL where an application can be reached), and this changing the startup order is a trial and error process. The other problem with this setup is that it is very different from our production deployments, which run on kubernetes.



