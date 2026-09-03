# @nebutra/cache

Public mirror for [@nebutra/cache](https://www.npmjs.com/package/%40nebutra%2Fcache) from [Nebutra/Nebutra-Sailor](https://github.com/Nebutra/Nebutra-Sailor/tree/main/packages/integrations/cache).

This repository is generated from the Nebutra Sailor monorepo. Package releases are cut from the monorepo and mirrored here for discovery, standalone cloning, and contribution intake.

- Canonical source: `packages/integrations/cache` in `Nebutra/Nebutra-Sailor`
- Package registry: npm and GitHub Packages
- Contributions: open issues or PRs here; maintainers port accepted changes back into the monorepo source package

---
Redis caching strategies for Upstash Redis.

## Installation

```bash
pnpm add @nebutra/cache
```

## Strategies

| Strategy      | Description                      |
| ------------- | -------------------------------- |
| `ttlCache`    | Standard TTL-based caching       |
| `lockCache`   | Distributed locks                |
| `stampede`    | Cache stampede prevention        |
| `lazyRefresh` | Background refresh before expiry |

## Setup

```bash
UPSTASH_REDIS_REST_URL=https://...upstash.io
UPSTASH_REDIS_REST_TOKEN=...
```

## Usage

### TTL Cache

```typescript
import { ttlCache } from "@nebutra/cache";

// Get or set with TTL
const data = await ttlCache.getOrSet(
  "user:123",
  async () => await fetchUser(123),
  { ttl: 3600 }, // 1 hour
);
```

### Distributed Lock

```typescript
import { lockCache } from "@nebutra/cache";

// Acquire lock
const lock = await lockCache.acquire("process:order:456", {
  ttl: 30000, // 30 seconds
});

try {
  await processOrder(456);
} finally {
  await lock.release();
}
```

### Stampede Protection

```typescript
import { stampedeCache } from "@nebutra/cache";

// Prevents multiple simultaneous cache rebuilds
const data = await stampedeCache.getOrSet(
  "expensive:query",
  async () => await expensiveQuery(),
  { ttl: 3600, lockTtl: 5000 },
);
```

### Lazy Refresh

```typescript
import { lazyRefresh } from "@nebutra/cache";

// Refresh in background before expiry
const data = await lazyRefresh.getOrSet(
  "api:data",
  async () => await fetchFromApi(),
  { ttl: 3600, refreshAt: 0.8 }, // Refresh at 80% of TTL
);
```

## Multi-tenancy

All cache keys are automatically prefixed with tenant ID:

```typescript
// Internally becomes: "tenant:org_123:user:456"
await ttlCache.get("user:456", { tenantId: "org_123" });
```

## Related

- [Rate limiting](../../platform/rate-limit/)
- [Upstash console](https://console.upstash.com)

## License

MIT
