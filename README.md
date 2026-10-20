# Bazaar API

First version of the Tibia Scout backend: scrapes the Tibia Bazaar (CipSoft), processes the data with a crawler and a worker, and serves it through a REST API. Designed for Railway with a local SQLite database.

> The current, containerised version is [bazaar-api-docker](https://github.com/luizgustavocs/bazaar-api-docker), which moved to Docker, Render and Turso after the Railway trial ended. This repository is kept to show the evolution of the architecture.

**Front end:** [tibia-scout](https://github.com/luizgustavocs/tibia-scout)

## Architecture

NPM Workspaces monorepo:

| App | Responsibility |
|---|---|
| `apps/api` | Fastify server that delivers the processed data to the front end |
| `apps/crawler` | Scraping engine that extracts raw data from the official website |
| `apps/worker` | Task orchestrator that manages persistence and updates |

## Tech stack

Node.js 20+ · TypeScript · Fastify 4 · Prisma · SQLite · BullMQ and Redis · node-cron

## Why Redis

SQLite stores the data; Redis is the message broker for BullMQ:
- **Asynchronous processing:** the API keeps responding while the crawler processes thousands of characters in the background.
- **Resilience:** if the application restarts, pending jobs remain in the queue.
- **Memory efficiency:** work is distributed in controlled chunks.

## Technical decisions

- `@fastify/cors` pinned to 8.x for compatibility with Fastify 4.
- SQLite as a single-file database: simple backups and no external database server in early stages.
- Heavy scraping scheduled with node-cron at 00:00, outside peak traffic.
- Server bound to `0.0.0.0:3333` so the hosting reverse proxy can route traffic to the container.

## API endpoints

| Method | Path | Description |
|---|---|---|
| GET | `/health` | Application and database health |
| GET | `/characters` | Processed Bazaar characters |
| GET | `/bazaar` | Active auction data and filters |

## Getting started

```bash
git clone https://github.com/luizgustavocs/bazaar-api.git
cd bazaar-api
npm install
npx prisma generate
npx prisma db push
npm run dev          # all services
npm run dev:api      # API only
npm run dev:crawler  # crawler and worker only
```

Environment variables (see `.env.example`): `PORT`, `HOST`, `DATABASE_URL`, `REDIS_HOST`, `REDIS_PORT`, `REDIS_PASSWORD`.

## Author and license

Luiz Gustavo Costa e Silva · [GitHub](https://github.com/luizgustavocs) · [LinkedIn](https://www.linkedin.com/in/luizgustavocs) · MIT License

<details>
<summary>Versão em português</summary>

### Bazaar API

Primeira versão do back-end do Tibia Scout: scraping do Bazaar do Tibia, processamento com crawler e worker e distribuição por API REST, pensada para o Railway com SQLite local. A versão atual, em container, é o [bazaar-api-docker](https://github.com/luizgustavocs/bazaar-api-docker).

**Destaques técnicos:** monorepo com NPM Workspaces, Fastify 4, Prisma, SQLite, BullMQ e Redis para processamento assíncrono e resiliente, e node-cron para atualizar os dados à meia-noite.

**Como executar:** `npm install`, `npx prisma generate`, `npx prisma db push`, configurar o `.env` e rodar `npm run dev`.
</details>
