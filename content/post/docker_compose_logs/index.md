---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "Docker Compose Tailing of Log Files"
subtitle: ""
summary: ""
authors:
- gliptak
tags:
- Docker
categories: []
date: 2022-04-23T01:50:53Z
lastmod: 2022-04-23T01:50:53Z
featured: false
draft: false

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
# Focal points: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight.
image:
  caption: ""
  focal_point: ""
  preview_only: false

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["internal-project"]` references `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
projects: []
---

## Docker Compose Logs

`docker compose logs` aggregates `stdout` of containers started with no out-of-the-box
capability for aggregating additional logs generated on container filesystems. 
Implementing [sidecar](https://kubernetes.io/docs/concepts/cluster-administration/logging/#using-a-sidecar-container-with-the-logging-agent)
allows tailing logs generated on container volumes.

## `docker-compose.yaml`

```yaml
services:
  counter:
    image: busybox
    command: |
      /bin/sh -c 'i=0; while true; do echo "$$(date) WARN $$i" >> /var/log/1.log; echo "$$(date) INFO $$i" >> /var/log/2.log; i=$$((i+1)); sleep 5; done'
    volumes:
    - log-volume:/var/log
  counter-log:
    image: busybox
    command: |
      /bin/sh -c 'tail -n+1 -F /var/log/*.log'
    volumes:
    - log-volume:/var/log

volumes:
  log-volume:
    name: counter-log-volume
```

## Running

```bash
➜ docker compose up
[+] Running 4/4
 ⠿ Network 202204_docker_default          Created                                                   0.1s
 ⠿ Volume "counter-log-volume"            Created                                                   0.0s
 ⠿ Container 202204_docker-counter-log-1  Created                                                   0.1s
 ⠿ Container 202204_docker-counter-1      Created                                                   0.1s
Attaching to 202204_docker-counter-1, 202204_docker-counter-log-1
202204_docker-counter-log-1  | ==> /var/log/1.log <==
202204_docker-counter-log-1  | Sat Apr 23 01:37:03 UTC 2022 WARN 0
202204_docker-counter-log-1  | 
202204_docker-counter-log-1  | ==> /var/log/2.log <==
202204_docker-counter-log-1  | Sat Apr 23 01:37:03 UTC 2022 INFO 0
202204_docker-counter-log-1  | 
202204_docker-counter-log-1  | ==> /var/log/1.log <==
202204_docker-counter-log-1  | Sat Apr 23 01:37:08 UTC 2022 WARN 1
202204_docker-counter-log-1  | 
202204_docker-counter-log-1  | ==> /var/log/2.log <==
202204_docker-counter-log-1  | Sat Apr 23 01:37:08 UTC 2022 INFO 1
^C
```

## Cleanup

```bash
docker compose down
docker volume rm counter-log-volume
```