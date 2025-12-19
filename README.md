# Vendure Dokploy Starter

This repository is a vanilla setup to deploy Vendure with Dokploy on a VPS:
* Vendure API
* Vendure Worker
* PostgreSQL
* Redis
* BullMQ

This repository is kept as close as possible to the vanilla setup created via `npx @vendure/create`. It only adds:
* Config for Redis and BullMQ
* A build pipeline to build the Vendure Docker image and push it to Docker Hub