# docker-janitor
This containers performs continuous cleanups on unused Docker resources on the host.

Basically it performs ```docker system prune --all``` from time to time.

## Usage
* To run it on a standalone host
  * ```docker run -v /var/run/docker.sock:/var/run/docker.sock getsoma/janitor```
* To run on all hosts of a Swarm Cluster
  * docker-compose.yml

```
version: '3.5'

services:
  janitor:
    image: getsoma/janitor
    build: .
    deploy:
      mode: global
    environment:
      - SLEEP_TIME=86400
      - UNUSED_TIME=24h
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock```
```
```docker stack deploy --compose-file docker-compose.yml janitor```

## ENV
* SLEEP_TIME: time in seconds between janitor script runs (default: 86400 - once a day)
* SKIP_RANDOM_BACKOFF: On the first run, the container will sleep randomly between 0 and SLEEP_TIME before entering the loop (random backoff) so that the hosts won't run this script at the same time, possibly causing unecessary global pressure on the cluster (default: false)
* UNUSED_TIME: min age of resource creation before being pruned (default: 24h).
