# RateLimiter

A rate limiter built with Next.js and Redis. It implements four algorithms using atomic Lua scripts and includes a dashboard to load-test them.

## Features

- **4 algorithms:** fixed window, sliding window, token bucket and leaky bucket
- **Race-free:** each check-and-update runs as one atomic Lua script in Redis
- **Distributed:** state is shared in Redis, so limits hold across serverless instances
- **Fail open or closed:** configurable behavior when Redis is unreachable
- **Load-testing dashboard:** floods an endpoint and reports block rate and p50/p95/p99 latency

## Tech Stack

| Layer | Technology |
| --- | --- |
| Framework | Next.js 16 (App Router) |
| UI | React 19, Tailwind CSS 4 |
| Language | TypeScript 5 |
| Data store | Redis via Upstash (`@upstash/redis`) |
| Atomicity | Lua scripts run in Redis |

## Algorithms

| Algorithm | Endpoint | Trade-off |
| --- | --- | --- |
| Fixed window | `/api/fixed-window` | Cheapest to use but it allows bursts at the window edges |
| Sliding window | `/api/sliding-window` | Exact, but it stores every request in the window |
| Token bucket | `/api/token-bucket` | Allows short bursts and then a steady rate |
| Leaky bucket | `/api/leaky-bucket` | Drains at a constant rate to smooth traffic |

Each endpoint allows 10 requests per minute per user. Blocked requests get a `429`.
