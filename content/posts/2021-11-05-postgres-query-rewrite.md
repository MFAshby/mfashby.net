---
title: "Postgres Query Rewrite"
date: 2021-11-05T11:57:12Z
draft: false
---

Recently at work, I needed to be able to rewrite some [postgresql](https://www.postgresql.org/) queries which were coming from a closed-source application in order to integrate the app into our own system testing setup. Specifically I needed to replace instances of `current_timestamp` with invocation of `now()` function.

I found a couple of options to do this: 
1. [pgbouncer-rr](https://github.com/awslabs/pgbouncer-rr-patch) is a _patch_ for [pgbouncer](https://www.pgbouncer.org/), initially written by AWS labs, which extends pgbouncer to include rewrite and rerouting capability.
2. [pg_query_rewrite](https://github.com/pierreforstmann/pg_query_rewrite) is an _extension_ for postgres, which supports substitution of an entire matching query with another one.

Unfortunately I hit some roadblocks with each. `pg_query_rewrite` simply wasn't flexible enough. I needed to do a string substitution on the query, not entire query replacement as I didn't have the exact set of every query the application would issue. Also providers of managed postgresql instances like Google Cloud or AWS won't let you install your own extensions. `pgbouncer-rr` was moderately difficult to configure given that I only wanted a single feature (rewriting) and none of the usual things pgbouncer is used for (connection pooling, load balancing).

In the end, I found a neat library [pgproto3](https://github.com/jackc/pgproto3) which implements the postgresql wire format and can be used to write both a client and server! I used this to write [an application](https://github.com/patientsknowbest/pg-rewrite-proxy) that can act as a proxy to a postgresql server and will rewrite the queries before forwarding them. The proxy approach seems more flexible than an extension as it can be bundled either on the application server, or on the database server, or somewhere in-between depending on the requirement.

At the moment this application only supports simple string replacement, but I hope to extend it with [LUA](https://www.lua.org/) support soon for arbitrary query rewriting.
