# AniMaple Stream Server
This repository contains the files needed to build the stream server used by AniMaple. It builds a Docker image with [Nginx](https://nginx.org) and the [nginx-rtmp-module](https://github.com/arut/nginx-rtmp-module) 

## Requirements
- A working version of [Docker](https://docs.docker.com/get-docker/)

## To build
```bash
$ docker-compose build
```

## To run
```bash
$ docker-compose up -d
```

## Information about the configuration
- The included configuration should allow for sending multiple streams to the server and have multiple clients receiving the stream (`rtmp_auto_push` and an automatic number of worker processes are set as configuration options)
- It includes 10 different ingest applications, from 1-10 respectively. To stream to them, use `rtmp://<server-ip>/<number>`, and to watch the stream use `rtmp://<server-ip>/<number>/<streamkey>`

## Future inclusions
- [ ] An included authentication handler for the stream key to prevent malicious hijack
- [ ] ???