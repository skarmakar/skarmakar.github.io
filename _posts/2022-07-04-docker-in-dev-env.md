---
title: "A different way to use docker in the development environment"
date: 2022-07-04T00:00:00+00:00
author: Santanu Karmakar
layout: post
permalink: /different-way-to-use-docker-in-development-environment/
categories: Ruby on Rails
tags: [ruby-on-rails, docker, postgres, redis]
---

Over the years, I was not lucky enough to work on a single project for long :D. Working parallelly on multiple projects is fun, and a pain as well. Different projects use different versions of the software, and if we do not have the same version in the development environment, often we can fall into the famous trap of `It's working on my machine!`

For example, the most common, Postgres version! I would bet, it won't be the same in 2/3 projects we're working on at the same time. Well, can happen with Memcached, Redis, Elasticsearch, MongoDB, etc. So, what's the easiest solution? dockerize every project? well, probably does not make sense and is not feasible!

So, the easiest solution is, use docker containers in the development machine! We can run different versions of the same software using docker. Run the container, and configure the project to use the specific one we need, mostly host and port, and that's it!

A real example from my dev machine:

```console

  santanu@santanu:~$ docker ps --all
  CONTAINER ID   IMAGE                 COMMAND                  CREATED         STATUS                     PORTS                                       NAMES
  bf36a824da2d   redis:5.0             "docker-entrypoint.s…"   3 days ago      Up 36 hours                0.0.0.0:6379->6379/tcp, :::6379->6379/tcp   redis-5-0
  5329b682f7d9   redis:6.0             "docker-entrypoint.s…"   10 months ago   Up 36 hours                0.0.0.0:7001->6379/tcp, :::7001->6379/tcp   redis-6-0
  0df341ea9c63   redis                 "docker-entrypoint.s…"   4 weeks ago     Up 36 hours                0.0.0.0:7002->6379/tcp, :::7002->6379/tcp   redis-latest
  d179dded4ee3   memcached             "docker-entrypoint.s…"   11 days ago     Exited (0) 11 days ago                                                 memcached-1-6
  ce3c7da74dd6   elasticsearch:6.5.4   "/usr/local/bin/dock…"   4 weeks ago     Exited (143) 3 weeks ago                                               elasticsearch
  50475b95e4ec   postgis/postgis       "docker-entrypoint.s…"   3 months ago    Up 36 hours                5432/tcp                                    postgres-13.5-postgis
  bf734187e0d1   postgres:13.2         "docker-entrypoint.s…"   10 months ago   Up 36 hours                0.0.0.0:5435->5432/tcp, :::5435->5432/tcp   postgres-13-2
  74bff55cf283   postgres:9.5.6        "docker-entrypoint.s…"   10 months ago   Up 36 hours                0.0.0.0:5433->5432/tcp, :::5433->5432/tcp   postgres-9-5-2

```

Running the docker containers is very easy. Can read the tutorial from [docker hub](https://hub.docker.com/). You can create an account as well - no harm! For example, I am running postgres containers for 3 projects in different ports being shown in above example. Similarly, Redis. For running Redis, after downloading the Redis images

```console

  docker run --name redis-5-0 -d redis:5.0
  docker run --name redis-6-0 -p 7001:6379 -d redis:6.0
  docker run --name redis-latest -p 7002:6379 -d redis

```

and use different ports in different projects! 
