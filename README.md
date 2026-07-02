# redjis

A Redis server implementation in Node.js, built from scratch. Speaks the RESP protocol, supports replication, persistence, pub/sub, streams, sorted sets, geo commands, and transactions.

## Run

```sh
node app/main.js [--port 6379] [--dir /path] [--dbfilename dump.rdb] [--replicaof <host> <port>]
```

Connect with any standard Redis client or `redis-cli`.

## Implemented

**Core**
- RESP protocol encoding and decoding
- `PING`, `ECHO`, `INFO`
- `SET` / `GET` with optional TTL (`PX`, `EX`) and key expiry
- `KEYS` with pattern matching
- `TYPE`, `INCR`, `CONFIG GET`

**Lists**
- `RPUSH`, `LPUSH`, `LRANGE`, `LPOP`, `LLEN`
- `BLPOP` — blocking pop with timeout, unblocks on new push

**Streams**
- `XADD` with auto-generated stream IDs (`*`, partial IDs)
- `XRANGE`, `XREAD` — both blocking and non-blocking reads

**Sorted Sets** — backed by a skip list
- `ZADD`, `ZREM`, `ZRANK`, `ZRANGE`, `ZCARD`, `ZSCORE`

**Geo**
- `GEOADD`, `GEOPOS`, `GEODIST`, `GEOSEARCH`

**Pub/Sub**
- `SUBSCRIBE`, `UNSUBSCRIBE`, `PUBLISH`
- Subscriber mode enforcement — commands outside the allowed set are rejected

**Transactions**
- `MULTI`, `EXEC`, `DISCARD`
- Commands queued and executed atomically; errors inside a transaction do not abort the whole batch

**Replication**
- Master/replica roles via `--replicaof`
- Handshake via `REPLCONF` and `PSYNC`
- Command propagation from master to all connected replicas
- `WAIT` — blocks until N replicas have acknowledged a given offset
- Replica offset tracking and `REPLCONF GETACK` handling

**Persistence**
- RDB file parsing on startup — loads keys and TTLs from an existing dump

## Structure

```
app/
  main.js              entry point, command dispatch
  config.js            server config (port, role, dir, replication state)
  state/store.js       in-memory stores for strings, lists, streams, sorted sets
  handlers/            per-feature command handlers
  transcoder/          RESP encoder and decoder
  data_structures/     skip list, queue
  utils/               RDB parser, expiry logic, connection helpers
```
